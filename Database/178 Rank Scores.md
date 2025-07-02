# 178. Rank Scores(Medium)  

## 1. Oracle  
### 1.1 solution  

    select score, dense_rank() over(order by score desc) as rank
    from scores  

The requirements of this question perfectly match the definition of the dense_rank() function.


## 2. Pandas

### 2.1 solution

If you know the equivalent of dense_rank() in pandas, then it is easy.

    def order_scores(scores: pd.DataFrame) -> pd.DataFrame:
        scores['rank'] = scores['score'].rank(method='dense', ascending=False).astype(int)
        return scores[['score', 'rank']].sort_values(by='rank')
