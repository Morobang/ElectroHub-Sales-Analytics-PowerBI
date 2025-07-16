
# Data Transformation (Part 2)

## 1. Merging Fact Table with Dim_Product
- **Join Type**: Left Outer Join  
  - **Fact_Table** (left) ↔ **Dim_Product** (right) on `ProductID`.  
  - **Action**: Removed duplicate `Price per Unit` column from Fact_Table and renamed joined column.  
  ![](/04_Assets/Screenshots/Merge_Fact_Dim_Product.png)


## 2. Calculating Total Sales
- **Formula**: `Total Sales = Price per Unit * Units Sold`  
- **Result**: Added new column and deleted the old null-filled column.  
  ![](/04_Assets/Screenshots/Total_Sales.png)


## 3. Merging Fact Table with Dim_Promotion
- **Join Type**: Left Outer Join  
  - **Fact_Table** (left) ↔ **Dim_Promotion** (right) on `PromotionID`.  
  - **Null Handling**: Replaced nulls in `Discount Percentage` with `0` (no promo).  
  ![](/04_Assets/Screenshots/Merge_PromotionID.png)  
  ![](/04_Assets/Screenshots/Replace_Values.png)

## 4. Calculating Discount Value & Net Sales
- **Discount Value**: `Total Sales * Discount Percentage`  
  ![](/04_Assets/Screenshots/Discount_Value.png)  
- **Net Sales**: `Total Sales - Discount Value`  
  ![](/04_Assets/Screenshots/Net_Sales.png)

## 5. Final Cleanup
- Removed legacy columns:  
  - Old `Discount Percentage`  
  - Old `Discount Value`  
- Applied all changes to the model.

---

### **🔧 Power Query Scripts**  
1. **Merge_Product.txt**:  
   ```m
   // Left Outer Join Fact_Table and Dim_Product
   #"Merged Product" = Table.NestedJoin(Fact_Table, {"ProductID"}, Dim_Product, {"ProductID"}, "Dim_Product", JoinKind.LeftOuter)
   #"Expanded Product" = Table.ExpandTableColumn(#"Merged Product", "Dim_Product", {"Price per Unit"}, {"Price per Unit (Joined)"})
   #"Removed Old Price" = Table.RemoveColumns(#"Expanded Product", {"Price per Unit"})
   #"Renamed Column" = Table.RenameColumns(#"Removed Old Price", {{"Price per Unit (Joined)", "Price per Unit"}})
   ```

2. **Net_Sales_Calculations.txt**:  
   ```m
   // Net Sales Logic
   #"Added Total Sales" = Table.AddColumn(Fact_Table, "Total Sales", each [Price per Unit] * [Units Sold], type number)
   #"Added Discount Value" = Table.AddColumn(#"Added Total Sales", "Discount Value", each [Total Sales] * [Discount Percentage], type number)
   #"Added Net Sales" = Table.AddColumn(#"Added Discount Value", "Net Sales", each [Total Sales] - [Discount Value], type number)
   ```
