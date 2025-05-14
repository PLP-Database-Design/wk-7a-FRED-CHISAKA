# 📝 Assignment: Database Design and Normalization

## 🎯 **Learning Objectives**
* 🛠️ **Understand the principles of good database design** and **normalization**.
* 💡 **Apply normalization techniques** to improve database structure and efficiency.
* 🔍 **Learn First, Second, and Third Normal Forms** (1NF, 2NF, 3NF) to eliminate redundancy and optimize data storage.

---

## 📋 **What You'll Need**
* 💻 A computer with internet access.
* ✍️ A code editor (e.g., Visual Studio Code).
* 🖥️ MySQL Workbench or another SQL database environment.

---


## 📝 Submission Instructions  
📂 Write all your SQL queries in the **answers.sql** file.  
✍️ Answer each question concisely and make sure your queries are clear and correct.  
🗣️ Structure your responses clearly, and use comments if necessary to explain your approach.

--- 

## 📚 Assignment Questions

---

### Question 1 Achieving 1NF (First Normal Form) 🛠️
Task:
- You are given the following table **ProductDetail**:

| OrderID | CustomerName  | Products                        |
|---------|---------------|---------------------------------|
| 101     | John Doe      | Laptop, Mouse                   |
| 102     | Jane Smith    | Tablet, Keyboard, Mouse         |
| 103     | Emily Clark   | Phone                           |


- In the table above, the **Products column** contains multiple values, which violates **1NF**.
- **Write an SQL query** to transform this table into **1NF**, ensuring that each row represents a single product for an order

--- 

### Question 2 Achieving 2NF (Second Normal Form) 🧩

- You are given the following table **OrderDetails**, which is already in **1NF** but still contains partial dependencies:

| OrderID | CustomerName  | Product      | Quantity |
|---------|---------------|--------------|----------|
| 101     | John Doe      | Laptop       | 2        |
| 101     | John Doe      | Mouse        | 1        |
| 102     | Jane Smith    | Tablet       | 3        |
| 102     | Jane Smith    | Keyboard     | 1        |
| 102     | Jane Smith    | Mouse        | 2        |
| 103     | Emily Clark   | Phone        | 1        |

- In the table above, the **CustomerName** column depends on **OrderID** (a partial dependency), which violates **2NF**. 

- Write an SQL query to transform this table into **2NF** by removing partial dependencies. Ensure that each non-key column fully depends on the entire primary key.

---
Good luck 🚀

You said:
Question 1 Achieving 1NF (First Normal Form) 🛠️
Task:

You are given the following table ProductDetail:
OrderID	CustomerName	Products
101	John Doe	Laptop, Mouse
102	Jane Smith	Tablet, Keyboard, Mouse
103	Emily Clark	Phone
In the table above, the Products column contains multiple values, which violates 1NF.
Write an SQL query to transform this table into 1NF, ensuring that each row represents a single product for an order
Question 2 Achieving 2NF (Second Normal Form) 🧩
You are given the following table OrderDetails, which is already in 1NF but still contains partial dependencies:
OrderID	CustomerName	Product	Quantity
101	John Doe	Laptop	2
101	John Doe	Mouse	1
102	Jane Smith	Tablet	3
102	Jane Smith	Keyboard	1
102	Jane Smith	Mouse	2
103	Emily Clark	Phone	1
In the table above, the CustomerName column depends on OrderID (a partial dependency), which violates 2NF.

Write an SQL query to transform this table into 2NF by removing partial dependencies. Ensure that each non-key column fully depends on the entire primary key.


ChatGPT said:
Here’s how to solve each normalization task using SQL:

✅ Question 1: Achieving 1NF (First Normal Form)
Problem: The Products column contains multiple values, violating 1NF.

Goal: Split the comma-separated values so each product appears in its own row.

✅ 1NF SQL Solution (Using Common Table Expressions & STRING_SPLIT in SQL Server):
sql
Copy
Edit
-- Assuming you're using SQL Server
-- Step 1: Create the original table
CREATE TABLE ProductDetail (
    OrderID INT,
    CustomerName VARCHAR(100),
    Products VARCHAR(255)
);

-- Step 2: Insert sample data
INSERT INTO ProductDetail (OrderID, CustomerName, Products)
VALUES 
(101, 'John Doe', 'Laptop, Mouse'),
(102, 'Jane Smith', 'Tablet, Keyboard, Mouse'),
(103, 'Emily Clark', 'Phone');

-- Step 3: Normalize to 1NF using STRING_SPLIT
SELECT 
    OrderID,
    CustomerName,
    LTRIM(RTRIM(value)) AS Product
FROM 
    ProductDetail
CROSS APPLY 
    STRING_SPLIT(Products, ',');
🔍 This splits each product into separate rows — satisfying 1NF.

✅ Question 2: Achieving 2NF (Second Normal Form)
Problem: The CustomerName depends only on OrderID, not on the full composite key (OrderID, Product), which violates 2NF.

Goal: Separate the CustomerName into a separate table so each non-key attribute depends on the full primary key.

✅ 2NF SQL Solution: Split into Two Tables
a) Orders Table – OrderID → CustomerName
sql
Copy
Edit
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerName VARCHAR(100)
);

-- Insert distinct orders
INSERT INTO Orders (OrderID, CustomerName)
SELECT DISTINCT OrderID, CustomerName
FROM OrderDetails;
b) OrderItems Table – (OrderID, Product) → Quantity
sql
Copy
Edit
CREATE TABLE OrderItems (
    OrderID INT,
    Product VARCHAR(100),
    Quantity INT,
    PRIMARY KEY (OrderID, Product),
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID)
);

-- Insert product items without CustomerName
INSERT INTO OrderItems (OrderID, Product, Quantity)
SELECT OrderID, Product, Quantity
FROM OrderDetails;
