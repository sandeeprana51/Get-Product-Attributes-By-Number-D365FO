// 1. Clear the temp table buffer before filling new data.
tempTable.clear();

// 2. Set known (non-attribute) fields from your other joined tables.
tempTable.Customer_Name   = salesTable.DeliveryName;         // Customer Name from SalesTable
tempTable.P_Code          = inventTable.ItemId;              // Product Code from InventTable
tempTable.Job_Order_date  = prodRouteJob.FromDate;           // Job Order Date from ProdRouteJob
tempTable.Batch_No        = inventDim.InventBatchId;         // Batch Number from InventDim
tempTable.Product_Name    = salesLine.Name;                  // Product Name from SalesLine
tempTable.Quantity        = salesLine.SalesQty;              // Quantity from SalesLine

// 3. Prepare your ProductNumber for the stored procedure.
str productNumber = 'P-000016702';

// 4. Create a new SQL connection and statement.
Connection connection = new Connection();
Statement statement = connection.createStatement();

// 5. Format the SQL query to call your stored procedure with the product number.
str sql = strFmt("EXEC OL_SP_GetProductAttributesByNumber @ProductNumber = '%1'", productNumber);

// 6. Execute the stored procedure and get the result set.
ResultSet resultSet = statement.executeQuery(sql);

// 7. Loop through each result row (attribute/value pair) from your SP.
while (resultSet.next())
{
    str attributeName  = resultSet.getString(2); // 2nd column: AttributeName
    str attributeValue = resultSet.getString(3); // 3rd column: AttributeValue

    // 8. Map each attribute name to the correct tempTable field.
    switch (attributeName)
    {
        case 'Across gap':
            tempTable.Across_Gap = attributeValue;
            break;
        // Repeat for every attribute/field you want to handle:
        // case 'Arround gap':
        //     tempTable.Arround_Gap = attributeValue;
        //     break;
        // ...
    }
}

// 9. After mapping all attributes, insert the filled temp table buffer as a new row.
tempTable.insert();
