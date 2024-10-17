Our project is "Data Warehouse Design for an E-Commerce Site", the main focus is on the architecture, data models (schema design), and ETL process. However, a basic implementation using Python, SQL (for the warehouse), and some sample data will be beneficial for demonstrating the functionality.

Here is the source code for various components of the project:

1. ETL Process:
In this step we extract data from the source, transforming it, and loading it into the data warehouse.

Python Code for ETL Process:

import pandas as pd
import sqlite3

# Step 1: Extract
def extract_data():
    # Sample data representing e-commerce sales
    data = {
        'order_id': [1, 2, 3, 4],
        'user_id': [101, 102, 103, 101],
        'product_id': [1001, 1002, 1003, 1001],
        'quantity': [1, 2, 1, 1],
        'price': [500, 1500, 1200, 500],
        'order_date': ['2024-10-01', '2024-10-02', '2024-10-03', '2024-10-01']
    }
    df = pd.DataFrame(data)
    return df

# Step 2: Transform
def transform_data(df):
    # Example of a simple transformation: Adding total amount column
    df['total_amount'] = df['quantity'] * df['price']
    return df

# Step 3: Load
def load_data(df):
    # Connect to SQLite database (can be replaced with other DBs like MySQL)
    conn = sqlite3.connect('ecommerce_warehouse.db')
    
    # Load the data into the 'orders' table
    df.to_sql('orders', conn, if_exists='replace', index=False)
    conn.close()
    print("Data loaded into the warehouse!")

# Running the ETL process
data = extract_data()
transformed_data = transform_data(data)
load_data(transformed_data)

Explanation:
Extract: Simulated the extraction of e-commerce sales data using a dictionary.
Transform: Added a transformation step by calculating the total amount.
Load: The data is loaded into an SQLite database (which simulates a data warehouse). This can be replaced with any real database like MySQL or PostgreSQL.


2. Schema Design:
For the e-commerce data warehouse, we make tables for storing facts (transactions) and dimensions (product, user, etc.).

SQL Code for Creating Tables:
sql

-- Creating a table for storing user information
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    name VARCHAR(100),
    age INT,
    gender VARCHAR(10),
    location VARCHAR(100)
);

-- Creating a table for storing product information
CREATE TABLE products (
    product_id INT PRIMARY KEY,
    name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10, 2)
);

-- Creating a fact table for storing transaction data (fact table)
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    user_id INT,
    product_id INT,
    quantity INT,
    total_amount DECIMAL(10, 2),
    order_date DATE,
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

Explanation:
users table: Stores user demographics.
products table: Stores product details.
orders table: This is the fact table for storing transactional data, connected to users and products through foreign keys.


3. Inserting Sample Data:
   
SQL Code for Inserting Data:
sql

-- Inserting data into users table
INSERT INTO users (user_id, name, age, gender, location)
VALUES
(101, 'John Doe', 28, 'Male', 'New York'),
(102, 'Jane Smith', 22, 'Female', 'Los Angeles'),
(103, 'Sam Brown', 35, 'Male', 'Chicago');

-- Inserting data into products table
INSERT INTO products (product_id, name, category, price)
VALUES
(1001, 'Smartphone', 'Electronics', 500),
(1002, 'Laptop', 'Electronics', 1500),
(1003, 'Headphones', 'Accessories', 1200);

-- Inserting data into orders table
INSERT INTO orders (order_id, user_id, product_id, quantity, total_amount, order_date)
VALUES
(1, 101, 1001, 1, 500, '2024-10-01'),
(2, 102, 1002, 2, 3000, '2024-10-02'),
(3, 103, 1003, 1, 1200, '2024-10-03'),
(4, 101, 1001, 1, 500, '2024-10-01');

Explanation:
The users, products, and orders tables are populated with sample data to simulate an e-commerce scenario.


4. Querying the Data Warehouse:
You can now run queries on the data warehouse to retrieve insights such as sales reports, customer activity, etc.

Sample SQL Queries:

Total Sales by Product:
sql

SELECT p.name AS product_name, SUM(o.total_amount) AS total_sales
FROM orders o
JOIN products p ON o.product_id = p.product_id
GROUP BY p.name;

Top Customers by Purchase Amount:
sql

SELECT u.name, SUM(o.total_amount) AS total_spent
FROM orders o
JOIN users u ON o.user_id = u.user_id
GROUP BY u.name
ORDER BY total_spent DESC;

Sales Over Time:
sql

SELECT order_date, SUM(total_amount) AS daily_sales
FROM orders
GROUP BY order_date;


5. Visualization (Optional):
Now we visualize your data using Python libraries like Matplotlib or Seaborn.

Python Code for Sales Over Time Visualization:
python

import pandas as pd
import sqlite3
import matplotlib.pyplot as plt

# Connect to the warehouse database
conn = sqlite3.connect('ecommerce_warehouse.db')

# Query the data
query = "SELECT order_date, SUM(total_amount) as daily_sales FROM orders GROUP BY order_date"
df = pd.read_sql(query, conn)

# Plot the data
plt.figure(figsize=(8,6))
plt.plot(df['order_date'], df['daily_sales'], marker='o')
plt.title('Daily Sales Over Time')
plt.xlabel('Date')
plt.ylabel('Sales ($)')
plt.grid(True)
plt.show()

# Close the connection
conn.close()
This simple line plot visualizes how the total sales fluctuate over different days.

