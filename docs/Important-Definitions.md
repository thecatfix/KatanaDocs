# Understanding Key Concepts**


## UNIVERSES

`UNIVERSES` is a dictionary that defines different investment universes. Each universe is an instance of the `Universe` class with specific parameters:
    
    
    UNIVERSES = {
        'EGR': Universe(...),
        'EMD': Universe(...),
        'IGEUR': Universe(...),
        'IGUS': Universe(...),
        'SSA': Universe(...),
    }

**Universe Parameters**

Each `Universe` instance is initialized with parameters that define its characteristics:

1. `name`: A string identifier for the universe.
2. `currencies`: A list of `Currency` enum values.
3. `countries`: Either a `Region` enum value or a list of `Country` enum values.
4. `classifications`: A list of `ClassificationSuperType` enum values.
5. `issue_types`: A list of `IssueSubType` enum values.
6. `payment_categories`: A list of `PaymentCategorySuperType` enum values or tuples of (`PaymentCategorySuperType`, `PaymentCategorySubType`).
7. `min_rating`: A `Rating` enum value representing the minimum acceptable rating.
8. `model_options`: A dictionary of parameters for the model associated with this universe.
9. `max_workers`: An integer specifying the maximum number of parallel workers for processing this universe.

Additional parameters may include:

- `industry_sectors_level1_include`
- `tickers_include`
- `min_amount_outstanding`
- `flags`
- `preferred_series`
- `country_type`
- `industry_sectors_level4_exclude`
- `tickers_exclude`

**Universe Descriptions**

1. **EGR (European Government-Related)**: Focuses on European government-related bonds in EUR.
2. **EMD (Emerging Market Debt)**: Covers bonds from emerging market countries in EUR and USD.
3. **IGEUR (Investment Grade Europe)**: Represents investment-grade corporate bonds in EUR and GBP.
4. **IGUS (Investment Grade US)**: Covers US investment-grade corporate bonds in USD.
5. **SSA (Supranational, Sub-sovereign, and Agency)**: Focuses on high-rated bonds from specific European countries and organizations.

**Source Code Location**
[~/src/generate/universe_definitions.py](https://gitlab.com/katanalabs/katana/-/blob/change/add-domain-model-design/src/generate/universe_definitions.py)

Code imports several classes and enums from custom modules:
    
    
    from components.domain.bond import (ClassificationSubType,
                                        ClassificationSuperType, CountryType,
                                        Currency, IssueSubType,
                                        PaymentCategorySubType,
                                        PaymentCategorySuperType, Rating, Series)
    from components.domain.country import Country, Region
    from components.domain.universe import Universe

**SOVEREIGN_TICKERS**

A list of ticker symbols for sovereign bonds:
    
    
    SOVEREIGN_TICKERS = [
        'DBR', 'BKO', 'OBL', 'BGB', 'FRTR', 'LGB', 'RAGB', 'NETHER', 'RFGB', 'BELG',

**EMD_countries**

A list of `Country` enum values representing Emerging Market Debt countries:
    
    
    EMD_countries = [
        Country.CHN,  # China
        Country.IDN,  # Indonesia
        Country.IND,  # India
A list comprehension that creates a list of all countries not in `EMD_countries`. This ensures that `IGUS_countries` includes only countries that are not classified as Emerging Market Debt, effectively separating them into a distinct group:
        Country.RUS,  # Russia
        Country.ZAF,  # South Africa
        Country.MEX,  # Mexico
        Country.TUR,  # Turkey
        Country.ARG,  # Argentina
        Country.PHL   # Philippines
    ]

**IGUS_countries**

A list comprehension that creates a list of all countries not in `EMD_countries`:
    
    
    IGUS_countries = [c for c in Country if c not in EMD_countries]