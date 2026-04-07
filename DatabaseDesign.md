
#Part 2: Database Desing

## Table Desing 

1. Company
id (PK)
name 

2. Warehouse
id (PK)
name 
company_id (FK)

3. Product
id (PK)
name 
sku (UNIQUE)
price

4. Inventory
id (PK)
product_id (FK)
warehouse_id (FK)
quantity

5. Inventory Transaction
id (PK)
product_id (FK)
warehouse_id (FK)
change_quantity
type (SALE / RESTOCK)
created_at

6. Supplier
id (PK)
name 
contact_email


7. Product Supplier

product_id (FK)
supplier_id (FK)

8. Bundle Products
id (PK)
product_id (FK)
quantity

Questions for product team
Can one product belong to more than one company, or only one?
Can one product have multiple suppliers?
Recent sales like in which context 24H, Week, month?
Do we need to track warehouse location details?
In bundles of other products should there be limitations

## Design Decisions

1. Separate Product and Inventory
Product = basic details  
Inventory = stock in warehouse  
This allows multiple warehouses per product  

2. SKU as Unique
Ensures no duplicate products  
Helps in fast lookup  

3. Inventory Transaction Table
Tracks history of stock changes  
Useful for auditing and reports  

4. Use of Foreign Keys
Maintains relationships between tables  
Ensures data integrity  

Summary
This design:
Supports multiple warehouses per company  
Tracks inventory correctly  
Handles suppliers and bundles  
Keeps schema simple 
