[LeetCode](https://leetcode.com/) a website for preparing for coding interviews. The platform provides coding and algorithms tasks for users to practice.

### 📌 1412. Find the Quiet Students in All Exams
[Question : ](https://leetcode.com/problems/find-the-quiet-students-in-all-exams/?envType=study-plan-v2&envId=premium-sql-50) Write a solution to report the
students (student_id, student_name) being quiet in all exams. Do not return the student who has never taken any exam.

```sql
SELECT * FROM Student 
WHERE student_id NOT IN (
SELECT DISTINCT T.student_id FROM (
	SELECT 
			S.*,
			DENSE_RANK() OVER (PARTITION BY  E.exam_id ORDER BY E.score) AS rank,
			DENSE_RANK() OVER (PARTITION BY E.exam_id ORDER BY E.score DESC) AS rank_rev

	FROM 
			Exam AS E
	FULL OUTER JOIN Student AS S ON S.student_id = E.student_id
) AS T
WHERE
	rank = 1 OR rank_rev = 1
)
ORDER BY student_id
```

### 📌 184. Department Highest Salary
[Question : ](https://leetcode.com/problems/department-highest-salary/description/?envType=study-plan-v2&envId=premium-sql-50)
Write a solution to find employees who have the highest salary in each of the departments.

```sql
SELECT
	D.name AS Department,
	empRank.name AS Employee,
	empRank.salary AS Salary
FROM
(SELECT *,
	DENSE_RANK() OVER (PARTITION BY departmentId ORDER BY salary DESC) AS rank_
FROM 
	Employee) AS empRank
JOIN Department D ON empRank.departmentId = D.id
WHERE rank_ = 1
```

### 📌 1077. Project Employees III
[Question : ](https://leetcode.com/problems/project-employees-iii/description/?envType=study-plan-v2&envId=premium-sql-50)
Write a solution to report the most experienced employees in each project. In case of a tie, report all employees with the maximum number of experience years.

```sql
;WITH ranking AS (

	SELECT
		project_id,
		P.employee_id,
		experience_years,
		DENSE_RANK() OVER (PARTITION BY project_id ORDER BY experience_years DESC) AS most_exp
	FROM
		Project AS P
	LEFT JOIN
		Employee AS E ON P.employee_id = E.employee_id
)
SELECT
	project_id,
	employee_id
FROM
	ranking
WHERE
	most_exp = 1
```

###  📌 1596. The Most Frequently Ordered Products for Each Customer
[Question : ](https://leetcode.com/problems/the-most-frequently-ordered-products-for-each-customer/description/?envType=study-plan-v2&envId=premium-sql-50)
Write a solution to find the most frequently ordered product(s) for each customer.

```sql
;WITH ranking_ AS (
	SELECT
		*,
		DENSE_RANK() OVER (PARTITION BY customer_id ORDER BY count_ DESC) AS ranking_
	FROM	(	SELECT DISTINCT 
				customer_id,
				product_id,
				COUNT(*) OVER (PARTITION BY customer_id, product_id) AS count_
			FROM Orders
		) AS temp
) 
SELECT 
	R.customer_id,
	R.product_id,
	P.product_name
FROM
	ranking_ AS R
LEFT JOIN Products AS P ON R.product_id = P.product_id
WHERE
	ranking_ = 1
```

### 📌 1709. Biggest Window Between Visits
[Question : ](https://leetcode.com/problems/biggest-window-between-visits/description/?envType=study-plan-v2&envId=premium-sql-50)
Write a solution that will, for each user_id, find out the largest window of days between each visit and the one right after it (or today if you are considering the last visit).

```sql
;WITH diff AS (
	SELECT 
		*,
		DATEDIFF(day, visit_date, COALESCE(LEAD(visit_date)
		 OVER (PARTITION BY user_id ORDER BY visit_date), '2021-1-1')) AS diff
	FROM 
		UserVisits
)
SELECT
	user_id,
	MAX(diff) AS biggest_window 
FROM
	diff
GROUP BY
	user_id
```

### 📌 176. Second Highest Salary
[Question : ](https://leetcode.com/problems/second-highest-salary/description/)
Write a solution to find the second highest distinct salary from the Employee table. If there is no second highest salary, return null (return None in Pandas).

```sql

;WITH ranking AS (
	SELECT 
			*,
			DENSE_RANK() OVER (ORDER BY salary DESC) AS ranking
	FROM
			Employee
)
SELECT COALESCE((SELECT TOP 1
	salary 
FROM
	ranking
WHERE
	ranking.ranking = 2), NULL) AS SecondHighestSalary
```
