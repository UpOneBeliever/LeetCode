# 177. Nth Highest Salary(Medium)


> This question is an advanced question of question 176.

Write a solution to find the nth highest distinct salary from the Employee table. If there are less than n distinct salaries, return null.


## 1. Solutions


- Oracle

There is no way to use max() in this question, so we can only use dense_rank() for unified ranking.


    CREATE FUNCTION getNthHighestSalary(N IN NUMBER) RETURN NUMBER IS
    result NUMBER;
    BEGIN
        /* Write your PL/SQL query statement below */
        select max(salary) into result
        from (select salary, dense_rank() over(order by salary desc) as rn from employee)
        where rn = N;
        RETURN result;
    END;




- Pandas

We cannot simply use Max() twice as in question 176, because we are not sure what the salary ranking is.  

Use a different approach and use the sorting method. At the same time, pay attention to the value of N. If it is less than 1 or greater than the number of deduplicated salaries, return None.


    def nth_highest_salary(employee: pd.DataFrame, N: int) -> pd.DataFrame:
        employee = employee.sort_values(by='salary', ascending=False).drop_duplicates(subset='salary')
        print(employee)
        if len(employee) < N or N<1:
            return pd.DataFrame({f'getNthHighestSalary({N})':[None]})
        else:
            return pd.DataFrame({f'getNthHighestSalary({N})':[employee['salary'].iloc[N-1]]}) # index start from 0


## 2. Notes

### 2.1 The usage of subset inside drop_duplicates()  
The subset parameter in pandas.DataFrame.drop_duplicates() **tells Pandas which column(s) to use to identify duplicates** — instead of using all columns by default.
