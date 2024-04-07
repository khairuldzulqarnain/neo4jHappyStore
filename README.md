# neo4jHappyStore

## Load the csv file
## Most payment method
LOAD CSV WITH HEADERS FROM 'file:///teststore.csv' AS row

MERGE (pm:PaymentMethod {method: row.`Payment Method`})
MERGE (o:Order {orderID: row.`Order ID`})
SET o.date = row.Date,
    o.product = row.Product,
    o.price = toFloat(row.Price),
    o.quantity = toInteger(row.Quantity),
    o.cost = toFloat(row.Cost),
    o.city = row.City,
    o.country = row.Country,
    o.purchaseType = row.`Purchase Type`,
    o.revenue = toFloat(row.Revenue)
MERGE (o)-[:PAID_WITH]->(pm);

## Query for most selling product

LOAD CSV WITH HEADERS FROM 'file:///teststore.csv' AS row

MERGE (m:Manager {name: row.Manager})
MERGE (o:Order {orderID: row.`Order ID`})
SET o.date = row.Date,
    o.price = toFloat(row.Price),
    o.quantity = toInteger(row.Quantity),
    o.cost = toFloat(row.Cost),
    o.city = row.City,
    o.country = row.Country,
    o.purchaseType = row.`Purchase Type`,
    o.paymentMethod = row.`Payment Method`,
    o.revenue = toFloat(row.Revenue)

// Merge the product node and create a relationship to the order
MERGE (p:Product {name: row.Product})
MERGE (o)-[:CONTAINS]->(p)
MERGE (o)-[:HANDLED_BY]->(m);

// Query to find the best selling product
MATCH (p:Product)<-[r:CONTAINS]-()
RETURN p.name AS BestSellingProduct, SUM(r.quantity) AS TotalQuantity
ORDER BY TotalQuantity DESC
LIMIT 1;

## Top sell manager

MATCH p=()-[r:HANDLED_BY]->() RETURN p LIMIT 100
