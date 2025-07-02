# 180. Consecutive Numbers(Medium)


## 1. Oracle

There are two methods in Oracle. One is to use the ID column for left join, and the other is to use only the num column for offset.

    select distinct t1.num ConsecutiveNums
    from logs t1
    left join logs t2
    on t1.id = t2.id-1
    left join logs t3
    on t1.id = t3.id-2
    where t1.num = t2.num and t1.num = t3.num

    select distinct num as ConsecutiveNums 
    from (select num, 
      lag(num,1) over (order by id) as prev_num,
      lead(num,1) over (order by id) as next_num from logs) x
    where num = prev_num and num = next_num;

In the above code, LEAD (get the value of the next row), LAG (get the value of the previous row).

## 2. Pandas

First, let's take a look at how to use Python's basic functions.The question requires returning a number that appears three times. 
You can do two staggered subtractions and record the positions of 0 in the two subtraction results. The intersection of the positions is the number you want to find.

    def consecutive_numbers(logs: pd.DataFrame) -> pd.DataFrame:
        nums = logs['num'].to_numpy()
        num1 = nums[:-1] - nums[1:]
        num2 = num1[:-1] - num1[1:]
        zero_indices1 = set(np.where(num1==0)[0])
        zero_indices = set(np.where(num2==0)[0])
        res = list(dict.fromkeys(nums[list(zero_indices&zero_indices1)]))
        return pd.DataFrame({'ConsecutiveNums':res})

In the above code, `list(dict.fromkeys())` can realize list deduplication and keep element position.

If you want to use pandas' built-in methods, there is a concise way to write it using variance, but when the data is large, it will increase the computational complexity.

    def consecutive_numbers(logs: pd.DataFrame) -> pd.DataFrame:
        logs['var'] = logs.num.rolling(window=3).var()
        return pd.DataFrame(data = {'ConsecutiveNums' : logs.query('var == 0').num.unique()})

In the above code,`rolling()` performs a rolling variance calculation over a window of size 3. When the three elements in the window are equal, the variance is 0.
