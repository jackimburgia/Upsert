# .Net Upsert
The .Net Upsert package is for one step Update and Insert of typed collections.


## Getting Started
To install .Net Upsert, run the following from the Package Manager Console.
```csharp
Install-Package Upsert -Version 1.0.0
```

## Important
* Database table column names and class properties should be the same
* Database tables must have primary keys
* The user that executes the Upsert code must have permissions to create objects

## Setup database
This example will create and populate a SQL Server database table used in this example.
```sql
CREATE SCHEMA Sales;


CREATE TABLE Sales.Customer (
	CustomerID INT NOT NULL PRIMARY KEY,
	Name VARCHAR(20) NOT NULL,
	City VARCHAR(20) NOT NULL,
	State CHAR(2) NOT NULL
);


INSERT INTO Sales.Customer (CustomerID, Name, City, State)
VALUES (1, 'Joe Smith', 'Philadelphia', 'PA'),
	(2, 'Mary Jones', 'New York', 'NY'),
	(3, 'Mike Andersen', 'Raleigh', 'NC');
```

## Customer class
Create a class that maps to the database table.
```csharp
    public class Customer
    {
        public int CustomerID {get;set;}
        public string Name {get;set;}
        public string City {get;set;}
        public string State {get;set;}
    }
```

## Upsert
The Upsert code will update any records that match on the primary key and attempt to insert any records that do not match on the primary key.
```csharp
using Spearing.Utilities.Data.Upsert;


    string connStr = @"Data Source=ServerName;Initial Catalog=DatabaseName;User Id=SomeUser; Password=password1;";

    List<Customer> customers = new List<Customer>()
    {
        new Customer() { CustomerID = 1, Name = "Joseph Smith", City = "Philadelphia", State = "PA" },
        new Customer() { CustomerID = 4, Name = "Jane West", City = "Denver", State = "CO" }
    };

    customers.Upsert("Sales", "Customer", connStr);
```

## Skip columns
* You can skip specified columns from being updated in the event of a match.  Inserts are not affected by these parameters.
* In this example, the first customer will match but will only update the City column even though Name and State were updated also
* The second customer will not match and insert as expected
```csharp
    string connStr = @"Data Source=ServerName;Initial Catalog=DatabaseName;User Id=SomeUser; Password=password1;";
    
    List<Customer> customers = new List<Customer>()
    {
        new Customer() { CustomerID = 1, Name = "J. Smith", City = "Philly", State = "XX" },
        new Customer() { CustomerID = 5, Name = "Terry Jenkins", City = "Los Angeles", State = "CA" }
    };

    customers.Upsert("Sales", "Customer", connStr, "Name", "State");  
```
