# 参考答案

1.
```sql
 SELECT SNAME,SSEX,CLASS FROM STUDENT;
```
2. 
```sql
SELECT DISTINCT DEPART FROM TEACHER;
```
3. 
```sql
SELECT * FROM STUDENT;
```
4. 
```sql
SELECT * FROM SCORE WHERE DEGREE BETWEEN 60 AND 80;
```
5. 
```sql
SELECT * FROM SCORE WHERE DEGREE IN (85,86,88);
```
6. 
```sql
SELECT * FROM STUDENT WHERE CLASS='95031' OR SSEX='女';
```
7. 
```sql
SELECT * FROM STUDENT ORDER BY CLASS DESC;
```
8. 
```sql
SELECT * FROM SCORE ORDER BY CNO ASC,DEGREE DESC;
```
9. 
```sql
SELECT  COUNT(*) FROM STUDENT WHERE CLASS='95031';

```

10. 
```sql
SELECT SNO,CNO FROM SCORE WHERE DEGREE=(SELECT MAX(DEGREE) FROM SCORE);

SELECT SNO,CNO FROM SCORE ORDER BY DEGREE DESC LIMIT 1;
```
11. 
```sql
SELECT AVG(DEGREE) FROM SCORE WHERE CNO='3-105';
```
12. 
```sql
select avg(degree),cno
from score
where cno like '3%'
group by cno
having count(sno)>= 5;
```
13. 
```sql
SELECT SNO FROM SCORE GROUP BY SNO HAVING MIN(DEGREE)>70 AND MAX(DEGREE)<90;
```
14. 
```sql
SELECT A.SNAME,B.CNO,B.DEGREE FROM STUDENT AS A JOIN SCORE AS B ON A.SNO=B.SNO;
```
15. 
```sql
SELECT A.CNAME, B.SNO,B.DEGREE FROM COURSE AS A JOIN SCORE AS B ON A.CNO=B.CNO ;
```
16. 
```sql
SELECT A.SNAME,B.CNAME,C.DEGREE FROM STUDENT A JOIN (COURSE B,SCORE C)
ON A.SNO=C.SNO AND B.CNO =C.CNO;
```
17. 
```sql
SELECT AVG(A.DEGREE) FROM SCORE A JOIN STUDENT B ON A.SNO = B.SNO WHERE B.CLASS='95033';
```
18.
```sql
 SELECT A.SNO,A.CNO,B.RANK FROM SCORE A,GRADE B WHERE A.DEGREE BETWEEN B.LOW AND B.UPP 

ORDER BY RANK;
```
19. 
```sql
SELECT A.* FROM SCORE A JOIN SCORE B WHERE A.CNO='3-105' AND A.DEGREE>B.DEGREE AND 

B. SNO='109' AND B.CNO='3-105';
--另一解法：
SELECT A.* FROM SCORE A  WHERE A.CNO='3-105' AND A.DEGREE>ALL(SELECT DEGREE FROM 

SCORE B WHERE B.SNO='109' AND B.CNO='3-105');
```
20. 
```sql
SELECT * FROM score s WHERE DEGREE<(SELECT MAX(DEGREE) FROM SCORE) GROUP BY SNO HAVING 

COUNT(SNO)>1 ORDER BY DEGREE ;
```
21. 见19的第二种解法

22. 
```sql
SELECT SNO,SNAME,SBIRTHDAY FROM STUDENT WHERE YEAR(SBIRTHDAY)=(SELECT YEAR(SBIRTHDAY) 

FROM STUDENT WHERE SNO='108');
ORACLE:select x.cno,x.Sno,x.degree from score x,score y where x.degree>y.degree and 

y.sno='109'and y.cno='3-105';
select cno,sno,degree from score   where degree >(select degree from score where sno='109' 

and cno='3-105')
```
23.
```sql
SELECT A.SNO,A.DEGREE FROM SCORE A JOIN (TEACHER B,COURSE C) ON A.CNO=C.CNO AND B.TNO=C.TNO WHERE B.TNAME='张旭';
--另一种解法：
select cno,sno,degree from score where cno=(select x.cno from course x,teacher y 

where x.tno=y.tno and y.tname='张旭');
-- 根据实际EXPLAIN此SELECT语句，第一个的扫描次数要小于第二个
```
24.
```sql
SSELECT A.TNAME FROM TEACHER A JOIN (COURSE B, SCORE C) ON (A.TNO=B.TNO AND B.CNO=C.CNO) 

GROUP BY C.CNO HAVING COUNT(C.CNO)>5;
--另一种解法：
select tname from teacher where tno in(select x.tno from course x,score y where 

x.cno=y.cno group by x.tno having count(x.tno)>5);
--实际测试1明显优于2
```

25.
```sql
select cno,sno,degree from score where cno=(select x.cno from course x,teacher y where 

x.tno=y.tno and y.tname='张旭');
```
26. 
```sql
SELECT CNO FROM SCORE GROUP BY CNO HAVING MAX(DEGREE)>85;
另一种解法：select distinct cno from score where degree in (select degree from score where 

degree>85);
```
27.
```sql
SELECT A.* FROM SCORE A JOIN (TEACHER B,COURSE C) ON A.CNO=C.CNO AND B.TNO=C.TNO
WHERE B.DEPART='计算机系';
--另一种解法：
SELECT * from score where cno in (select a.cno from course a join teacher b on 

a.tno=b.tno and b.depart='计算机系');
--此时2略好于1，在多连接的境况下性能会迅速下降
```
28。
```sql
select tname,prof from teacher where depart='计算机系' and prof not in (select prof from 

teacher where depart='电子工程系');
```
29。SELECT * FROM SCORE WHERE DEGREE>ANY(SELECT DEGREE FROM SCORE WHERE CNO='3-245') ORDER 

BY DEGREE DESC;

30。
```sql
SELECT * FROM SCORE WHERE DEGREE>ALL(SELECT DEGREE FROM SCORE WHERE CNO='3-245') ORDER 

BY DEGREE DESC;
```
31.
```sql
SELECT SNAME AS NAME, SSEX AS SEX, SBIRTHDAY AS BIRTHDAY FROM STUDENT
UNION
SELECT TNAME AS NAME, TSEX AS SEX, TBIRTHDAY AS BIRTHDAY FROM TEACHER;
```
32.
```sql
SELECT SNAME AS NAME, SSEX AS SEX, SBIRTHDAY AS BIRTHDAY FROM STUDENT WHERE SSEX='女'
UNION
SELECT TNAME AS NAME, TSEX AS SEX, TBIRTHDAY AS BIRTHDAY FROM TEACHER WHERE TSEX='女';
```
33.
```sql
SELECT A.* FROM SCORE A WHERE DEGREE<(SELECT AVG(DEGREE) FROM SCORE B WHERE A.CNO=B.CNO);
--须注意********此题
```
34。
```sql
--解法一：
SELECT A.TNAME,A.DEPART FROM TEACHER A JOIN COURSE B ON A.TNO=B.TNO;
--解法二：
select tname,depart from teacher a where exists
(select * from course b where a.tno=b.tno);
--解法三：
SELECT TNAME,DEPART FROM TEACHER WHERE TNO IN (SELECT TNO FROM COURSE);

--实际分析，第一种揭发貌似更好，至少扫描次数最少。
```
35.
```sql
--解法一：
SELECT TNAME,DEPART FROM TEACHER A LEFT JOIN COURSE B USING(TNO) WHERE ISNUL(B.tno);

--解法二：
select tname,depart from teacher a where not exists
(select * from course b where a.tno=b.tno);
--解法三：
SELECT TNAME,DEPART FROM TEACHER WHERE TNO NOT IN (SELECT TNO FROM COURSE);
NOT IN的方法效率最差，其余两种差不多
```

36.
```sql
SELECT CLASS FROM STUDENT A WHERE SSEX='男' GROUP BY CLASS HAVING COUNT(SSEX)>1;
```
37.
```sql
SELECT * FROM STUDENT A WHERE SNAME not like '王%';
```
38.
```sql
SELECT SNAME,(YEAR(NOW())-YEAR(SBIRTHDAY)) AS AGE FROM STUDENT;
```
39.
```sql
select sname,sbirthday as THEMAX from student where sbirthday =(select min(SBIRTHDAY) 

from student)
union
select sname,sbirthday as THEMIN from student where sbirthday =(select max(SBIRTHDAY) from 

student);
```
40.
```sql
SELECT CLASS,(YEAR(NOW())-YEAR(SBIRTHDAY)) AS AGE FROM STUDENT ORDER BY CLASS DESC,AGE 

DESC;
```
41.
```sql
SELECT A.TNAME,B.CNAME FROM TEACHER A JOIN COURSE B USING(TNO) WHERE A.TSEX='男';
```
42.
```sql
SELECT A.* FROM SCORE A WHERE DEGREE=(SELECT MAX(DEGREE) FROM SCORE B );
```
43.
```sql
SELECT SNAME FROM STUDENT A WHERE SSEX=(SELECT SSEX FROM STUDENT B WHERE B.SNAME='李军');
```
44.
```sql
SELECT SNAME FROM STUDENT A WHERE SSEX=(SELECT SSEX FROM STUDENT B WHERE B.SNAME='李军' )
AND CLASS=(SELECT CLASS FROM STUDENT C WHERE c.SNAME='李军');
```
45.
```sql
--解法一：
SELECT A.* FROM SCORE A JOIN (STUDENT B,COURSE C) USING(sno,CNO) WHERE B.SSEX='男

' AND C.CNAME='计算机导论';
--解法二：
select * from score where sno in(select sno from student where
ssex='男') and cno=(select cno from course
where cname='计算机导论');
```