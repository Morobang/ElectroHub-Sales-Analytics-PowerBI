# Data Profiling & Transformation (Part 1)

## 1. Table Renaming
- Renamed **Sheet3** to **Fact_Table**.

## 2. Data Profiling (View Tab)
Checked for all 4 tables (`Fact_Table`, `Dim_Customers`, `Dim_Promotion`, `Dim_Products`):
- **Column Distribution**: Histograms for numeric columns.
- **Column Profile**: Stats like min/max/unique values.
- **Column Quality**: Identified missing/error values.

## 3. Data Type Adjustments
| Table           | Column         | Old Data Type | New Data Type | Reason               |
|-----------------|----------------|---------------|---------------|----------------------|
| Dim_Customers   | CustomerID     | Whole Number  | Text          | No math operations   |
| Fact_Table      | CustomerID     | Whole Number  | Text          | Consistency with Dim |
| Fact_Table      | PromotionID    | Whole Number  | Text          | No math operations   |
| Fact_Table      | ProductID      | Whole Number  | Text          | No math operations   |

## 4. Dim_Promotion Fixes
- **Headers**: Fixed by `Transform > Use First Row as Headers`.
- **Price Reduction Type**: Standardized values:
  ```text
  "20% off", "10% off", "Buy 1 Get 1 Free", "50% off", "70% off"
  ```
- **Added Column**: Created `Percentage` column (extracted numeric value from text).  
  ![](/04_Assets/Screenshots/Percentage.png)

## 5. Screenshot References
- Dim_Promotion before fixes:  
  ![](/04_Assets/Screenshots/Dim_Promotion.png)


### ** Power Query Scripts**  
1. **Dim_Promotion_Transform.txt**:  
   ```m
   // Step 1: Use first row as headers
   #"Promotion Headers Fixed" = Table.PromoteHeaders(Dim_Promotion, [PromoteAllScalars=true])
   
   // Step 2: Extract percentage
   #"Added Percentage" = Table.AddColumn(#"Promotion Headers Fixed", "Percentage", 
       each if Text.Contains([Price Reduction Type], "%") then 
           Number.From(Text.BetweenDelimiters([Price Reduction Type], "", "%"))/100 
       else null)
   ```

2. **Fact_Table_Transform.txt**:  
   ```m
   // Change data types to Text
   #"Changed Types" = Table.TransformColumnTypes(Fact_Table, 
       {{"CustomerID", type text}, {"PromotionID", type text}, {"ProductID", type text}})
   ```



  
