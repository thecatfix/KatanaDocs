# The Ingest Domain

![diagram.drawio.svg](Domain-Model/uploads/b501a999beb7c2f5b66fd303c13a587c/diagram.drawio.svg)

The **Ingest Domain** in the Katana Domain Model represents the first stage in the pipeline where raw data (such as bonds and their prices) is collected, processed, and organized into a structured form that can be further analyzed and used in generating trade ideas. The goal of the Ingest process is to ensure that only relevant and high-quality data is passed on for further analysis, making it an essential part of the data preparation process.

### Key Concepts and Entities in the Ingest Domain:

1. **Bond** (Entity):

        - The primary financial instrument considered in the Ingest process. Bonds have several key attributes, such as:
                - `ISIN`: The unique identifier for each bond.
                - `Universe`: The financial universe or category to which the bond belongs (e.g., "USD Investment Grade," "EUR High Yield").
                - Metadata like `IssuerName`, `CountryOfRisk`, `RatingCategory`, `MaturityCategory`, etc.
        - Bonds are the central data point around which the entire process revolves.
2. **BondPrice** (Entity):

        - This represents the price of a bond at a specific point in time. Key attributes include:
                - `ISIN`: Links the price to the bond it belongs to.
                - `AsOf`: The timestamp indicating when the price was recorded.
                - `ZSpread`: A measure of risk for the bond compared to risk-free instruments.
        - Bond prices are essential for evaluating trade ideas and are collected for further analysis.
3. **BondSpec** (Specification):

        - A set of rules or criteria that filters bonds based on certain characteristics before they can be passed on for further processing.
        - For example, **BondSpec** might require bonds to have a minimum amount of outstanding debt or a maximum number of months to maturity.
        - This ensures that only bonds meeting specific financial standards are included in the downstream processes.
4. **BondPriceSpec** (Specification):

        - Similarly, this specification filters bond prices based on criteria such as:
                - The timestamp (`MinAsOf`), ensuring the price data is recent.
                - The `ZSpread`, ensuring that only bonds with acceptable risk levels (via their ZSpread values) are included.
5. **Universe** (Entity):

        - A collection or category of bonds. Universes help group bonds based on specific characteristics, such as geographical location, risk profile, or bond type (e.g., "USD High Yield" or "EUR Investment Grade").

### Ingest Domain Process Breakdown:

#### 1. **Data Collection**:

- **Bond and BondPrice Data**: The Ingest process starts by pulling in raw bond and bond price data from various sources. This data could come from financial data providers, exchanges, or internal systems.
- The data includes key bond attributes (e.g., ISIN, Issuer, Country of Risk) and bond price records with associated timestamps and ZSpread values.

#### 2. **Filtering Bonds (BondSpec)**:

- Once the data is ingested, the **BondSpec** comes into play. This specification filters bonds to ensure that only those that meet certain criteria are passed on.

- For example, **BondSpec** might filter out bonds that have too much time until maturity (`MaxMonthsToMature`) or that have insufficient liquidity (`MinAmountOutstanding`).

- This stage ensures that only bonds that are tradable and relevant for analysis are included in the process. Bonds that fail to meet the criteria are excluded.

This happens in the 
[universes.py file in the generate/components](https://gitlab.com/katanalabs/katana/-/blob/master/pipelines/generate/components/filters/universes.py)

#### 3. **Filtering Bond Prices (BondPriceSpec)**:

- After the bonds have been filtered, the **BondPriceSpec** applies to the associated bond prices. This specification ensures that the bond price data is up-to-date and within acceptable risk limits.

- For example, **BondPriceSpec** might exclude prices that are too old (`MinAsOf`) or prices with a ZSpread value that is too high or too low (`MinZSpread`, `MaxZSpread`).
- This ensures that only current, relevant price data is considered for each bond.

#### 4. **Assigning Bonds to Universes**:

- Bonds that pass the filtering process are assigned to specific **Universes** based on their characteristics. A universe represents a category or set of bonds that share similar traits.

- For example, bonds might be grouped into universes like "USD Investment Grade" or "EUR High Yield." These universes help categorize the bonds and enable further downstream analysis within specific financial contexts.

#### 5. **Value Object Assignment**:

- The Ingest process also involves assigning various **Value Objects** to the bonds. These value objects provide additional metadata for the bonds, such as:

        - **Classification**: The bond's sector (e.g., Corporate, Government).
     
        - **Country**: The bond's country of risk.
 
        - **Currency**: The currency in which the bond is issued.

        - **Rating**: The credit rating of the bond.

- These value objects are non-unique and provide contextual information to help categorize the bonds within the universes. For example, a bond issued by a corporate entity in the USA would have a classification of "Corporate" and a country of risk of "USA."

#### 6. **Specifications for Universes (UniverseSpec)**:

- The **UniverseSpec** defines criteria for including bonds in specific universes. For example, a universe might only include bonds with a minimum amount of outstanding debt or bonds issued in certain countries or sectors.
- Bonds that do not meet the criteria of a particular universe are excluded from that universe.
- This helps to group bonds effectively and ensures that universes contain only the bonds that meet the desired financial characteristics.

#### 7. **Finalizing Ingested Data**:

- Once the bonds have been filtered, their prices validated, and they are assigned to universes, the ingested data is ready for downstream processes.
- This data, now organized and filtered, can be passed on to the **Generate Domain**, where it is further analyzed to produce actionable trade ideas.

### Key Relationships in the Ingest Domain:

- **Bond to BondPrice**:
        - Each bond can have multiple prices over time, and these prices are filtered by **BondPriceSpec** to ensure only relevant price records are considered.
- **Bond to Universe**:
        - Bonds are assigned to specific universes based on their characteristics and the criteria defined in **UniverseSpec**.
- **Value Objects to Bonds**:
        - Value objects such as **Classification**, **Country**, **Currency**, and **Rating** are assigned to bonds to provide additional context for how the bonds should be categorized in the universes.
- **BondSpec and BondPriceSpec**:
        - These specifications play a critical role in filtering the raw bond data to ensure that only bonds meeting the criteria are passed on for further processing.

### Purpose of the Ingest Domain:

- **Filtering and Structuring Data**:
        - The Ingest Domain serves to organize and filter raw bond data, ensuring that only relevant, high-quality bonds and prices are included in the downstream analysis.
- **Categorization and Contextualization**:
        - By assigning bonds to universes and enriching them with value objects, the Ingest Domain helps create a structured representation of the bond market that can be easily analyzed.
- **Prepares for Trade Idea Generation**:
        - The structured, filtered data is then ready to be passed on to the Generate Domain, where it will be used to create **Trade Ideas** based on the bonds' price trends, risk levels, and other financial factors.

### Interpretation:

The **Ingest Domain** is a critical step in the bond trading system, as it ensures that the data fed into later stages is clean, organized, and relevant. By filtering out irrelevant bonds and prices, enriching bonds with value objects, and categorizing them into universes, the Ingest Domain ensures that the subsequent analysis is based on high-quality data. This process lays the foundation for generating accurate and actionable **Trade Ideas** in the later stages.
