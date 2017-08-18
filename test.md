### 练习：

#### A（课堂练习）.创建删除数据库，和插入修改数据练习:

1. 创建一个mingzi_score表 
    字段1： account_id 数字类型 非空 唯一 外键约束（account的id字段）
    字段2：score  自增整数
2. 删除自己mingzi表和mingzi_score表
3. account表中自行查找自己的id；
4. 修改自己的数据, 按照以下规则
	1. 将account表中性别字段（gender ）修改为 男生 1，  女生 2
	2. 在account中按桌子填入小组号码（group_id），
	3. 将 is_return 字段修改正确 true表示参加过上期课程  false 表示没有参加过上期课程
5. 把自己的id加入 在account_score中 

#### 作业

1. 在account表中筛选出即是男生又是上期学员的id,name的数据 按照创建日期倒叙排列
2. 联表查询，account_score中分值从小到大第5位的用户名, 列出名字和分数
3. 联表查询，列出每个人的姓名和所在的小组名, (没有小组的人不需要显示出来)
4. 计算每个小组，新学员的数量，列出组名和数量（数量为0的小组也要显示）    提示： group by
5. 联表查询，列出小组名和小组的平均分   提示： group by

### 答案：
#### A

1.
```
CREATE TABLE mingzi_score (
    account_id INTEGER NOT NULL UNIQUE ,
    score serial ,
    FOREIGN KEY (account_id) REFERENCES account(id)
);
```
2.
`drop table mingzi_score;`

3.
`select id from account where name = 'your_name' ;`

4.
```sql
UPDATE account 
SET gender = '1',
    is_return = TRUE ,
    group_id = 1
WHERE id=1;
```

5.
`INSERT INTO account_score VALUES (2);`

#### B

1.
```
SELECT id,name FROM account 
WHERE gender = '1' AND is_return = TRUE 
ORDER BY date_created DESC ;
```

2.
```
SELECT account.name, account_score.score FROM 
account RIGHT JOIN account_score ON account_score.account_id = account.id 
ORDER BY account_score.score 
LIMIT 1 OFFSET 4;
```

3.
```
SELECT account.name, study_group.name FROM account 
JOIN study_group ON study_group.id = account.group_id;
```

4.
```
SELECT study_group.name, count(account.id) FROM account 
RIGHT JOIN study_group ON study_group.id = account.group_id
GROUP BY study_group.id;
```

5.

```
SELECT study_group.name, AVG(account_score.score) FROM account 
RIGHT JOIN study_group ON study_group.id = account.group_id
RIGHT JOIN account_score ON account_score.account_id = account.id
GROUP BY study_group.id;
```