# The Etymology of Data Terms

Every name in data was chosen as a metaphor from another field. Once you see where the word came from, the concept clicks instantly. Fact = a thing that happened. Dimension = a direction to look at it from. Frame = the rectangular frame around a picture.

## Fact Table -- Why "Fact"?

The word comes straight from Latin *factum* -- "a thing done." A fact is something that happened and was measured. A sale occurred. A click happened. A shipment was dispatched.

In data warehousing (coined by Bill Inmon, 1990s), a fact table records events -- each row is one thing that occurred -- and stores the measurable numbers from that event: the amount, the quantity, the duration. The numbers are the "facts" -- they are objective, they are the record of what happened.

This is why fact tables contain foreign keys (pointing outward to dimensions) and numeric measures. The keys say "this event involved these things", and the measures say "here is what we observed."

## Dimension Table -- Why "Dimension"?

"Dimension" comes from Latin *dimensio* -- "a measurement in a direction." Think of physical dimensions: width, height, depth. Each one is a different axis you can measure along.

In data, a dimension is an axis of analysis -- a different angle you can slice the facts by. You can look at sales by time, by customer, by product, by location. Each of those is a dimension -- a direction you can rotate your analysis to face.

Dimension tables hold the descriptive context that gives facts meaning. The fact table knows a sale of $250 happened. The dimension tables tell you who bought it, what they bought, when, and from where.

| Physical World | Data World |
|---|---|
| Width, height, depth | Customer, product, date, location |
| "Measure along the X axis" | "Slice your analysis by customer" |
| Coordinate system | Star schema |

## DataFrame -- Why "Frame"?

The word comes from R's `data.frame` (introduced in the 1990s), which itself borrowed the concept from a statistical data frame -- the rectangular grid a statistician would draw on paper to organize observations and variables before analysis.

"Frame" as in a picture frame -- it is the rectangular boundary that holds structured data in rows and columns. When pandas (Python, 2008) adopted the concept, they kept the name: `pd.DataFrame`.

The key insight in the name: a DataFrame is not just a table. It is a self-describing rectangular structure that knows its own column names, data types, and index. The "frame" holds all that metadata together.

## The Full Naming Map

| Term | Origin Word | Original Meaning | Why It Fits |
|---|---|---|---|
| Fact table | Latin *factum* | "a thing done" | Each row is a recorded event with measurable numbers |
| Dimension table | Latin *dimensio* | "measurement in a direction" | Each table is a different axis to slice facts by |
| DataFrame | "data frame" from statistics/R | A rectangular grid of observations | Rows = observations, columns = variables, rectangular |
| Schema | Greek *skhēma* | "form, shape, plan" | The plan/shape of how tables relate |
| Record | Latin *recordari* | "to remember" | A single stored memory of an event (= a row) |
| Field | Old English *feld* | "open land, area" | A named area within a record that holds one value (= a column) |
| Query | Latin *quaere* | "ask, seek" | You are asking the database a question |
| Index | Latin *index* | "pointer, indicator" | A pointer structure that helps you find rows fast |
| Primary key | Latin *clavis* | "key" (as in a key to a door) | Uniquely unlocks one specific row |
| Foreign key | Same | A key that opens a door in another table | References a row in another table |
| ETL | Extract, Transform, Load | Literal pipeline steps | Pull data → reshape it → put it somewhere new |
| Star schema | Shape of the diagram | A star (centre + radiating arms) | Fact in centre, dimensions pointing outward |
| Snowflake schema | Shape of the diagram | A snowflake (branching arms) | Dimensions that themselves have sub-dimensions |
| Pipeline | Physical pipeline | A tube that moves things | Data flows through stages like water through pipes |
| Grain | Size of a grain of sand | Smallest particle | The level of detail of one row in a fact table |
| Cardinality | Latin *cardinalis* | "principal, chief" (also: counting) | How many unique values a column has; how many rows relate |
| Aggregate | Latin *aggregare* | "to flock together" | Many rows combined into one summary value |
| Partition | Latin *partitio* | "a dividing" | Physically splitting a table into chunks for performance |