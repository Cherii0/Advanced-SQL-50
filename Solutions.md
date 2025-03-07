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
