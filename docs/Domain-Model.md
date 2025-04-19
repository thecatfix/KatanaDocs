# Combined Domain Model Diagram
![diagram.drawio.svg](Katana/uploads/046b8b8a7d39a00f97bf8ae4e24d2127/diagram.drawio.svg)

# Ingest
![diagram.drawio.svg](Katana/uploads/db1a0e965242ca1a96f71a2ef0c6552f/diagram.drawio.svg)

# Generate
![diagram.drawio.svg](Katana/uploads/18f9fe47c5d8750f070ef294516d7707/diagram.drawio.svg)

# Discover
![diagram.drawio.svg](Katana/uploads/4e28525acaba636e35c78949ad138f86/diagram.drawio.svg)
Explaining Domain Model
### Entity

- Data Holder: An entity holds data within your domain model.
- Unique Identification: Each entity is uniquely identifiable, distinguishing it from other entities.
- Lifecycle and Persistence: Entities have a lifecycle (creation, modification, deletion) and thus need to be persisted in a database or another storage system.

### Value Object

- Data Holder: Similar to entities, value objects hold data.
- Non-unique: Unlike entities, value objects don't need to be unique within the domain.
- On-demand Creation: They can be created as needed and are more transient.
- Replication and Re-use: Value objects can be replicated and reused across the system.
- No Lifecycle: They don't have a lifecycle and are typically not persisted, though they can be for convenience.

### Aggregate

- Collection of Entities and Value Objects: An aggregate groups related entities and value objects.
- Internal Utility: The elements within an aggregate are not typically useful outside of it.
- Root Element: There's a root element in each aggregate that acts as an anchor point and public interface for the internal elements.

### Repository

- Entity Storage and Retrieval: Repositories handle the storage and retrieval of entities, which could be aggregate roots.
- Abstraction Over Persistence: They abstract away the underlying persistence mechanism, like databases.

### Stream

- Entity Flow: A stream is a conduit for a specific type of entity, facilitating the flow of these entities within the system.
- Receive and Send Entities: Streams can be used to both receive and send entities.

### Service

- Functionality Collection: A service in your domain model represents a collection of related functionalities.
- Stateless: Services typically don't have their own persisted state.

### Index

- The description for "Index" is missing in your glossary. Usually, an index refers to a data structure that improves the speed of data retrieval operations.

### Core Domain

- Central Functionalities: The core domain likely includes the central, most crucial functionalities of your system. It's referenced as a separate document [Core](https://loop.microsoft.com/p/core.md).

### Sub Domains

- These are specific areas within your domain model, each focusing on different aspects of the system. They are also referenced as separate documents, indicating detailed descriptions elsewhere:
    - Ingest
    - Generate
    - Discover
    - Authenticate & Collaborate
    - Notify

Your domain model seems well-structured, dividing the system into clear, manageable parts, each with a specific role. This structure aids in both understanding and maintaining the system.

# Core

Your Core domain model, represented using a `mermaid` class diagram, clearly outlines the relationships between the primary entities in your system: `Bond`, `BondPrice`, `Universe`, and `TradeIdea`. Here's a description of the classes and their relationships:

### Classes and Their Fields

1. Bond
    - `ISIN` (International Securities Identification Number): A unique identifier for each bond.
2. BondPrice
    - `ISIN`: The identifier of the bond, linking it to the `Bond` class.
    - `AsOf`: A timestamp indicating when the price was recorded.
    - `ZSpread`: The Z-Spread value of the bond.
3. Universe
    - `Name`: The name of a universe. In financial contexts, a universe often refers to a collection or set of bonds.
4. TradeIdea
    - `ID`: A unique identifier for the trade idea, derived from the concatenation of the `BondBuyISIN` and `BondSellISIN`.

### Relationships

- BondPrice --> Bond: Each `BondPrice` is associated with a `Bond`. The ISIN field in `BondPrice` links to the corresponding `Bond`.
- Universe o-- Bond: This represents a one-to-many relationship where a `Universe` can contain multiple `Bonds`.
- Bond *-- TradeIdea (buy and sell): A `TradeIdea` is associated with two `Bonds`, one to buy and one to sell. This is a many-to-one relationship where multiple trade ideas can involve the same bond.
- BondPrice *-- TradeIdea (buy and sell): Similar to the `Bond` relationship, a `TradeIdea` is also associated with the `BondPrice` for both the buying and selling bonds.
- Universe o-- TradeIdea: A `Universe` can contain multiple `TradeIdeas`. This relationship indicates that trade ideas are categorized or grouped under different universes.

This class diagram effectively represents the core relationships in your financial trading system, particularly focusing on how trade ideas are formulated based on bonds, their prices, and the universes they belong to.
    
    
    ```mermaid
    classDiagram
        class Bond {
            string ISIN
        }
        class BondPrice {
            string ISIN
            time AsOf
            float ZSpread
        }
        class Universe {
            string Name
        }
        class TradeIdea {
            string ID %% BondBuyISIN+BondSellISIN
        }
    
        BondPrice --> Bond
        Universe o-- Bond
        Bond *-- TradeIdea : buy
        BondPrice *-- TradeIdea : buy
        Bond *-- TradeIdea : sell
        BondPrice *-- TradeIdea : sell
        Universe o-- TradeIdea
    ```
    
    

  

# Ingest

The `Ingest` section of your domain model, depicted using a `mermaid` class diagram, represents the structure and relationships involved in the ingestion process of your financial trading system. This diagram showcases several classes, primarily focusing on the characteristics of bonds and their categorization within universes. Let's delve into the details:

### Classes and Their Attributes

1. Bond
    - An entity with fields like `ISIN` (a unique identifier) and `Universe` (the universe to which the bond belongs).
2. Universe
    - An entity identified by `Name`.
3. Value Objects
    - Include `Classification`, `Country`, `Currency`, `Flag`, `IssueType`, `Rating`, `Ticker`, `IndustrySectorLevel1`, `IndustrySectorLevel4`, and `PaymentCategory`. These are data holders without unique identifiers within the domain.
4. UniverseSpec
    - A specification with a field `MinAmountOutstanding`, indicating the minimum amount outstanding for a bond to be included in a universe.

### Relationships

- Value Objects *-- UniverseSpec: Value objects like `Classification`, `Country`, etc., are included or excluded in `UniverseSpec`. This indicates that `UniverseSpec` criteria can specify the inclusion or exclusion of bonds based on these characteristics.
- UniverseSpec "1" ..|> "1" Universe: A one-to-one relationship where `UniverseSpec` is a part of `Universe`.
- UniverseSpec "1" ..|> "*" Bond: A one-to-many relationship where `UniverseSpec` can apply to multiple `Bonds`. This relationship suggests that `UniverseSpec` defines the criteria for including bonds in a universe.
- Bond "" o-- "" Universe: A many-to-many relationship indicating that bonds can belong to multiple universes and vice versa.

### Interpretation

This model primarily focuses on the criteria and specifications for including bonds in different universes. It highlights how various attributes of bonds, such as their classifications, ratings, and sectors, play a role in categorizing them. The use of value objects like `Country`, `Currency`, and `Rating` emphasizes the importance of these characteristics in the bond universe categorization process.

Overall, the `Ingest` domain model provides a structured way to categorize bonds based on multiple criteria, essential for organizing and analyzing financial securities in a trading system.

  

# Generate

The `Generate` section of your domain model, illustrated using a `mermaid` class diagram, portrays the process and entities involved in generating trade ideas in your financial system. This diagram highlights the entities, value objects, specifications, and their interconnections. Let's examine the key components:

### Entities and Value Objects

1. Bond
    - An entity characterized by `ISIN` and `Universe`.
2. BondSpec
    - A specification defining criteria like `MaxMonthsToMature` and `MinAmountOutstanding` for bonds.
3. BondPrice
    - An entity representing the price of a bond at a specific time, identified by `ISIN`, `AsOf` date, and `ZSpread`.
4. BondPriceSpec
    - A specification with criteria for bond prices, such as `MinAsOf`, `MinZSpread`, and `MaxZSpread`.
5. BondWithPrices
    - A value object representing a bond with its associated prices.
6. BondWithPricesSpec
    - A specification for `BondWithPrices`, including criteria like `MaxEWMScore`, `MaxDODScore`, etc.
7. Universe
    - An entity identified by `Name`, representing a collection of bonds.
8. Bookmark
    - An entity representing a bookmarked trade idea, identified by `TradeIdeaID`.
9. BondPair
    - A value object indicating a pair of bonds, possibly bookmarked.
10. TradeIdeaCandidate
    - An entity with attributes like `ID`, `IsBookmarked`, various spread values, and scores.
11. TradeIdeaCandidateSpec
    - A specification for trade idea candidates, including criteria like `MinSpreadMeanReversion`, `MinPearsonScore`, etc.
12. Model
    - An entity representing a predictive model, identified by `ID`.
13. TradeIdeaSpec
    - A specification for trade ideas, including criteria like `MinSimilarityScore`, `MinPearsonScore`, etc.
14. TradeIdea
    - An entity representing a potential trade idea, identified by a concatenated `ID` from two ISINs.

### Relationships

- Bond -- BondWithPrices; BondPrice -- BondWithPrices: Bonds and their prices are associated with `BondWithPrices`.
- BondWithPrices o-- BondPair: Bond pairs are formed from bonds with prices.
- Bookmark ..> BondPair: Bookmarks can be associated with bond pairs.
- BondPair ..|> TradeIdeaCandidate; TradeIdeaCandidate ..|> TradeIdea: Bond pairs are used to generate trade idea candidates, which can become trade ideas.
- Specs to Entities/Value Objects: Specifications (`BondSpec`, `BondPriceSpec`, etc.) are linked to their corresponding entities or value objects, defining criteria or rules for them.
- Universe o-- Bond; Universe o-- TradeIdea: Bonds and trade ideas are associated with universes.
- Model ..> TradeIdeaSpec; TradeIdeaSpec --> TradeIdeaCandidate: Models influence trade idea specifications, which in turn apply to trade idea candidates.

### Interpretation

This model emphasizes the generation of trade ideas from bond data. It showcases how various specifications and criteria filter and refine bonds, their prices, and pairs to form viable trade idea candidates. The presence of value objects like `BondWithPrices` and `BondPair` indicates the aggregation of data from different entities to form meaningful combinations.

The use of bookmarks suggests that certain bond pairs or trade ideas can be flagged for special attention. Finally, the transition from `TradeIdeaCandidate` to `TradeIdea` signifies the final step in the generation process, where candidates are evaluated against specific models and criteria to become actionable trade ideas.

  
  

# Discover

The `Discover` section of your domain model, illustrated using a `mermaid` class diagram, details the structure and interactions for discovering trade ideas within your financial system. This comprehensive model includes various entities, value objects, services, and their relationships. Let's delve into its components:

### Entities and Value Objects

1. Bond
    - An entity with attributes like `ISIN`, `Name`, `IssueDate`, `IssuerName`, `CountryOfRisk`, etc.
2. BondPrice
    - An entity representing a bond's price at a specific time, identified by `ISIN`, `AsOf` date, and `ZSpread`.
3. BondPricePair
    - A value object representing a pair of bond prices.
4. BondPricePairWithStats
    - A value object that includes statistics like `Diff`, `Mean`, `StdDev`, and an `IsIdea` flag.
5. Universe
    - An entity representing a collection of bonds, identified by `Name`.
6. TradeIdea
    - An entity representing a potential trade idea, identified by concatenated ISINs of `BondBuy` and `BondSell`.
7. Filters (TagFilter, MatchFilter, LikeFilter, RangeFilter, ExcludeFilter)
    - Value objects representing different types of filters used in search criteria.
8. Page
    - A value object for pagination details such as `Number`, `Size`, and `Total`.
9. OrderDirection (Enum) and Order
    - `OrderDirection` is an enumeration for sorting direction, and `Order` is a value object specifying the field and direction for ordering.
10. SearchCriteria
    - A value object defining the search criteria, including universe, filters, order, and pagination.
11. Facets (TagFacet, RangeFacet, MatchFacet)
    - Value objects representing facets for search results.
12. SearchResult
    - A value object encapsulating the results of a search, including items, facets, order, pagination, and total item count.
13. TradeIdeaService (Service)
    - A service for executing searches based on `SearchCriteria` and returning `SearchResult`.

### Relationships

- BondPrice -- Bond; BondPricePair -- BondPrice: Bonds are associated with their prices, and bond price pairs are linked to individual bond prices.
- Bond -- Universe; TradeIdea -- Universe; SearchResult *-- Universe: Bonds, trade ideas, and search results are associated with universes.
- TradeIdea *-- Bond: Trade ideas are linked to buy and sell bonds.
- SearchCriteria o-- Filters; SearchResult o-- Facets: Search criteria include various filters, and search results contain facets.
- Order -- SearchCriteria; Page -- SearchCriteria; Order -- SearchResult; Page -- SearchResult: Order and page details are used in both search criteria and results.
- SearchCriteria ..> TradeIdeaService; TradeIdeaService ..|> SearchResult: Search criteria are used by the `TradeIdeaService` to generate search results.

### Interpretation

This diagram illustrates the discovery process within the trading system, focusing on how trade ideas are identified, filtered, and presented. It highlights the importance of flexible search criteria, including various filters and sorting options, facilitating targeted searches within the bond universe. The presence of statistical value objects like `BondPricePairWithStats` suggests an analytical approach to identifying potential trade ideas.

The `TradeIdeaService` plays a central role, acting as the interface for processing search queries and returning structured results. The model also emphasizes the importance of pagination and sorting in managing and presenting search results, ensuring a user-friendly discovery experience.

Overall, the `Discover` section of your domain model portrays a robust system for exploring and identifying trade opportunities in the financial market.

  

# Authenticate & Collaborate

The `Authenticate & Collaborate` section of your domain model illustrates the structure and interactions of entities and services involved in user authentication and collaboration in the context of a financial trading system. It includes entities like `Team`, `AuthUser`, `User`, bookmarks, comments, and channels, along with streams for bookmarks and comments. Let's break down the main components and their relationships:

### Entities and Aggregates

1. Team
    - Represents a team within a company, with attributes like `Company` and `Name`.
2. AuthUser
    - Represents an authenticated user, identified by `UID` and `Email`.
3. User
    - Represents a user within the system, linked to a company, team, and authenticated user ID (`UID`).
4. EnrichedUser
    - An aggregate that enriches an `AuthUser` with more detailed information from `User`.
5. Bookmark
    - Represents a bookmarked trade idea, associated with a user, team, and specific trade idea ID.
6. BookmarkChannel
    - An entity that allows users to publish and subscribe to bookmarks. It is associated with a specific team.
7. Comment
    - Represents a user's comment, linked to a team and user.
8. CommentChannel
    - An entity for publishing and subscribing to comments, associated with a team.

### Streams

- BookmarkStream and CommentStream
    - Streams for bookmarks and comments, respectively, facilitating real-time communication and updates.

### Relationships

- AuthUser -- EnrichedUser; User -- EnrichedUser: Authenticated users are enriched with additional information to form `EnrichedUser`.
- User, Bookmark, Comment --> Team: Users, bookmarks, and comments are associated with a specific team.
- Bookmark, Comment --> User: Bookmarks and comments are linked to individual users.
- Bookmark o-- BookmarkStream; Comment o-- CommentStream: Bookmarks and comments are part of their respective streams.
- BookmarkChannel, CommentChannel --> Team: Channels for bookmarks and comments are tied to specific teams.
- BookmarkChannel, CommentChannel ..|> BookmarkStream, CommentStream: Channels publish to and subscribe from their respective streams.
- User ..|> BookmarkChannel, CommentChannel: Users can publish to and subscribe from bookmark and comment channels.

### Interpretation

The model emphasizes the collaborative aspect of the trading system, where users within a company's teams can interact, share insights, and collaborate on trade ideas. It highlights the importance of real-time communication through bookmark and comment channels, supported by streams that ensure continuous updates and interactions.

The inclusion of `AuthUser` and `EnrichedUser` suggests a robust authentication system that securely manages user identities while enriching them with relevant team and company information. This ensures that users operate within the context of their organizational structures, maintaining data security and compliance.

The model also demonstrates the use of bookmarks and comments as key collaborative tools, allowing users to highlight important trade ideas and engage in discussions. This fosters a community-driven approach to decision-making and idea generation in the financial trading environment.

Overall, the `Authenticate & Collaborate` section of your domain model portrays a sophisticated and secure platform for user authentication and collaboration, enhancing the user experience in a financial trading system.

  

# Notify

The `Notify` section of your domain model outlines the notification system within your application, primarily focusing on how users receive notifications. Here's a breakdown of the components and their relationships:

### Entities and Value Objects

1. User
    - Represents a user in the system, uniquely identified by `UID`.
2. Notification
    - Represents a notification entity containing an `ID`, `Text` (message of the notification), and `Link` (possibly pointing to related content or action).
3. NotificationChannel
    - A value object that represents a channel through which users can subscribe to receive notifications.

### Stream

- NotificationStream
    - A stream that holds all the notifications, serving as a central pipeline through which notifications flow.

### Relationships

- Notification o-- NotificationStream: Notifications are part of the NotificationStream, indicating that all notifications flow through this centralized stream.
- NotificationChannel ..> NotificationStream: The NotificationChannel subscribes to the NotificationStream. This relationship implies that the channel receives data from the stream, likely to forward it to subscribed users.
- User ..> NotificationChannel: Users subscribe to the NotificationChannel to receive notifications. This relationship indicates that notifications are pushed to users through these channels.

### Interpretation

The model illustrates a straightforward notification system. When a notification is created, it enters the NotificationStream. NotificationChannels are subscribed to this stream, and they relay notifications to users who have subscribed to these channels.

The use of a stream for notifications suggests a real-time or near-real-time system where notifications are continuously pushed through the stream and distributed to users. This approach is effective for applications requiring timely updates, such as messaging apps, social media platforms, or systems where prompt user response is critical.

The simplicity of the model focuses on the flow of notifications from their creation to the end user. It abstracts away the complexities of how notifications are generated or the criteria for pushing specific notifications to particular users. This high-level view is beneficial for understanding the overall architecture without getting bogged down in implementation details.

In summary, the `Notify` section of your domain model provides a clear picture of the notification delivery mechanism in your application, emphasizing real-time communication and user engagement.