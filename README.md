# Stored Procedure 預存程序 DEMO


### 這是一個MySQL儲存程序的範例演示，目的是檢查並購買一個產品。

在程式碼中，將四個輸入參數定義為整數，分別是 in_user_id、in_product_id、in_buy_count 和 out_result。

在這個存儲程序中，使用了一些變量來存儲獲取的值，如 @user_verified 和 @stock。

在存儲程序中，先設置了 @user_verified 變量的初始值為-1，然後通過查詢 user 資料表並將驗證狀態值 assigned 給該變量。

- 如果獲取的值等於-1，則意味著未找到對應的使用者ID，設置 out_result 變量為-1，表示無效的使用者 ID。
- 如果獲取的值等於0，則意味著使用者未通過驗證，設置 out_result 變量為-2，表示使用者未通過驗證。如果獲取的值等於1，則檢查產品的庫存量。
- 如果獲取的值等於-1，則設置 out_result 變量為-3，表示無效的商品 ID。
- 如果庫存量小於需要購買的量，則設置 out_result 變量為-4，表示商品庫存不足。
- 如果以上都符合，則創建訂單和寫入日誌，最後將 out_result 變量設置為1，表示OK。


