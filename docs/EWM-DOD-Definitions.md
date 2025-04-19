# Tolerance Metrics `ewm_tolerance` and `dod_tolerance`

Parameters `ewm_tolerance` and `dod_tolerance` are used to control the sensitivity or tolerance levels of specific financial metrics when evaluating bonds and their prices over time. These parameters typically play a role in filtering bonds based on their historical performance, particularly in relation to price stability and volatility.

## Definitions

1. **ewm_tolerance** (Expected Weighted Average Mean Tolerance):

                - **EWM** stands for **Exponentially Weighted Moving Average**. This is a statistical technique used to assign greater weight to more recent data points while discounting older data.
                - **ewm_tolerance** sets the threshold for how much variation or deviation is allowed in a bond's exponentially weighted average price movement. If the bond's price volatility (as measured by EWM) exceeds this tolerance level, the bond may be excluded from further consideration in the trade idea generation process.
                - **Purpose**: To ensure that bonds with stable, predictable price trends are considered for trade ideas. Bonds with excessive price swings or volatility are filtered out by setting a lower tolerance for price fluctuations.

                **Example**: If `ewm_tolerance` is set to 5.0, this means that the bond's price volatility, as calculated using the EWM method, must be within a 5% deviation to be included in the analysis. Bonds with volatility beyond this threshold would be excluded.

2. **dod_tolerance** (Day Over Day Tolerance):

                - **DOD** stands for **Day Over Day** and refers to changes in a bond's price from one day to the next.
                - **dod_tolerance** defines the allowable threshold for day-over-day price changes. If the bond's day-over-day price fluctuations exceed the tolerance level, the bond might be considered too volatile and will be excluded from further analysis.
                - **Purpose**: This tolerance ensures that only bonds with relatively stable day-to-day price movements are considered. Large or erratic day-over-day price swings are generally seen as a sign of high risk, and bonds exceeding this tolerance are filtered out.

                **Example**: If `dod_tolerance` is set to 5.0, the bond's day-to-day price fluctuations must stay within 5% for the bond to be included in the analysis. Bonds with daily price changes beyond this threshold would be excluded from further consideration.

## Why Are These Tolerances Important?

Both **ewm_tolerance** and **dod_tolerance** are designed to filter out bonds that exhibit too much price volatility. The goal is to focus on bonds with more stable price patterns, which are often preferred in trade ideas because they carry less risk. By setting thresholds for acceptable levels of volatility, the system can narrow down the pool of bonds to those that are more predictable and less likely to be affected by sudden price swings.

## Application in the Generate Process

- These tolerances are applied as part of the **BondWithPricesSpec**, which filters bonds with associated pricing data. If the bond's price volatility, as measured by the EWM and DOD scores, exceeds the defined tolerance levels, that bond is excluded from the trade idea generation process.
- Bonds that meet both the **ewm_tolerance** and **dod_tolerance** thresholds are considered more stable and are passed on for further evaluation as **TradeIdeaCandidates**.
```