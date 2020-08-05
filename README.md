# Tasks-SQL-Tableau

There are 4 Tasks given of employees data from MySQL Database. We shoud write a query code to retrive result from given Task, then after we should explot the result in any file format like csv or xlsx, finally to observe the tasks given we can visualize in Tableau by connecting the file after explot is done.

Here, I will show the given Tasks and Solution done i.e query code and tableau visualization

Task-1:-
1) Create a visualization that provides a breakdown b/w male and female employees working in the company each year, starting from 1990?

solution in SQL:-
SELECT 
    YEAR(d.from_date) AS calender_year,
    e.gender,
    COUNT(e.emp_no) AS num_of_employees
FROM
    t_employees e
        JOIN
    t_dept_emp d ON d.emp_no = e.emp_no
GROUP BY calender_year , e.gender
HAVING calender_year >= 1990;

Task-2:-
2) Compare the number of male managers to the number of female managers from different departments for each year starting from 1990?

solution in SQL:-
SELECT 
    d.dept_name,
    ee.gender,
    dm.emp_no,
    dm.from_date,
    dm.to_date,
    e.calendar_year,
    CASE
        WHEN
            YEAR(dm.to_date) >= e.calendar_year
                AND YEAR(dm.from_date) <= e.calendar_year
        THEN
            1
        ELSE 0
    END AS ACTIVE
FROM
    (SELECT 
        YEAR(hire_date) AS calendar_year
    FROM
        t_employees
    GROUP BY calendar_year) e
        CROSS JOIN
    t_dept_manager dm
        JOIN
    t_departments d ON dm.dept_no = d.dept_no
        JOIN
    t_employees ee ON dm.emp_no = ee.emp_no
ORDER BY dm.emp_no , calendar_year;

Task-3:-
3) Compare the average salary if female versus male employees in the entire company until year 2002, and add a filter allowing you to see the per each departments?

solution in SQL:-
SELECT 
    e.gender,
    d.dept_name,
    ROUND(AVG(s.salary), 2) AS salary,
    YEAR(s.from_date) AS calendar_year
FROM
    t_salaries s
        JOIN
    t_employees e ON s.emp_no = e.emp_no
        JOIN
    t_dept_emp de ON de.emp_no = e.emp_no
        JOIN
    t_departments d ON d.dept_no = de.dept_no
GROUP BY d.dept_no , e.gender , calendar_year
HAVING calendar_year <= 2002
ORDER BY d.dept_no;

Task-4:-
4) Create an SQL stored procedure that will allow you to obtain the average male and female salary per department within a certain salary range. Let this range be defined by two values the user can insert when calling the procedures. Finally visualize the obtain result-set in Tableau as a double bar chart?

solution in SQL:-
DROP PROCEDURE IF EXISTS filter_salary;
DELIMITER $$
CREATE PROCEDURE filter_salary (IN p_min_salary FLOAT, IN p_max_salary FLOAT)
BEGIN SELECT e.gender, d.dept_name, AVG(s.salary) AS avg_salary
FROM t_salaries s JOIN t_employees e ON s.emp_no = e.emp_no JOIN t_dept_emp de ON de.emp_no = e.emp_no
JOIN t_departments d ON d.dept_no = de.dept_no
WHERE s.salary BETWEEN p_min_salary AND p_max_salary
GROUP BY d.dept_no, e.gender;
END$$
DELIMITER ;

CALL filter_salary(50000,90000);
