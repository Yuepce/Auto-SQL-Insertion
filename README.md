# Auto-SQL-Insertion
这套脚本把“复制 Excel 转 INSERT”变成一键操作，同时保证日期识别正确、ID 不被误改，极大减少人工错误和操作时间。
For a faster SQL query insertion statements

1. 解决什么痛点？
	•	原流程：手动把 Excel 表格改格式、补 NULL、改日期、再写 INSERT 语句
	•	现工具：复制-粘贴一张表 → 点一下按钮 → 立即得到整条 INSERT 语句
省掉手动排版、避免漏改日期或拼错单引号，10 秒完成过去 10 分钟的工作。

2. 关键功能在做什么？
功能	    ||     说明	    ||     带来的好处
自动识别列名	|| 程序第一行就是从 Excel 复制来的列标题 ||	不需要提前配置字段
智能数据格式化 ||	只要列名里含 “date” 或 “day” 就把 8/14 位数字识别为真正的日期或日期时间；其它列保持原样 ||	避免把 8 位数字 ID 当日期、确保日期字段统一 YYYY-MM-DD 格式
空单元格→ NULL	|| 复制过来若某格为空，自动写成 SQL 的 NULL || 不再漏掉必填的 NULL
整行空白自动跳过	Excel || 里偶尔有空行，生成 SQL 时会忽略 ||	结果更干净
可选常量列 ||	一键决定要不要在每行末尾加两列：record_effective_datetime='2024-01-01' & record_created_by='SYSTEM'	便于审计；如果业务不需要也可以关闭
结果一次性输出 ||	最终 SQL 类似：INSERT INTO attendance (…) VALUES (…) , (…) ;	可直接粘贴到数据库执行

3. 用起来多简单？
复制 Excel → 粘贴 → 点生成
连时间字符串（如 9:00）都会自动补成 '09:00:00'，数字字段不会加引号，字符串自动转义，开发或数据团队直接可用。


One-pager: Excel → SQL Bulk Insert Generator

⸻

1. What pain does it remove?
	•	Old way: manually clean Excel, fix dates, fill NULLs, write one INSERT per row.
	•	New way: copy the sheet → click Generate → obtain a ready-to-run bulk INSERT in seconds.
Cuts a 10-minute, error-prone task down to 10 seconds.

⸻

2. What exactly happens under the hood?

Capability	How it works	Benefit
Auto-detect columns	First row of the paste is read as field names.	Zero pre-configuration.
Smart data formatting	A field is treated as a date only when its column name contains “date” or “day”.  YYYYMMDD → YYYY-MM-DD, YYYYMMDDhhmmss → YYYY-MM-DD hh:mm:ss. All other 8-digit values stay as numeric IDs.	Prevents mis-converting IDs like 10001010 into bogus dates.
Empty cells → NULL	Any blank cell becomes SQL NULL.	Ensures mandatory nulls without manual edits.
Skip blank rows	Fully empty lines are ignored.	Keeps output clean.
Optional audit columns	Toggle once to append record_effective_datetime = '2024-01-01' and record_created_by = 'SYSTEM' to every row.	Adds traceability when required.
Single bulk statement	Output: INSERT INTO table (…) VALUES (…), (…), …;	Direct copy-paste into the DB.


⸻

3. How simple is it to use?
	1.	Copy the entire table from Excel (tabs & newlines come for free).
	2.	Paste into the webpage.
	3.	Enter target table name, choose Yes/No for the extra columns, click Generate.
	4.	Paste the resulting SQL into any SQL client and run.

⸻

4. Why is the output reliable?
	•	Date logic bound to column names – only columns explicitly labeled with “date”/“day” are parsed, so numeric IDs remain untouched.
	•	Centralized parsing function – if we ever add new patterns (e.g., “timestamp”), it’s a one-line update.
	•	Client-side processing – all data stays in the browser; nothing is sent to a server.

⸻

5. Bottom line

The tool turns a tedious, error-prone Excel-to-SQL workflow into a one-click, bullet-proof operation—saving time, eliminating date/ID mistakes, and requiring no infrastructure budget.

请重新对以下逻辑进行SQL query代码生成。首先先将tb_member_account通过mbr_acct_cd这列和ra_rebate_details_account的member_account_code 进行merge，其次再用merge后的表中的（employer_account_code， member_account_code）和ra_rebate_details_funds的（employer_account_code， member_account_code）进行merge。之后基于以下筛选条件生产SQL query：Report Criteria：
Extract all information in ra_rebate_details_account and ra_rebate_details_funds where ra_rebate_details_account.account_type = “ER”
OR all information in ra_rebate_details_account and ra_rebate_details_funds where ra_rebate_details_account.account_type != “ER”, “REE”, “CEE” and ra_rebate_details_funds.fund_code != “SLMIF” or “SLMIF-B”
OR information in ra_rebate_details_account and ra_rebate_details_funds where ra_rebate_details_account.account_type != “ER”, “REE”, “CEE” and ra_rabte_details_fund.fund_code = “SLMIF” or “SLMIF-B” and [1st day of (tb_member_account.date_of_birth + 60 years)] > Report Generation Date
DELETE_FLAG = N for all tables in DWH used in the extraction.
