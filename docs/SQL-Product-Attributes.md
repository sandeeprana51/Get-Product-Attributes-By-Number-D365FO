# SQL: Get Product Attributes By Number

This page documents the **SQL Server stored procedure** for retrieving product attribute name/value pairs by product number in D365 F&O.

## 📜 Stored Procedure

```sql
-- Place your SQL code here
CREATE PROCEDURE usp_GetProductAttributesByNumber
    @ProductNumber NVARCHAR(50)
AS
BEGIN
    SELECT
        p.DisplayProductNumber      AS ProductNumber,
        a.Name                     AS AttributeName,
        COALESCE(
            CAST(ev.TextValue AS NVARCHAR(255)),
            CAST(ev.FloatValue AS NVARCHAR(255)),
            CAST(ev.IntValue AS NVARCHAR(255)),
            CAST(ev.CurrencyValue AS NVARCHAR(255)),
            CAST(ev.BooleanValue AS NVARCHAR(255)),
            CAST(ev.DateTimeValue AS NVARCHAR(255))
        ) AS AttributeValue
    FROM
        EcoResProductAttributeValue v
        JOIN EcoResAttribute a ON v.Attribute = a.RecId
        JOIN EcoResProduct p ON v.Product = p.RecId
        LEFT JOIN EcoResValue ev ON v.Value = ev.RecId
        LEFT JOIN EcoResAttributeType atype ON a.AttributeType = atype.RECID
    WHERE
        p.DisplayProductNumber = @ProductNumber
    ORDER BY
        a.Name
END
