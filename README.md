# Data Cleaning with SQL
This is a project to cleaning data with SQL using dataset Nashville Housing that I took from Alex Freberg on Youtube to complete Data Analyst Bootcamp.

Dataset : https://github.com/AlexTheAnalyst/PortfolioProjects/blob/main/Nashville%20Housing%20Data%20for%20Data%20Cleaning.xlsx

### Dataset Description
This is a property dataset that used in Nashville, United States. The attributes are LandsUse, PropertyAddress, SaleDate, SalePrice, OwnerName and many more. First step
is to import dataset to SQL and start the cleaning process.

### Cleaning Process
1. Change Sale Date datatype
    ```
    ALTER TABLE NashvilleHousing
    ADD SaleDateConverted Date;

    UPDATE NashvilleHousing
    SET SaleDateConverted = CONVERT(Date,SaleDate)
    ```
    <img width="113" alt="image" src="https://github.com/melodyvictorian22/Nashville-Housing-Cleaning-in-SQL/assets/50192955/f2f39975-a055-4908-858d-bc3a9cdaae0e">

2. Breaking out address into individual columns

   Before :

   <img width="139" alt="image" src="https://github.com/melodyvictorian22/Nashville-Housing-Cleaning-in-SQL/assets/50192955/9305e6f3-3a39-46a2-981a-50f7ef4476ea">

   ```
   ALTER TABLE NashvilleHousing
   ADD PropertySplitAdress nvarchar(255);
    
   UPDATE NashvilleHousing
   SET PropertySplitAdress = SUBSTRING(PropertyAddress, 1, CHARINDEX(',', PropertyAddress) -1)
    
   ALTER TABLE NashvilleHousing
   ADD PropertySplitCity nvarchar(255);
    
   UPDATE NashvilleHousing
   SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',', PropertyAddress) +1, LEN(PropertyAddress))
   ```

   After :

   <img width="146" alt="image" src="https://github.com/melodyvictorian22/Nashville-Housing-Cleaning-in-SQL/assets/50192955/d73bc924-ced8-4642-8192-a13dabcf2a19">

3. Change Y and N in SoldAsVacant field into Yes or No

   Before :

   <img width="55" alt="image" src="https://github.com/melodyvictorian22/Nashville-Housing-Cleaning-in-SQL/assets/50192955/dc15f302-936d-4bfa-8103-ef9a39ae1141">

   ```
   UPDATE NashvilleHousing
   SET SoldAsVacant =
       CASE WHEN SoldAsVacant = 'Y' THEN 'Yes'
	          WHEN SoldAsVacant = 'N' THEN 'No'
	          ELSE SoldAsVacant
	          END
   ```

   After :

   <img width="104" alt="image" src="https://github.com/melodyvictorian22/Nashville-Housing-Cleaning-in-SQL/assets/50192955/1c6cbbbb-912d-4c6f-9da0-89dc25fe8334">

4. Remove Duplicates with CTE
   ```
   WITH RowNumCTE AS (
   SELECT *, 
    	ROW_NUMBER() OVER (
    	PARTITION BY ParcelID,
    				PropertyAddress,
    				SalePrice,
    				SaleDate,
    				LegalReference
    				ORDER BY UniqueID
             ) row_num
    FROM NashvilleHousing
    )
    
    DELETE
    FROM RowNumCTE
    WHERE row_num > 1
   ```

5. Delete Unused Columns
   
   ```
   ALTER TABLE NashvilleHousing
   DROP COLUMN OwnerAddress, TaxDistrict, PropertyAddress
    
   ALTER TABLE NashvilleHousing
   DROP COLUMN SaleDate
   ```

### Conclusion
In this project we can cleaning data using SQL Queries, but it's so complicated. If you want to clean raw data using SQL, better to copy the data for better usage.
