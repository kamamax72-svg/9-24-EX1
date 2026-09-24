--สำรวจข้อมูล
SELECT * FROM Receipts
SELECT * FROM Details
SELECT * FROM Employees
SELECT * FROM Products
--เป้าหมาย ต้องการสร้างรายการจำหน่ายสินค้า ผู้ขายคือ วุฒิ
--สินค้า ได้แก่ ดินสอ 5 แท่ง และยางลบ 4 ก้อน
Begin Transaction
--1. เพิ่มใบเสร็จใหม่ Recripts ยังไงมียอด totalCash
Insert into Receipts(ReceiptDate,EmployeeID,TotalCash)
	Values(GETDATE(),4,0)
--2. เพิ่มรายการสินค้าใน Details 2 รายการ 
Insert into Details(ReceiptID,ProductID,UnitPrice,Quantity)
	Values(6,1,17,5)
Insert into Details(ReceiptID,ProductID,UnitPrice,Quantity)
	Values(6,2,17,5)
--3. ปรับปรุงยอดขาย TotalCash
update Receipts set TotalCash = 
	(select sum(unitprice*quantity) from Details
	where ReceiptID = 6 )
	where receiptID = 6
--4. ปรับปรุงจำนวนสินค้า ดินสอ -5 ยางลบ -4
update Products set UnitsInStock = UnitsInStock - 5 where productID = 1
update Products set UnitsInStock = UnitsInStock - 5 where productID = 2
--จบการทำงาน
commit

--ทดสอบ Roll back
Begin Transaction
--1. เพิ่มใบเสร็จใหม่ Recripts ยังไงมียอด totalCash
Insert into Receipts(ReceiptDate,EmployeeID,TotalCash)
	Values(GETDATE(),4,0)
--2. เพิ่มรายการสินค้าใน Details 2 รายการ 
Insert into Details(ReceiptID,ProductID,UnitPrice,Quantity)
	Values(7,1,17,5)
Insert into Details(ReceiptID,ProductID,UnitPrice,Quantity)
	Values(7,2,17,5)

	--สำรวจข้อมูล
	SELECT * FROM Receipts where ReceiptID = 7
	SELECT * FROM Details where ReceiptID = 7
--หากระบบผิดพลาด เราจะ Rollback
Rollback

--PART 1
--0.
SELECT CustomerID , CompanyName
FROM Customers
WHERE CustomerID = 'ALFKI';
--2.
USE Northwind;
Begin Transaction
Insert into Orders
	(CustomerID,EmployeeID,
	OrderDate,RequiredDate,Freight)
Values
	('Alfki',1,GETDATE(),
	DATEADD(DAY,7,GETDATE()), 50.00 );
--3.
SELECT SCOPE_IDENTITY() as NewOrderID 
--4.
INSERT INTO [Order Details]
(OrderID, ProductID,
UnitPrice, Quantity, Discount)
SELECT
	11078, ProductID,
	UnitPrice, 2,0
FROM Products 
WHERE ProductID = 1;
--5.
SELECt *
FROM Orders
WHERE OrderID = 11078;

SELECT * 
FROM [Order Details]
WHERE OrderID = 11078;
--6.
SELECT *
FROM Orders
WHERE OrderID = 11078;

--Part 2
--1.
USE Northwind;
Begin Transaction
Insert into Orders
	(CustomerID,EmployeeID,
	OrderDate,RequiredDate,Freight)
Values
	('Alfki',1,GETDATE(),
	DATEADD(DAY,7,GETDATE()), 75.00 );
--2.
SELECT SCOPE_IDENTITY()
		As RollbackOrderID;
--3.
INSERT INTO [Order Details]
(OrderID, ProductID,
UnitPrice, Quantity, Discount)
SELECT
	11079, ProductID,
	UnitPrice, 1,0
FROM Products 
WHERE ProductID = 1;
--4.
INSERT INTO [Order Details]
(OrderID, ProductID,
UnitPrice, Quantity, Discount)
SELECT
	11079, ProductID,
	UnitPrice, 2,0
FROM Products 
WHERE ProductID = 2;
--5.
SELECT *
FROM Orders
WHERE OrderID = 11079;
SELECT * 
FROM [Order Details]
WHERE OrderID = 11079;
--6.
ROLLBACK
--7.
SELECT *
FROM Orders
WHERE OrderID = 11079;
SELECT * 
FROM [Order Details]
WHERE OrderID = 11079;
