---
title: "Data Relationships and Database Creation for Ecommerce"
seoTitle: "Ecommerce Database: Optimal Structure & Relationships"
seoDescription: "Build a fundamental ecommerce database: Organize products, orders & customers smartly. Learn vital techniques for efficient data management"
datePublished: Wed Aug 16 2023 05:16:25 GMT+0000 (Coordinated Universal Time)
cuid: cllda5fv4000609mfh8rgeb5e
slug: data-relationships-and-database-creation-for-ecommerce
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1692009484984/1fd7c7e6-0f02-49bc-bb8b-bcf52405bb16.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1692162648534/f7b0b909-4a44-46d3-9ff9-335499801abf.jpeg
tags: ecommerce, databases, ecommerce-development, ecommerce-website-development

---

Designing a database for an e-commerce website involves structuring the data in a way that efficiently represents products, customers, orders, payments, and other relevant information. Below is an example of an e-commerce database design:

### What Is Relationship In Database?

Data relationships in a database define how different tables are connected or related to each other. These relationships enable you to retrieve and manipulate data from multiple tables as a cohesive unit. In an eCommerce database, understanding and implementing data relationships is crucial for managing various aspects of the platform

1. **One-to-Many Relationship:** This is a common relationship where one record in the parent table is associated with multiple records in the child table
    
2. **Many-to-One Relationship:** This is the reverse of a one-to-many relationship. Many records in the child table are associated with a single record in the parent table.
    
3. **Many-to-Many Relationship:** In this relationship, multiple records in one table are associated with multiple records in another table
    

## Creating an eCommerce database, defining tables, and performing select and insert operations using MySQL

### **Create a database**

```sql
CREATE DATABASE ecommercedb;
```

### **Core Tables for eCommerce Database Design**

**Users**

* Stores user information such as UserID, username, email, password, address, and phone.
    
* Enables user authentication and personalized experiences.
    

**Categories**

* Stores different product categories.
    
* Helps categorize products and enables users to browse by category wise.
    

**Products**

* Contains product information like ProductID, product name, description, price, stock quantity, and the associated CategoryID.
    
* Represents the products available for sale.
    

**Orders**

* Tracks order details such as OrderID, UserID, order date, and total amount.
    
* Represents individual orders placed by users.
    

**OrderItems**

* Contains information about items within each order, including OrderItemID, OrderID, ProductID, quantity, and subtotal.
    
* Represents the items purchased within each order.
    

**Payments**

* Stores payment-related information for orders
    
* Based on the status we can track the orders and generate the reports
    

**Reviews**

* Stores product reviews submitted by users, including ReviewID, ProductID, UserID, rating, review text, and review date.
    
* Enables users to share their opinions about products.
    

**Return and Refund**

* It involves several steps, including recording return requests, processing refunds, updating inventory, and keeping track of the overall process
    

### **User Creation**

The `UserID` is set as the primary key with auto-increment, ensuring each user has a unique identifier. Make sure to replace data types, constraints, and field lengths according to your application's requirements

```sql
CREATE TABLE Users (
    UserID INT AUTO_INCREMENT PRIMARY KEY,
    Username VARCHAR(50) NOT NULL,
    Email VARCHAR(100) NOT NULL,
    Password VARCHAR(100) NOT NULL,
    Address VARCHAR(200),
    Phone VARCHAR(20)
);

INSERT INTO Users (Username, Email, Password, Address, Phone)
VALUES ('blog_user', 'blog@example.com', 'hashed_password', 'Test St', '123456');
```

### **Category Creation**

```sql
CREATE TABLE Categories (
    CategoryID INT AUTO_INCREMENT PRIMARY KEY,
    CategoryName VARCHAR(50) NOT NULL,
    ParentCategoryID INT,
    FOREIGN KEY (ParentCategoryID) REFERENCES Categories(CategoryID)
);
```

```sql
INSERT INTO Categories (CategoryName) VALUES ('Clothing');

-- Assign Clothing as the parent of subcategories
INSERT INTO Categories (CategoryName, ParentCategoryID) VALUES ('Men', 2);
INSERT INTO Categories (CategoryName, ParentCategoryID) VALUES ('Women', 2);
```

In this structure, The `ParentCategoryID` column references the `CategoryID` in the same table, creating a relationship between parent and child categories. Here's how you could insert data to establish a parent-child relationship:

### **Product Creation**

```sql
CREATE TABLE Products (
    ProductID INT AUTO_INCREMENT PRIMARY KEY,
    ProductName VARCHAR(100) NOT NULL,
    Description TEXT,
    Price DECIMAL(10, 2) NOT NULL,
    Quantity INT NOT NULL,
    CategoryID INT,
    FOREIGN KEY (CategoryID) REFERENCES Categories(CategoryID)
);
```

The `CategoryID` column is a foreign key that references the `CategoryID` in the Categories table, establishing a relationship between `products` and `categories`

```sql
INSERT INTO Products (ProductName, Description, Price, StockQuantity, CategoryID)
VALUES ('T-Shirt', 'Comfortable and stylish cotton t-shirt.', 299.99, 200, 3);

INSERT INTO Products (ProductName, Description, Price, StockQuantity, CategoryID)
VALUES ('Fiction Novel', 'Bestselling fiction book by a renowned author.', 399.99, 150, 6);
```

### Order Creation

```sql
CREATE TABLE Orders (
    OrderID INT AUTO_INCREMENT PRIMARY KEY,
    UserID INT,
    OrderDate TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    TotalAmount DECIMAL(10, 2) NOT NULL,
    PaymentStatus ENUM('Pending', 'Paid', 'Cancelled') DEFAULT 'Pending',
    ShippingAddress VARCHAR(200),
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);
```

the `UserID` column is a foreign key that references the `UserID` in the `Users` table, establishing a relationship between orders and users. The PaymentStatus column represents the status of payment for the order

```sql
INSERT INTO Orders (UserID, TotalAmount, PaymentStatus, ShippingAddress)
VALUES (1, 299.99, 'Paid', 'Main St, City, Country');

INSERT INTO Orders (UserID, TotalAmount, PaymentStatus, ShippingAddress)
VALUES (2, 4299.99, 'Pending', 'Test St, Town, Country');
```

### Order Item Creation

```sql
CREATE TABLE OrderItems (
    OrderItemID INT AUTO_INCREMENT PRIMARY KEY,
    OrderID INT,
    ProductID INT,
    Quantity INT NOT NULL,
    Subtotal DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID),
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID)
);
```

```sql
-- Inserting sample order items
INSERT INTO OrderItems (OrderID, ProductID, Quantity, Subtotal)
VALUES (1, 1, 1, 299.99);

INSERT INTO OrderItems (OrderID, ProductID, Quantity, Subtotal)
VALUES (2, 2, 1, 4299.99);
```

`The orders` table stores general order information, while the `OrderItems` table stores details about the items within each order. The `OrderID` column in the OrderItems table establishes a relationship with the corresponding order in the `Orders` table, and the `ProductID` column links to the products in the Products table

### Payment Creation

```sql
CREATE TABLE Payments (
    PaymentID INT AUTO_INCREMENT PRIMARY KEY,
    OrderID INT,
    PaymentDate TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PaymentAmount DECIMAL(10, 2) NOT NULL,
    PaymentMethod VARCHAR(50),
    TransactionID VARCHAR(100),
    Status ENUM('Pending', 'Completed', 'Failed') DEFAULT 'Pending',
    FOREIGN KEY (OrderID) REFERENCES Orders(OrderID)
);
```

the `Payments` table stores payment-related information for each order. The `OrderID` column establishes a relationship with the corresponding order in the `Orders` table.

```sql
-- Inserting sample payment data
INSERT INTO Payments (OrderID, PaymentAmount, PaymentMethod, TransactionID, Status)
VALUES (1, 299.99, 'Debit Card', '123456789', 'Completed');
```

### Review Table

```sql
CREATE TABLE Reviews (
    ReviewID INT AUTO_INCREMENT PRIMARY KEY,
    ProductID INT,
    UserID INT,
    Rating INT NOT NULL,
    ReviewText TEXT,
    ReviewDate TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID),
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);
```

The `Reviews` table stores product reviews submitted by users. The `ProductID` column establishes a relationship with the corresponding product in the `Products` table, and the `UserID` column establishes a relationship with the corresponding user in the `Users` table.

**Sample Insert format:**

```sql
-- Inserting sample product reviews
INSERT INTO Reviews (ProductID, UserID, Rating, ReviewText)
VALUES (1, 1, 5, 'Great product');

INSERT INTO Reviews (ProductID, UserID, Rating, ReviewText)
VALUES (2, 2, 4, 'Good Product');
```

### Return Creation

```sql
CREATE TABLE Returns (
    return_id INT PRIMARY KEY,
    order_id INT,
    return_reason TEXT,
    return_date DATETIME,
    status VARCHAR(50),
    FOREIGN KEY (order_id) REFERENCES Orders(order_id)
);
```

Customer initiates a return, an entry is created in the Returns table with details like the order ID, return reason, and status ("Pending")

```sql
-- Insert a return request
INSERT INTO Returns (order_id, return_reason, return_date, status)
VALUES (1, 'Defect Product', NOW(), 'Pending');

-- Approve the return
UPDATE Returns SET status = 'Approved' WHERE return_id = 1;
```

### Refund Creation

```sql
CREATE TABLE Refunds (
    refund_id INT PRIMARY KEY,
    return_id INT,
    refund_date DATETIME,
    refund_amount DECIMAL(10, 2),
    FOREIGN KEY (return_id) REFERENCES Returns(return_id)
);
```

If the return is approved, a refund entry is created in the `refunds` table and the refund amount is calculated based on the order items' prices

```sql
-- Calculate refund amount
-- Assuming order ID 1 had a total of 299.99
INSERT INTO Refunds (return_id, refund_date, refund_amount)
VALUES (1, NOW(), 299.99);

-- Update inventory for returned item
UPDATE Products
SET stock_quantity = stock_quantity + (SELECT quantity FROM Order_Items WHERE order_item_id = 1)
WHERE product_id = (SELECT product_id FROM Order_Items WHERE order_item_id = 1);

-- Update order status
UPDATE Orders SET status = 'Returned' WHERE order_id = 1;
```

In the above example, inventory in the `Products` table is updated, incrementing the stock quantity for returned items.

The order status in the `Orders` table might be updated to "Returned" and then further to "Refunded" once the refund is processed

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">This example demonstrates a simplified e-commerce database design. Depending on your specific requirements, you might need to add more attributes, tables, or relationships. Additionally, remember to implement proper indexing, data validation, and security measures to ensure the database's efficiency and integrity</div>
</div>