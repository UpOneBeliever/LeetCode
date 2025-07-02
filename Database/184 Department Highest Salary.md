# 184. Department Highest Salary(Medium)

Write a solution to find employees who have the highest salary in each of the departments.

## 1. Oracle

This question is very similar to question 176. You can use Max() or window functions.

    /* Write your PL/SQL query statement below */
    select t2.name Department, t1.name employee, t1.salary
    from (
        select name, salary, rank() over(partition by departmentid order by salary desc) rn,departmentid
        from employee
    ) t1
    left join department t2
    on t1.departmentid = t2.id
    where t1.rn = 1

## 2. Pandas

You can choose two functions to implement, `rank()` `transform()`.

    def department_highest_salary(employee: pd.DataFrame, department: pd.DataFrame) -> pd.DataFrame:
        employee['rn'] = employee.groupby('departmentId')['salary'].rank(ascending=False, method='min')
        res = employee.merge(department, left_on = 'departmentId', right_on = 'id')
        res = res[res['rn']==1]
        return res[['name_y', 'name_x', 'salary']].rename(columns = {'name_y':'Department','name_x':'Employee'})

There are three things to note:

1. Rank() is a function of series

2. The default method of rank is average. When there are multiple rankings with the same value, the average value of the ascending ranking will be given.
   Therefore, the min method is needed here. When method=min, this method is equivalent to rank in Oracle.

3. When using rename() to modify the column name, you must specify the columns parameter


    def department_highest_salary(employee: pd.DataFrame, department: pd.DataFrame) -> pd.DataFrame:
        employee = pd.merge(employee, department, left_on="departmentId", right_on="id",suffixes=('_left', '_right'))
        employee = employee.rename(columns={"name_right": "department", "name_left": "employee"})
        max_salary = employee.groupby("department")["salary"].transform('max')
        return employee.loc[employee["salary"] == max_salary, ["department", "employee", "salary"]]

In Pandas, transform('max') is typically used after a groupby() to compute the maximum value for each group, 
and broadcast that value back to the original rows without reducing the DataFrame size.
