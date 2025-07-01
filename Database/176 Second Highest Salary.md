# 176. Second Highest Salary(Medium)
Write a solution to find the second highest distinct salary from the Employee table. If there is no second highest salary, return null (return None in Pandas).


## 1. Solutions
- Oracle


The first reaction when seeing the question is to use window functions, there are three common window functions: row_number(), rank(), dense_rank().The differences between the three window functions will be discussed below. Here we use dense_rank() for processing. The code is as follows.


    select max(salary) SecondHighestSalary
    from
    (select salary, dense_rank() over(order by salary desc) as rn from Employee)
    where rn=2

There is actually a more efficient solution to this problem.

    select max(salary) SecondHighestSalary
    from employee 
    where salary < (select max(salary) from employee)

- Pandas


Use MAX():


    def second_highest_salary(employee: pd.DataFrame) -> pd.DataFrame:
        fir = max(employee['salary'], default=None)
        sec = max(employee[employee['salary']!=fir]['salary'], default=None)
        return pd.DataFrame({'SecondHighestSalary':[sec]})
  

## 2. Notes

### 2.1 `ROW_NUMBER()` vs `RANK()` vs `DENSE_RANK()`

| Value | `ROW_NUMBER()` |  `RANK()`       | `DENSE_RANK()` |
| ----- | -------------- |  -------------- | ---------------|
| 100   | 1              |  1              | 1              |
| 100   | 2              |  1              | 1              |
| 90    | 3              |  3              | 2              |


### 2.2 `ROW_NUMBER()` vs `MAX()`

✅ MAX() is usually faster than ROW_NUMBER()

- MAX() is an aggregate function. It simply scans a column and returns the maximum value.

  - Complexity: O(n) for a full scan, sometimes less with indexes.

  - Simple operation, no need to sort or partition unless used with GROUP BY.

- ROW_NUMBER() is a window function. It assigns a number to each row based on ordering, and optionally partitioning.

  - Complexity: At least O(n log n), due to sorting required by the ORDER BY clause.

  - Often requires a sort or reordering even if indexes exist.
 

### 2.3 Usage of Pandas-max()

❌ This raises: ValueError: max() arg is an empty sequence

    nums = []
    max(nums) 

✅ Will return 0 if list is empty

    result = max(nums, default=0)  
  
