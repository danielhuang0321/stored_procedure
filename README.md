# Stored Procedure 預存程序 DEMO


### 這是一個MySQL儲存程序的範例演示，目的是檢查並購買一個產品。

在程式碼中，將四個輸入參數定義為整數，分別是 in_user_id、in_product_id、in_buy_count 和 out_result。

![Screenshot of a comment on a GitHub issue showing an image, added in the Markdown, of an Octocat smiling and raising a tentacle.](https://bayimg.com/b68b1f4b17d090fadaf453fcd9dfa6bad7a79057.jpg)

在這個存儲程序中，使用了一些變量來存儲獲取的值，如 @user_verified 和 @stock。

在存儲程序中，先設置了 @user_verified 變量的初始值為-1，然後通過查詢 user 資料表並將驗證狀態值 assigned 給該變量。

CREATE DEFINER=`root`@`localhost` PROCEDURE `BuyProduct`(
	IN  in_user_id INT,
	IN  in_product_id INT,
	IN  in_buy_count INT,
	OUT out_result INT
)
BEGIN
    -- 取得使用者驗證狀態
	SET @user_verified := -1;
    
    SELECT verified 
    INTO @user_verified
    FROM `user` 
    WHERE user_id = in_user_id;
    
    IF @user_verified = -1 THEN
		SET out_result = -1; -- 無效的使用者 ID
	ELSEIF @user_verified = 0 THEN 
		SET out_result = -2; -- 使用者未通過驗證
	ELSEIF @user_verified = 1 THEN 
		-- 檢查庫存數量
        SET @stock := -1;
        
		SELECT stock 
        INTO @stock
        FROM product 
        WHERE product_id = in_product_id;
        
        IF @stock = -1 THEN
			SET out_result = -3; -- 無效的商品 ID
		ELSEIF @stock < in_buy_count THEN
			SET out_result = -4; -- 商品庫存不足
		ELSE
			-- 建立訂單
			INSERT INTO `user_order`
			(
				`user_id`,
				`product_id`,
				`order_status`,
				`create_datetime`
            )
			VALUES
			(
				in_user_id,
				in_product_id,
				'NEW ORDER',
				NOW()
            );

			-- 寫入 LOG
            INSERT INTO `user_log`
			(
				`user_id`,
				`action`,
				`action_datetime`
            )
			VALUES
			(
				in_user_id,
				'CREATE ORDER',
				NOW()
            );
            SET out_result = 1; -- OK
		END IF;
    END IF;
END



- 如果獲取的值等於-1，則意味著未找到對應的使用者ID，設置 out_result 變量為-1，表示無效的使用者 ID。
- 如果獲取的值等於0，則意味著使用者未通過驗證，設置 out_result 變量為-2，表示使用者未通過驗證。如果獲取的值等於1，則檢查產品的庫存量。
- 如果獲取的值等於-1，則設置 out_result 變量為-3，表示無效的商品 ID。
- 如果庫存量小於需要購買的量，則設置 out_result 變量為-4，表示商品庫存不足。
- 如果以上都符合，則創建訂單和寫入日誌，最後將 out_result 變量設置為1，表示OK。
- 
![Screenshot of a comment on a GitHub issue showing an image, added in the Markdown, of an Octocat smiling and raising a tentacle.](https://bayimg.com/aca083f8aa00f7d646064f5df604ad159675acf6.jpg)



