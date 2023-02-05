# 1. สร้างโปรเจค
จะใช้ command หรือ visual studio code ก็ได้
```cmd
d:\> cd Code
d:\Code> mkdir AppTest
d:\Code> cd AppTest
d:\Code\AppTest> code .
d:\Code\AppTest> npm init -y
```
จะได้ไฟล์ package.json ในโฟลเดอร์
d:\Code\AppTest\package.json:

```javascript
{
  "name": "AppTest",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

# 2. เพิ่ม Module ใน Node Project
```npm
d:\Code\AppTest> npm install express ejs sqlite3
```
ตัวอย่างไฟล์ package.json หลังจาก ติดตั้ง Module ซึ่งของนักศึกษาอาจได้ Version อื่นซึ่งสามารถใช้ได้
```javascript
{
  "name": "AppTest",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "ejs": "^3.1.8",
    "express": "^4.18.2",
    "sqlite3": "^5.1.4"
  }
}
  ```

# 3.สร้างไฟล์ index.js
```javascript
const express = require("express");

const app = express();

app.listen(3000, () => {
    {
        console.log("Server started (http://localhost:3000/) !");
    }});

app.get("/", (req, res) => {
    {
        res.send("Hello world...");
    }
});
```
ทดลอง run พิมพ์ node index.js

# 4. สร้าง EJS views ใน 
4.1 "views/_header.ejs"
```html
<!doctype html>
<html lang="fr">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <title>AppTest</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css">
</head>

<body>

  <div class="container">

    <nav class="navbar navbar-expand-lg navbar-light bg-light">
      <a class="navbar-brand" href="/">AppTest</a>
      <ul class="navbar-nav mr-auto">
        <li class="nav-item">
          <a class="nav-link" href="/data">Data</a>
        </li>
        <li class="nav-item">
          <a class="nav-link" href="/books">Books</a>
        </li>
      </ul>
    </nav>
   ``` 
  
4.2 "views/index.ejs"
  ```html
<%- include("_header") -%>

<h1>Hello world...</h1>

<%- include("_footer") -%>
  ```
  
4.3 "views/_footer.ejs"
```html
<footer>
      <p>&copy; 2023 - AppTest</p>
    </footer>

  </div>

</body>

</html>
  ```
# 5. ใช้ EJS ใน index.js
5.1 เพิ่มโด้ดใน index.js ในบรรทัดก่อน listen
```javascript
app.set("view engine", "ejs");
app.set("views", __dirname + "/views");

แก้ไข code บรรทัด Hello world เป็น
app.get("/", (req, res) => {
    {
        // res.send("Hello world...");
        res.render("index");
    }});
```
ทดลอง run พิมพ์ node index.js

5.2 ทดลองส่งข้อมูลไปให้ Views
สร้างไฟล์ data.ejs ใน views โฟลเดอร์
```javascript
<%- include("_header") -%>

<h1><%= model.title %></h1>

<ul>

  <% for (let i = 0; i < model.items.length; i++) { %>
    <li><%= model.items[i] %></li>
  <% } %>

</ul>

<%- include("_footer") -%>
```

5.3 ใน index.js ทดลองส่งข้อมูลไปยัง data.ejs
```javascript
app.get("/data", (req, res) => {
  const test = {
    title: "Test",
    items: ["one", "two", "three"]
  };
  res.render("data", { model: test });
});
```
ทดลอง run พิมพ์ node index.js

<<< สรุปไฟล์ index.js ในตอนนี้>>>
```javascript
const express = require("express");
const app = express();
const path = require("path");

app.set("view engine", "ejs");
app.set("views", path.join(__dirname, "views"));

app.listen(3000, () => {
    {
        console.log("Server started (http://localhost:3000/) !");
    }
});

app.get("/", (req, res) => {
    {
        // res.send("Hello world...");
        res.render("index");
    }
});

app.get("/data", (req, res) => {
    const test = {
        title: "Test",
        items: ["one", "two", "three"]
    };
    res.render("data", { model: test });
});
```


#6. ใช้งานฐานข้อมูล sqlite 
6.1 เรียกใช้ module
```javascript
const sqlite3 = require("sqlite3").verbose();
```
สร้าง โฟลเดอร์ data เพื่อเก็บฐานข้อมูล

6.2 เชื่อมต่อฐานข้อมูล
```javascript
const db_name = path.join(__dirname, "data", "apptest.db");
const db = new sqlite3.Database(db_name, err => {
  if (err) {
    return console.error(err.message);
  }
  console.log("Successful connection to the database 'apptest.db'");
});
```
6.3 สร้างตาราง
```sql
const sql_create = `CREATE TABLE IF NOT EXISTS Books (
  Book_ID INTEGER PRIMARY KEY AUTOINCREMENT,
  Title VARCHAR(100) NOT NULL,
  Author VARCHAR(100) NOT NULL,
  Comments TEXT
);`;
```

```javascript
db.run(sql_create, err => {
  if (err) {
    return console.error(err.message);
  }
  console.log("Successful creation of the 'Books' table");
  
  // เพิ่มข้อมูลในตาราง
  const sql_insert = `INSERT INTO Books (Book_ID, Title, Author, Comments) VALUES
  (1, 'Somchai', 'Somchai', 'Programming'),
  (2, 'Somsri', 'Somsri', 'Coding'),
  (3, 'Somjai', 'Somjai', 'Network');`;
  db.run(sql_insert, err => {
    if (err) {
      return console.error(err.message);
    }
    console.log("Successful creation of 3 books");
    // Database seeding
    const sql_insert = `INSERT INTO Books (Book_ID, Title, Author, Comments) VALUES
    (1, 'Somchai', 'Somchai', 'Programming'),
    (2, 'Somsri', 'Somsri', 'Coding'),
    (3, 'Somjai', 'Somjai', 'Network');`;
        db.run(sql_insert, err => {
            if (err) {
                return console.error(err.message);
            }
            console.log("Successful creation of 3 books");
        });
      });
    });
```

6.4 แสดงข้อมูล
6.4.1 สร้างไฟล์ views books.ejs ในโฟลเดอร์ views
```html
<%- include("_header") -%>

<h1>List of books</h1>

<ul>

  <% for (const book of model) { %>
    <li>
      <%= book.Title %>
      <em>(<%= book.Author %>)</em>
    </li>
  <% } %>

</ul>

<%- include("_footer") -%>
```

6.4.2 เรียกใช้ ใน index.js
```javascript
app.get("/books", (req, res) => {
  const sql = "SELECT * FROM Books ORDER BY Title"
  db.all(sql, [], (err, rows) => {
    if (err) {
      return console.error(err.message);
    }
    res.render("books", { model: rows });
  });
});
```
ทดลอง run node index.js

คลิกเลือกเมนู books

ref:
ดัดแปลงจาก
https://blog.pagesd.info/2019/10/08/crud-with-express-sqlite-10-steps/
