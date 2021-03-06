# A02-1 新增模組-使用VALUES

### 測試方式
```
node main.js
```


### 執行結果
![GitHub Logo](/imgs/results02-1.jpg)


### 檔案放置方式
```
|__ main.js
|   
|__ <utility>
        |__ asyncDB.js    
        |__ addProduct.js
```

### 假設
```
(1) 已安裝Node.js
(2) 已加載MySQL外掛, npm install mysql --save
(3) 已安裝MySQL
(4) 已在MySQL中安裝north資料庫    
```


### (1) asyncDB.js

``` js
'use strict';

//引用mysql模組
var mysql = require('mysql');

//建立資料庫連接池
var pool  = mysql.createPool({
    user: 'root',
    password: 'mysql',
    host: '127.0.0.1',
    database: 'north'     
});

//產生可同步執行query物件的函式
function query(sql, value) {
    return new Promise((resolve, reject) => {
        pool.query(sql, value, function (error, results, fields) {
            if (error){
                reject(error);
            }else{
                resolve(results);
            }
        });
    });
}

//匯出
module.exports = query;
```



### (2) addProduct.js  
``` js
'use strict';

//引用操作資料庫的物件
const query = require('./asyncDB');

//----------------------------------
// 新增商品
//----------------------------------
var addProduct = async function(proNo, proName, price){
    var result;

    await query('INSERT INTO product (proNo, proName, price) VALUES (?, ?, ?)', [proNo, proName, price])
        .then((data) => {
            result = 0;  
        }, (error) => {
            result = -1;
        });
		
    return result;
}
//----------------------------------

//匯出
module.exports = addProduct;
```


### (3) main.js
``` js
'use strict';

//引用函式
const addProduct = require('./utility/addProduct');

//執行函式
addProduct('P200', '凍豆腐', 50).then(d => {
    if(d==0){
        console.log('新增成功');        
    }else{
        console.log('新增失敗');
    }
})
```
