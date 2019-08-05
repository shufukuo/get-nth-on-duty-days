# get-nth-on-duty-days
R 小工具, 列出整年度每個月的第 n 個上班日的隔 m 日日期

需要先準備好一個檔案紀錄整年度特殊節日

範例: [holiday_2019.csv](https://drive.google.com/open?id=15zSgQuQZSDLyo8EKrq-Tq0Znroup98Ek)

以下為 n = 1, m = 1 的例子
```r
require(data.table)

df_holidays <- read.csv( "holiday_2019.csv", header = TRUE )

# 特殊節日日期
date_special <- data.table::as.IDate(df_holidays[ , "date"])

# 特殊節日是否放假, 1: 是, 0: 否
is_off_duty <- df_holidays[ , "is_off_duty"]
names(is_off_duty) <- as.character(date_special)

out_dates <- character(12)
dates <- seq( data.table::as.IDate("2019/01/01"), by = "month", length.out = 12 )

# 第 n 個上班日
n <- 1

# 隔 m 日
m <- 1

for (i in 1:12) {
	tmp_date <- dates[i]
	j <- 0
	
	while (TRUE) {
		# 判斷是否為特殊節日
		if ( tmp_date %in% date_special ) {
			# 判斷特殊節日是否為上班日
			if ( is_off_duty[as.character(tmp_date)] == 0 ) {
				j <- j + 1
				if ( j == n ) {
					out_dates[i] <- as.character( tmp_date + m )  # 第 n 個上班日隔 m 日
					break
				}
			}
		}
		# 判斷是否為週末
		else if ( (data.table::wday(tmp_date) - 1) %in% 1:5 ) {
			j <- j + 1
			if ( j == n ) {
				out_dates[i] <- as.character( tmp_date + m )  # 第 n 個上班日隔 m 日
				break
			}
		}
		
		tmp_date <- tmp_date + 1
	}
}
```

Output:
```r
> out_dates
 [1] "2019-01-03" "2019-02-02" "2019-03-05" "2019-04-02"
 [5] "2019-05-03" "2019-06-04" "2019-07-02" "2019-08-02"
 [9] "2019-09-03" "2019-10-02" "2019-11-02" "2019-12-03"
```

若是想列出每個月第 3 個上班日, 則設定 n = 3, m = 0
```r
> out_dates
 [1] "2019-01-04" "2019-02-12" "2019-03-06" "2019-04-03"
 [5] "2019-05-06" "2019-06-05" "2019-07-03" "2019-08-05"
 [9] "2019-09-04" "2019-10-03" "2019-11-05" "2019-12-04"
```

P.S.

上方是以每個月 1 號起算的結果, 若是想列出每個月 5 號起算第 1 個上班日隔 1 日, 則是將宣告 ```dates``` 的日期改為 ```"2019/01/05"```, 便可得到下方結果:
```r
> out_dates
 [1] "2019-01-08" "2019-02-12" "2019-03-06" "2019-04-09"
 [5] "2019-05-07" "2019-06-06" "2019-07-06" "2019-08-06"
 [9] "2019-09-06" "2019-10-06" "2019-11-06" "2019-12-06"
```
