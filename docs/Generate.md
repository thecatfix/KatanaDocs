# Generate Domain
In the **Generate Domain** of the Katana Domain Model, the process focuses on transforming raw data about bonds and their prices into actionable **Trade Ideas**. This domain is all about filtering, refining, and structuring bond data to generate meaningful trade ideas for financial decision-making. Let's break down the key components, entities, and specifications involved, as well as their roles in this process.

![diagram.drawio.svg](Domain-Model/uploads/e05cce4fcbfcb81d5b9d1df26bd71a76/diagram.drawio.svg)

### Key Entities and Specifications in the Generate Domain:

1. **Bond**:

        - This entity represents a financial instrument (a bond) with key attributes like `ISIN`, `Universe`, and various metadata (e.g., `IssuerName`, `CountryOfRisk`, `RatingCategory`, etc.).
        - In the Generate Domain, bonds are the primary assets under consideration for trade ideas.

2. **BondPrice**:

        - This entity captures the price of a bond at a particular moment in time. It includes attributes like the `ISIN` (identifying the bond), `AsOf` (time of the price record), and the `ZSpread` (a measure of bond risk compared to a risk-free bond).
        - The bond's price history plays a critical role in evaluating trade ideas since pricing data over time provides insights into the bond's risk and potential profitability.

3. **BondSpec** (Specification for Bonds):

        - This is a specification that defines constraints or filters for bonds.
        - **Key attributes**:
                - `MaxMonthsToMature`: Specifies the maximum remaining time to maturity for a bond to be considered.
                - `MinAmountOutstanding`: Ensures the bond has a sufficient amount of outstanding debt to be included (as liquidity is crucial for tradability).
        - The **BondSpec** ensures that only bonds meeting these criteria are included in the generation of trade ideas.

4. **BondPriceSpec** (Specification for Bond Prices):

        - This defines constraints on bond prices.
        - **Key attributes**:
                - `MinAsOf`: Ensures the price data is recent enough to be considered.
                - `MinZSpread` and `MaxZSpread`: These define acceptable risk levels for bonds (via the ZSpread).
        - **BondPriceSpec** ensures that only bonds with acceptable risk and up-to-date price data are included in the generation process.

5. **BondWithPrices** (Value Object):

        - A value object representing a bond along with its associated price history.
        - This combination allows the system to analyze both the bond's static attributes (like `IssuerName` and `RatingCategory`) and its dynamic attributes (like price changes and `ZSpread`) over time.

6. **BondWithPricesSpec** (Specification for Bonds with Price History):

        - This specification filters bonds and their associated price histories based on various metrics.
        - **Key attributes**:
                - `MaxEWMScore` and `MaxDODScore`: These scores represent risk and price volatility metrics, respectively. Bonds that exceed these thresholds are excluded.
                - `MinLatestAsOf`: Ensures that the price data is recent.
                - `MinNrConsecutivePrices`: Requires bonds to have a sufficient history of consecutive price records.
        - The **BondWithPricesSpec** ensures that only bonds with stable price histories and acceptable risk are considered.

7. **TradeIdeaCandidate** (Entity):

        - This entity represents potential trade ideas that are yet to be finalized or confirmed.
        - **Key attributes**:
                - `ID`: Uniquely identifies the trade idea candidate.
                - `IsBookmarked`: A boolean indicating if this trade idea has been flagged for special attention.
                - Various metrics such as `SpreadLastValue`, `SpreadMean`, `SpreadZScore`, and `PearsonScore` are used to evaluate the viability of the trade idea based on bond price spreads and other financial indicators.

8. **TradeIdeaCandidateSpec** (Specification for Trade Idea Candidates):

        - This specification defines the criteria for generating trade idea candidates.
        - **Key attributes**:
                - `BookmarksExempt`: A boolean that determines if bookmarks influence the trade idea generation process.
                - `MinSpreadMeanReversion` and `MinNrOverlappingPrices`: These attributes ensure the spread between bond prices shows a trend or reversion to the mean, and that there is sufficient overlap in pricing history between the two bonds in the trade idea.
                - `MinPearsonScore`: A threshold to ensure a minimum correlation between the bonds in the trade idea.
        - **TradeIdeaCandidateSpec** ensures that only trade ideas with sufficiently reliable financial characteristics are generated.

9. **TradeIdea** (Entity):

        - This entity represents a final, actionable trade idea, created from one or more trade idea candidates that passed all the specified criteria.
        - **Key attributes**:
                - `ID`: A unique identifier for the trade idea, typically derived from the bonds involved.
                - The `BondBuy` and `BondSell` attributes specify which bonds are to be purchased and sold, respectively.
        - A **TradeIdea** is generated after all the filtering and analysis has been completed and is ready for evaluation by traders or algorithms.

10. **TradeIdeaSpec** (Specification for Trade Ideas):

        - This is the final layer of specification that trade ideas must pass to be considered viable.
        - **Key attributes**:
                - `MinSimilarityScore`, `MinPearsonScore`, and `MinZScoreForIdea`: These metrics ensure that only trade ideas with strong financial indicators are selected. For example, the Pearson Score measures the correlation between bond price movements.
                - `NeedsToBeIdea`: A boolean indicating whether the candidate trade must be generated as a trade idea.
        - **TradeIdeaSpec** ensures that only the best trade ideas, based on strong financial metrics, are selected for action.

### Generate Process Flow:

1. **Filter Bonds**:

        - Bonds are first filtered through the **BondSpec**. Only bonds that meet criteria such as maximum months to maturity and a minimum amount of outstanding debt are considered.

2. **Filter Bond Prices**:

        - Once the bonds are selected, their price histories are filtered using the **BondPriceSpec**, ensuring that only bonds with recent and acceptable price data (such as ZSpread) move forward in the process.

3. **Analyze Bond and Price History**:

        - The **BondWithPricesSpec** further narrows down bonds based on their price volatility (`EWMScore` and `DODScore`), ensuring that only bonds with stable price histories and minimal volatility are included.

4. **Generate Trade Idea Candidates**:

        - Trade idea candidates are generated from pairs of bonds, where one bond is considered for buying and the other for selling.
        - The **TradeIdeaCandidateSpec** applies filters to ensure that the bond pairs have a reasonable spread relationship (`SpreadMeanReversion`), sufficient price overlap, and correlation (`PearsonScore`).

5. **Refine Trade Ideas**:

        - After trade idea candidates are created, they are further refined based on **TradeIdeaSpec**, which looks for the strongest candidates by evaluating financial metrics such as similarity scores and Z-scores.

6. **Generate Final Trade Ideas**:

        - Once all filters and specifications have been applied, final **TradeIdeas** are generated. These ideas represent actionable trading opportunities, where one bond is suggested for purchase and the other for sale.

### Interpretation of the Generate Domain:

- The Generate Domain focuses on turning raw bond and bond price data into well-analyzed, viable trade ideas.

- **BondSpec** and **BondPriceSpec** filter out bonds and price data that do not meet the necessary criteria, while **BondWithPricesSpec** ensures that only bonds with stable price histories are considered.

- **TradeIdeaCandidateSpec** analyzes bond pairs and their pricing relationships to generate trade idea candidates, and **TradeIdeaSpec** applies final filtering to produce actionable **Trade Ideas**.

- This process is highly systematic, leveraging a series of specifications to ensure that the resulting trade ideas are backed by strong financial metrics, minimizing risk, and maximizing potential for profitable trades.
