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

