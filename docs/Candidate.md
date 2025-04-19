## Breakdown of a candidate

Based on the usage in the `StationarityModel` class, a candidate (represented by the `IdeaCandidate` class) is an object that encapsulates a potential investment idea. Here's a breakdown of what we can infer about a candidate:

1. Structure:

    - It contains two bond objects: `bond_buy` and `bond_sell`, representing the bonds to buy and sell in the investment idea.
    - Each bond has properties like `country_of_risk`, `industry_group`, `rating`, and `maturity_date`.
2. Properties:

    - `similarity_score`: An optional pre-computed similarity score.
    - `bookmark`: A boolean flag indicating if the idea is bookmarked.
    - `curve_trade`: A boolean indicating if it's a curve trade.
    - `spread_last_zscore`: The last z-score of the spread.
    - `spread_mean_reversion`: The mean reversion of the spread.
3. Purpose:

    - It represents a potential pair trade or investment opportunity.
    - It contains all the necessary information for the model to evaluate and make predictions about the investment idea.
4. Usage in the Model:

    - The model computes similarities between the buy and sell bonds.
    - It uses the candidate's properties to determine whether to trigger alerts or mark as alternatives.
5. Flexibility:

    - The candidate structure allows for easy comparison between two bonds.
    - It can accommodate various types of trades, including curve trades and bookmarked ideas.

6. Data Encapsulation:

    - It encapsulates all relevant data about an investment idea in a single object.
    - This makes it easier to pass around and analyze potential trades.

In summary, a candidate is a comprehensive representation of a potential investment idea, containing all the necessary information for the stationarity model to analyze and make predictions about its viability as a trade opportunity.

## What are the properties that create IdeaCandidate?

Based on the code provided in the `stationarity_model.py` file, we can infer some of the properties of `IdeaCandidate`, but we don't have the full class definition. However, from the usage in the `_compute_similarity` method, we can deduce that `IdeaCandidate` has at least the following properties:

1. `bond_buy`: An object representing the bond to buy, which has the following attributes:

    - `country_of_risk`
    - `industry_group`
    - `rating`
    - `maturity_date`
2. `bond_sell`: An object representing the bond to sell, with the same attributes as `bond_buy`.

3. `similarity_score`: An optional float value representing the pre-computed similarity score.

4. `bookmark`: A boolean indicating whether the idea is bookmarked.

5. `curve_trade`: A boolean indicating whether it's a curve trade.

Additionally, from the `predict` method, we can infer a few more properties:

6. `spread_last_zscore`: An optional float representing the last z-score of the spread.

7. `spread_mean_reversion`: An optional float representing the mean reversion of the spread.

To get a complete and accurate list of all properties that create an `IdeaCandidate`, we would need to see the actual class definition, which is not present in the provided code snippet. The properties mentioned above are those that are used or referenced within the `StationarityModel` class, but there could be additional properties not visible in this context.

## What is a bookmark?

In the context of the `IdeaCandidate` class and the stationarity model, a "bookmark" is likely a flag or indicator used to mark specific investment ideas for further attention or quick access. Here's a more detailed explanation:

1. Purpose: A bookmark allows users or algorithms to highlight particular investment ideas that are of special interest, without necessarily changing their analytical properties.

2. Implementation: It's represented as a boolean property (`bookmark`) in the `IdeaCandidate` class. When `True`, it indicates that the idea has been bookmarked.

3. Usage in the model: In the `predict` method of the `StationarityModel`, we see:
    
        if ((similarity_score and similarity_score >= self.similarity_threshold)
            or candidate.bookmark or candidate.curve_trade):
        alert = True

This suggests that bookmarked ideas are treated with special consideration, potentially triggering alerts even if they don't meet other criteria.

4. Possible applications:

    - Marking ideas for review by human analysts
    - Flagging ideas that meet certain criteria outside of the standard model calculations
    - Allowing users to save ideas they find interesting for later examination
5. Flexibility: The bookmark feature adds a manual or rule-based selection mechanism to complement the algorithmic selection of investment ideas.

It's important to note that without access to the full codebase or documentation, this explanation is based on common practices and the context provided in the `StationarityModel` class. The exact functionality and purpose of bookmarks may vary depending on the specific implementation and business requirements of the system.


## Detailed breakdown of the `predict` method in the `StationarityModel` class:
    
    
    def predict(
        self,
        candidate: IdeaCandidate,
        opts: Dict[str, Any] = None,
    ) -> ModelResult:

1. Input Parameters:

    - `candidate`: An `IdeaCandidate` object representing the investment idea to evaluate.
    - `opts`: An optional dictionary for additional options (not used in the method body).
2. Similarity Score Calculation:
    
        similarity_score = (
        candidate.similarity_score
        if candidate.similarity_score is not None
        else self._compute_similarity(candidate))

    - Uses the pre-computed similarity score if available, otherwise calculates it.
3. Initial Variable Setup:
    
        pearson_score = 0.99
    adf_score = 1.0e-6
    alert = False
    alternative = False

    - Sets default values for Pearson score, ADF score, alert, and alternative flags.
4. Alert Determination:
    
        if ((similarity_score and similarity_score >= self.similarity_threshold)
            or candidate.bookmark or candidate.curve_trade):
        alert = True

    - Sets `alert` to `True` if:
        - The similarity score exists and is above the threshold, or
        - The candidate is bookmarked, or
        - It's a curve trade.
5. Alternative Determination:
    
        if (
        alert
        and (
            (
                candidate.spread_last_zscore is not None
                and
                candidate.spread_last_zscore < self.min_zscore_to_alert
            )
            or
            (
                candidate.spread_mean_reversion is not None
                and
                candidate.spread_mean_reversion < self.min_zspread_to_alert
            )
        )
    ):
        alert = False
        alternative = True

    - If an alert was triggered, it checks additional conditions:
        - If the last z-score is below the minimum threshold, or
        - If the mean reversion is below the minimum z-spread threshold.
    - If either condition is true, it changes the idea from an alert to an alternative.
6. Return Result:
    
        return ModelResult(
        alert=alert,
        alternative=alternative,
        similarity_score=similarity_score,
        pearson_score=pearson_score,
        adf_score=adf_score,
    )

    - Returns a `ModelResult` object with the computed values.

Key Points:

- The method primarily decides whether to trigger an alert or mark the idea as an alternative.
- It uses pre-computed scores when available, falling back to calculations if necessary.
- The Pearson and ADF scores are set to constant values, suggesting they might be placeholders for future implementations.
- The method considers multiple factors: similarity score, bookmarks, curve trades, z-scores, and mean reversion.

This method encapsulates the core decision-making logic of the stationarity model, determining how to classify and prioritize investment ideas based on various criteria.



