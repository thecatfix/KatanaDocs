# Idea Candidate Creation
[idea.py](https://gitlab.com/katanalabs/katana/-/blob/a4f73f37a8c9a1c0baf2e614125739d41ac0d908/src/generate/components/domain/idea.py)

This module defines classes related to investment ideas, including IdeaCandidate and Idea.

It provides functionality for creating, manipulating, and converting idea objects,
which are used in the context of bond trading and analysis.

## Classes

### IdeaCandidate

Represents a candidate for an investment idea.

This class encapsulates information about potential bond trades,
including the bonds to buy and sell, and various metrics related to the trade.

#### Attributes

- `idea_id` (str): Unique identifier for the idea candidate.
- `bond_buy` (BondWithPricingHistory): The bond to buy in this idea.
- `bond_sell` (BondWithPricingHistory): The bond to sell in this idea.
- `bookmark` (bool): Whether this idea candidate is bookmarked.
- `curve_trade` (bool): Whether this is a curve trade.
- `spread_last_value` (Optional[float]): The last value of the spread.
- `spread_last_zscore` (Optional[float]): The last z-score of the spread.
- `spread_mean` (Optional[float]): The mean of the spread.
- `spread_mean_reversion` (Optional[float]): The mean reversion of the spread.
- `similarity_score` (Optional[float]): The similarity score of the idea.
- `pearson_score` (Optional[float]): The Pearson correlation score.

### Idea

Represents a fully formed investment idea.

This class encapsulates all information related to an investment idea,
including the bonds to buy and sell, various metrics, and flags indicating
the idea's status.

#### Attributes

- `idea_id` (str): Unique identifier for the idea.
- `alert` (bool): Whether this idea should trigger an alert.
- `new` (bool): Whether this is a new idea.
- `bookmark` (bool): Whether this idea is bookmarked.
- `alternative` (bool): Whether this is an alternative idea.
- `curve_trade` (bool): Whether this is a curve trade.
- `shortening` (bool): Whether this trade involves shortening.
- `bond_buy` (Bond): The bond to buy in this idea.
- `bond_sell` (Bond): The bond to sell in this idea.
- `spread_last_value` (Optional[float]): The last value of the spread.
- `spread_last_zscore` (Optional[float]): The last z-score of the spread.
- `spread_mean` (Optional[float]): The mean of the spread.
- `spread_mean_reversion` (Optional[float]): The mean reversion of the spread.
- `spread_diff` (Optional[float]): The difference in spreads.
- `similarity_score` (Optional[float]): The similarity score of the idea.
- `pearson_score` (Optional[float]): The Pearson correlation score.
- `adf_score` (Optional[float]): The ADF (Augmented Dickey-Fuller) test score.

#### Methods

##### from_candidate

```python
@staticmethod
def from_candidate(
    candidate: IdeaCandidate,
    zspread_to_use: str,
    alert: bool,
    new: bool,
    alternative: bool,
    run_id: str,
    run_date: datetime,
    universe: str,
    adf_score: Optional[float],
    similarity_score: Optional[float] = None,
    pearson_score: Optional[float] = None,
) -> Idea:
````