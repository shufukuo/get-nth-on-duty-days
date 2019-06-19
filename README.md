# get-nth-on-duty-days
R 小工具, 列出整年度每個月的第 1 個上班日的隔日日期

必須事先準備一個檔案紀錄整年度特殊節日

範例: [holiday_2019.csv](https://drive.google.com/open?id=15zSgQuQZSDLyo8EKrq-Tq0Znroup98Ek)

```
require(data.table)

df_holidays <- read.csv( "holiday_2019.csv", header = TRUE )

# 特殊節日日期
date_special <- data.table::as.IDate(df_holidays[ , "date"])

# 特殊節日是否上班, 1: 是, 0: 否
is_on_duty <- df_holidays[ , "is_on_duty"]
names(is_on_duty) <- as.character(date_special)

nth_on_duty_days <- character(12)
dates <- seq( data.table::as.IDate("2019/01/01"), by = "month", length.out = 12 )

for (i in 1:12) {
	tmp_date <- dates[i]
	
	while (TRUE) {
		# 判斷是否為週末
		if ( (data.table::wday(tmp_date) - 1) %in% 1:5 ) {
			# 判斷是否為特殊節日
			if ( !(tmp_date %in% date_special) ) {
				nth_on_duty_days[i] <- as.character( tmp_date + 1 ) # 第 1 個上班日隔日
				break
			}
			# 判斷是否為上班日
			else if ( is_on_duty[as.character(tmp_date)] == 0 ) {
				nth_on_duty_days[i] <- as.character( tmp_date + 1 ) # 第 1 個上班日隔日
				break
			}
		}
		
		tmp_date <- tmp_date + 1
	}
}
```

Output:
```
> nth_on_duty_days
 [1] "2019-01-03" "2019-02-02" "2019-03-05" "2019-04-02"
 [5] "2019-05-03" "2019-06-04" "2019-07-02" "2019-08-02"
 [9] "2019-09-03" "2019-10-02" "2019-11-02" "2019-12-03"
```


P.S.

上方例子是以每月 1 日起算列出每個月的第 1 個上班日之隔日. 若是想列出每個月 5 號起算第 1 個上班日隔日, 只需將宣告 ```dates``` 的日期改為 ```"2019/01/05"```, 便可得到下方結果:
```
> nth_on_duty_days
 [1] "2019-01-08" "2019-02-12" "2019-03-06" "2019-04-09"
 [5] "2019-05-07" "2019-06-06" "2019-07-06" "2019-08-06"
 [9] "2019-09-06" "2019-10-08" "2019-11-06" "2019-12-06"
```
