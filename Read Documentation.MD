# **FastFoodX Database Design Documentation**

## **1. Gathering Requirements**

The first stage in database design involves understanding the system's needs. For FastFoodX, the online food ordering and delivery platform, the following requirements were identified:

### **Functional Requirements:**

- User authentication and role-based access (Admin, Customer, Restaurant Owner, Delivery Agent)
- Customers can browse restaurant menus and place orders
- Restaurants manage their menus and receive orders
- Payment processing through multiple methods
- Delivery agents manage deliveries and update order status
- Customers can leave reviews for restaurants
- Promotions and discount codes for orders
- System-generated reports for analysis

### **Non-Functional Requirements:**

- High performance and scalability
- Secure storage of sensitive data (hashed passwords, transaction IDs)
- Data consistency and integrity
- Support for multiple payment gateways
- User-friendly experience

---

## **2. DBMS Selection**

### **Database Management System (DBMS):**

- **Chosen DBMS:** SQL Server
- **Reasons for selection:**
  - Supports relational data model and complex queries
  - Ensures ACID compliance for transactions
  - Provides scalability and security features
  - Seamless integration with ASP.NET Core

---

## **3. Analysis**

### **Entities and Attributes Identified:**

- **Users**: Stores general information for all system users
- **Customers**: Subset of users who place orders
- **Restaurants**: Businesses offering food items
- **Menus**: Contains menu items for restaurants
- **Orders**: Tracks customer orders
- **OrderItems**: Links orders with menu items
- **Payments**: Stores transaction details
- **DeliveryAgents**: Users responsible for order deliveries
- **Deliveries**: Tracks order delivery details
- **Reviews**: Customer feedback on restaurants
- **Reports**: System-generated analytics
- **Promotions**: Discounts available for orders

### **Relationships Defined:**

- One-to-One (1:1)
  - Users - Customers
  - Users - Delivery Agents
  - Users - Restaurant Owners
  - Orders - Payments
  - Orders - Deliveries
- One-to-Many (1\:M)
  - Restaurants - Menus
  - Customers - Orders
  - Orders - OrderItems
  - Deliveries - Delivery Agents
  - Restaurants - Reviews
  - Users - Reports
- Many-to-One (M:1)
  - Orders - Promotions
- Many-to-Many (M\:M) (Handled via junction tables)
  - Orders - Menu Items

---

## **4. Logical Design**

The logical design represents the structured database schema:

### **Entities and Primary Keys:**

- `Users(UserID)`
- `Customers(CustomerID)`
- `Restaurants(RestaurantID)`
- `Menus(MenuID)`
- `Orders(OrderID)`
- `OrderItems(OrderItemID)`
- `Payments(PaymentID)`
- `DeliveryAgents(AgentID)`
- `Deliveries(DeliveryID)`
- `Reviews(ReviewID)`
- `Reports(ReportID)`
- `Promotions(PromoID)`

### **Foreign Keys and Relationships:**

- `Customers.UserID` → `Users.UserID`
- `Restaurants.OwnerID` → `Users.UserID`
- `Menus.RestaurantID` → `Restaurants.RestaurantID`
- `Orders.CustomerID` → `Customers.CustomerID`
- `OrderItems.OrderID` → `Orders.OrderID`
- `OrderItems.MenuID` → `Menus.MenuID`
- `Payments.OrderID` → `Orders.OrderID`
- `Deliveries.OrderID` → `Orders.OrderID`
- `Deliveries.AgentID` → `DeliveryAgents.AgentID`
- `Reviews.CustomerID` → `Customers.CustomerID`
- `Reviews.RestaurantID` → `Restaurants.RestaurantID`
- `Reports.GeneratedBy` → `Users.UserID`
- `Orders.PromoID` → `Promotions.PromoID`

---

## **5. Physical Design**

The physical design involves implementing data types, indexes, constraints, and optimizations:

### **Data Types Selection:**

- `VARCHAR(100)` for names and emails
- `VARCHAR(255)` for addresses and descriptions
- `DECIMAL(10,2)` for monetary values
- `DATETIME` for timestamps
- `INT` for primary and foreign keys

### **Indexes:**

- Primary keys indexed by default
- Additional indexes on `Email` (Users), `TransactionID` (Payments), and `OrderDate` (Orders) for faster lookups

### **Constraints:**

- **Primary Keys** ensure uniqueness
- **Foreign Keys** enforce referential integrity
- **NOT NULL** constraints ensure required data is present
- **CHECK** constraints for valid data ranges (e.g., `Rating` between 1-5)

---

## **6. Creation & Implementation**

### **SQL Schema Implementation:**

```sql
CREATE TABLE Users (
    UserID INT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Email VARCHAR(100) UNIQUE NOT NULL,
    HashedPassword VARCHAR(255) NOT NULL,
    Role VARCHAR(50) NOT NULL,
    Phone VARCHAR(20)
);

CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    UserID INT UNIQUE NOT NULL,
    Address VARCHAR(255) NOT NULL,
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);

CREATE TABLE DeliveryAgents (
    AgentID INT PRIMARY KEY,
    UserID INT UNIQUE NOT NULL,
    IsAvailable BIT NOT NULL,
    CurrentLocation VARCHAR(255),
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);

CREATE TABLE Restaurants (
    RestaurantID INT PRIMARY KEY,
    Name VARCHAR(100) NOT NULL,
    Address VARCHAR(255) NOT NULL,
    Phone VARCHAR(20) NOT NULL,
    OwnerID INT UNIQUE NOT NULL,
    FOREIGN KEY (OwnerID) REFERENCES Users(UserID)
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    OrderDate DATETIME NOT NULL,
    TotalAmount DECIMAL(10,2) NOT NULL,
    Status VARCHAR(50) NOT NULL,
    CustomerID INT NOT NULL,
    PromoID INT,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID),
    FOREIGN KEY (PromoID) REFERENCES Promotions(PromoID)
);
```

---

## **Conclusion**

This database design ensures an efficient, scalable, and secure structure for FastFoodX. By following best practices in relational database design, the system maintains data integrity, supports complex queries, and allows future enhancements.

---

**Contributors:**

- Mohamed Hamed



