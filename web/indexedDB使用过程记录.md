# indexedDB使用过程记录

## 打开数据库

``` javascript
var request = window.indexedDB.open("MyTestDatabase");
```
IndexedDB 对象只有一个单一方法，open()， 当这个方法被调用时，打开名为 "MyTestDatabase"的数据库。 所有 IndexedDB 数据库都按照同样的 origin 进行存储, 所以 mozilla.com 可能有一个名为 "binky" 的数据库，同时 mozilla.org 也可以有一个完全不同的名为 "binky" 的数据库。如果该数据库不存在，则会被创建；如果已经存在，则被打开。

## 生成处理函数
``` javascript
    //数据库error事件被触发时
    request.onerror=function(){}
    //数据库success事件被触发时
    request.onsuccess=function(){}
    // 该事件仅在较新的浏览器中被实现
    request.onupgradeneeded = function(event) { 
   // 更新对象存储空间和索引 .... 
    };
```

## 构建数据库
|Key Path|Key Generator|Description|
|--|--|--|
|No|No|这种对象存储空间可以持有任意类型的值，甚至是像数字和字符串这种基本数据类型的值。每当我们想要增加一个新值的时候，必须提供一个单独的键参数。|
|Yes|No|这种对象存储空间只能持有 JavaScript 对象。这些对象必须具有一个和 key path 同名的属性。|
|No|Yes|这种对象存储空间可以持有任意类型的值。键会为我们自动生成，或者如果你想要使用一个特定键的话你可以提供一个单独的键参数。|
|Yes|Yes|这种对象存储空间只能持有 JavaScript 对象。通常一个键被生成的同时，生成的键的值被存储在对象中的一个和 key path 同名的属性中。然而，如果这样的一个属性已经存在的话，这个属性的值被用作键而不会生成一个新的键。|

### 创建一个对象存储空间
``` javascript
    var objectStore = db.createObjectStore("res_topo", { keyPath: "id" });
```
keyPaht:保证唯一性，不会创建两个相同keyPath的数据

### unique 索引
```javascript
    objectStore.createIndex("email", "email", { unique: true });
```

## 数据库操作
### 增加数据
* 数据库操作依赖事物
* db = request.result;
* db.transaction([操作的数据库对象存储空间的列表，空数组使用所有的储存空间],事物操作权限，默认只读，读写为readwrite)
* 获取事物中的对象存储空间，然后通过add()增加数据
```javascript
    // 当所有的数据都被增加到数据库时执行一些操作
    transaction.oncomplete = function(event) {
      alert("All done!");
    };

    transaction.onerror = function(event) {
      // 不要忘记进行错误处理！
    };

    var objectStore = transaction.objectStore("customers");
    for (var i in customerData) {
      var request = objectStore.add(customerData[i]);
      request.onsuccess = function(event) {
        // event.target.result == customerData[i].ssn
      };
    }
```
* 如果你正在试图修改一个现有条目，或者你并不关心是否有一个同样的条目已经存在，使用 put()函数

### 删除数据
```javascript
    var request = db.transaction(["customers"], "readwrite")
                    .objectStore("customers")
                    .delete("444-44-4444");
    request.onsuccess = function(event) {
      // 删除数据成功！
    };
```

### 获取数据
#### get()
```javascript
    var transaction = db.transaction(["customers"]);
    var objectStore = transaction.objectStore("customers");
    var request = objectStore.get("444-44-4444");
    request.onerror = function(event) {
      // 错误处理!
    };
    request.onsuccess = function(event) {
      // 对 request.result 做些操作！
      alert("Name for SSN 444-44-4444 is " + request.result.name);
    };
```

```javascript
    db.transaction("customers").objectStore("customers").get("444-44-4444").onsuccess = function(event) {
      alert("Name for SSN 444-44-4444 is " + event.target.result.name);
    };
```
#### openCursor()
``` javascript
    var objectStore = db.transaction("customers").objectStore("customers");

    objectStore.openCursor().onsuccess = function(event) {
      var cursor = event.target.result;
      if (cursor) {
        alert("Name for SSN " + cursor.key + " is " + cursor.value.name);
        cursor.continue();
      }
      else {
        alert("No more entries!");
      }
    };
```


# 使用ydn.db
* [github](https://github.com/yathit/ydn-db)
* [官网](https://yathit.github.io/ydn-db/)

