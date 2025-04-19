# Classification Filtering in Bonds

The **Classification Filtering** implemented in the method `has_correct_classification` ensures that bonds are filtered based on their **classification** attributes, which are usually defined in terms of a **super type** and a **sub type**. These classifications represent how a bond is categorized within a financial domain, such as its asset class, industry sector, or security type.

## Breakdown of the `has_correct_classification` Filtering Process

### 1. **Super Type and Sub Type**

- Every bond can be classified by a **super type** and a **sub type**, which represent broad and specific categories, respectively. For example:
    - **Super Type** could be something general like "Corporate Bond" or "Government Bond."
    - **Sub Type** could further narrow down the category, such as "Investment Grade Corporate Bond" or "Municipal Government Bond."

### 2. **Universe-Specific Preferences**

- Each **Universe** defines a set of **preferred classifications** (both super type and sub type). These preferences indicate what types of bonds are allowed to pass through the filter and be included in that universe.
- For example, a universe may specify that it only includes "Government Bonds" (super type) with "Sovereign" or "Agency" classifications (sub type). Any bonds that don't match this classification will be filtered out.

### 3. **Filtering Logic**

- The `has_correct_classification` method checks the bond's classification against the universe's specified preferences:
    - **Super Type Matching**: First, it verifies if the bond's super type matches one of the preferred super types for that universe.
    - **Sub Type Matching**: If the super type matches, it then checks if the bond's sub type is also in the list of preferred sub types.
- If both the super type and sub type match the universe's specifications, the bond passes this filter and is included for further processing. If not, the bond is excluded.

### 4. **Inclusion and Exclusion Lists**

- The filtering mechanism might also have **inclusion** and **exclusion lists**. This means that:
    - Bonds explicitly listed in an **inclusion list** will pass through, regardless of their classification.
    - Bonds in an **exclusion list** will be filtered out, even if their classification would normally pass the filter.

## Example Scenario

Imagine the following universe settings:

- **Super Type**: Corporate Bond
- **Sub Type**: Investment Grade, High Yield

And the bond classifications are:

- **Bond A**: Corporate Bond (Super Type), Investment Grade (Sub Type) -- **Passes the filter**.
- **Bond B**: Corporate Bond (Super Type), High Yield (Sub Type) -- **Passes the filter**.
- **Bond C**: Government Bond (Super Type), Sovereign (Sub Type) -- **Fails the filter** because the super type doesn't match the universe's settings.
- **Bond D**: Corporate Bond (Super Type), Speculative (Sub Type) -- **Fails the filter** because the sub type is not part of the preferred classifications.

## Purpose of the Classification Filter

The classification filter ensures that only bonds which belong to specific financial categories, as defined by the universe, are included in the analysis or trade idea generation process. This allows financial analysts or automated systems to focus only on bonds that fit their investment strategies or risk profiles, which are often defined by classification.

## Location of Logic for Classification Filtering

[Classification filtering logic in bond.py](https://gitlab.com/katanalabs/katana/-/blob/master/pipelines/generate/domain/bond.py)

## Key Elements in the Classification Filtering

1. **Classifications in the Universe**

     - The universe defines a list of classifications (both super types and sub types) that it prefers. These are specified within the `universe.classifications` list.

2. **Classification Super Type and Sub Type**

     - Bonds are categorized by both a **super type** (e.g., "Corporate", "Government") and a **sub type** (e.g., "Domestic", "Eurobond").
     - The filtering logic first checks the bond's **super type** against the universe's preferences, and then it checks the **sub type** to ensure the bond matches.

3. **Logic Inside `has_correct_classification`**

     - The function loops over the universe's preferred classifications.
     - If the bond's **super type** matches a preferred super type, the bond passes the filter.
     - If the bond's **sub type** matches a preferred sub type, the bond also passes the filter.
     - If both a **super type** and a **sub type** are provided in the form of a tuple (i.e., both are expected to match), the bond must match both to pass the filter.

## Example Filtering Scenario

- If a universe prefers the classification **Corporate Bonds** (super type) with a sub type of **Domestic**, only bonds that are classified as **Corporate Bonds** with the **Domestic** sub type will pass this filter.
- If the universe doesn't specify any classifications, all bonds will pass this part of the filter.

## Code Reference

This filtering logic is directly implemented in the `has_correct_classification` function within the `BondsUniverseFilter` class, located in your file.
```
