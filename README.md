# MongoDB Practicals

**Name:** Ritesh Chaudhari
**Roll No:** 25104129

A collection of MongoDB practical exercises covering CRUD operations, indexing, aggregation, sorting, pagination, regular expressions and query operators. Each practical lists the query and the **definition of every function/operator used**.

## Databases and Collections

| Database | Collection | Description |
|---|---|---|
| `employee` | `emp_personal_details` | emp_id, emp_name, emp_address, emp_dob, emp_age, emp_mobileno |
| `employee` | `emp_professional_details` | emp_id, emp_name, designation, salary, incentive, working_hours |
| (restaurant db) | `restaurants` | restaurant_id, name, borough, cuisine, address (building, street, zipcode, coord), grades (date, grade, score) |

## Requirements

- MongoDB 6.0 or later
- MongoDB Shell (`mongosh`) or MongoDB Compass

## How to Run

```bash
mongosh
use employee
# paste the queries from the sections below
```

---

# Part 1: Employee Database

## Practical 1: Create `emp_personal_details`

```js
use employee

db.emp_personal_details.insertOne({
  emp_id: 101,
  emp_name: "Ritesh chaudhari",
  emp_address: "Raver",
  emp_dob: new Date("2004-05-01"),
  emp_age: 24,
  emp_mobileno: "9874563214"
})
```

**Functions used**

| Function | Definition |
|---|---|
| `use <db>` | Switches to the given database. If it does not exist, MongoDB creates it when data is first inserted. |
| `insertOne()` | Inserts a single document into a collection. The collection is created automatically if it does not exist. |
| `new Date()` | Creates a date value (BSON Date) so dates can be stored and compared properly. |

## Practical 2: Create `emp_professional_details`

```js
db.emp_professional_details.insertMany([{
  emp_id: 101,
  emp_name: "Ram Chaudhari",
  designation: "hr",
  salary: 85400,
  incentive: 4522,
  working_hours: 7
}])
```

**Functions used**

| Function | Definition |
|---|---|
| `insertMany()` | Inserts multiple documents into a collection in one call. Takes an array of documents. |

## Practical 3: Insert 10 records and basic find

Insert 10 documents into both collections using `insertMany()` (emp_id 101 to 110).

**3.1 Show all employees having designation Manager**

```js
db.emp_professional_details.find({ designation: 'Manager' })
```

**3.2 Show all employees having salary 6000**

```js
db.emp_professional_details.find({ salary: 6000 })
```

**Functions used**

| Function | Definition |
|---|---|
| `find(filter)` | Retrieves documents that match the filter condition. `find()` with no filter returns all documents. |

## Practical 4: Update operations

**4.1 Add field `status` = "Retired" where age is 60 or more**

```js
db.emp_personal_details.updateMany(
  { emp_age: { $gte: 60 } },
  { $set: { status: "Retired" } }
)
```

**4.2 Give incentive 5000 to employees whose working hours are greater than 45 per week**

```js
db.emp_professional_details.updateMany(
  { working_hours: { $gt: 45 } },
  { $set: { incentive: 5000 } }
)
```

**4.3 Add 1000 to the salary of employees whose designation is Accountant**

```js
db.emp_professional_details.updateMany(
  { designation: "Accountant" },
  { $inc: { salary: 1000 } }
)
```

**Functions used**

| Function / Operator | Definition |
|---|---|
| `updateMany(filter, update)` | Updates all documents that match the filter. |
| `$set` | Sets the value of a field. Adds the field if it does not exist. |
| `$inc` | Increases (or decreases, if negative) a numeric field by the given amount. |
| `$gte` | Greater than or equal to. |
| `$gt` | Greater than. |

## Practical 5: Indexes

**5.1 Single field index on `emp_id`**

```js
db.emp_professional_details.createIndex({ emp_id: 1 })
db.emp_professional_details.getIndexes()
```

**5.2 Compound index on `emp_id`, `emp_name`**

```js
db.emp_professional_details.createIndex({ emp_id: 1, emp_name: 1 })
db.emp_professional_details.getIndexes()
```

**Functions used**

| Function | Definition |
|---|---|
| `createIndex()` | Creates an index on one or more fields to make queries faster. `1` = ascending, `-1` = descending. |
| Compound index | An index on more than one field, used when queries filter or sort on those fields together. |
| `getIndexes()` | Lists all indexes on a collection. |

## Practical 6: Aggregation

**6.1 Sum of salaries of employees having designation Clerk**

```js
db.emp_professional_details.aggregate([
  { $match: { designation: "Clerk" } },
  { $group: { _id: null, total_salary: { $sum: "$salary" } } }
])
```

**6.2 Minimum salary of Software Engineers**

```js
db.emp_professional_details.aggregate([
  { $match: { designation: "Software Engineer" } },
  { $group: { _id: null, min_salary: { $min: "$salary" } } }
])
```

**Functions used**

| Function / Operator | Definition |
|---|---|
| `aggregate()` | Runs a multi-stage pipeline where each stage processes and passes documents to the next. |
| `$match` | Pipeline stage that filters documents (like `find`). |
| `$group` | Pipeline stage that groups documents by `_id` and applies accumulators. `_id: null` groups all documents together. |
| `$sum` | Adds up the values of a field. |
| `$min` | Returns the smallest value of a field. |

## Practical 7: Unwind, Skip, Limit

**7.1 Unwind a mobile number array**

```js
db.emp_personal_details.aggregate([{ $unwind: '$emp_mobile_number' }])
```

**7.2 Skip the first 3 records**

```js
db.emp_personal_details.find({}).skip(3)
```

**7.3 Show only the first 4 records**

```js
db.emp_personal_details.find({}).limit(4)
```

**Functions used**

| Function | Definition |
|---|---|
| `$unwind` | Splits an array field into separate documents, one per array element. |
| `skip(n)` | Skips the first `n` documents in the result. |
| `limit(n)` | Returns only the first `n` documents. |

---

# Part 2: Restaurants Database

## Practical 9: Create the `restaurants` collection

```js
db.restaurants.insertOne({
  restaurant_id: 'R101',
  name: 'Wilson Cafe',
  borough: 'Bronx',
  cuisine: 'American',
  address: {
    building: '101',
    street: 'Street 1',
    zipcode: '10401',
    coord: [-73.81, 40.71]
  },
  grades: [
    { date: new Date("2025-01-15T00:00:00.000Z"), grade: 'A', score: 95 }
  ]
})
```

**Concepts used**

| Concept | Definition |
|---|---|
| Embedded document | A document stored inside another document (here `address`). |
| Array | An ordered list of values (here `coord` and `grades`). |
| Array of documents | An array whose elements are documents (here `grades`). |

## Practical 10: Display documents

**10.1 All documents**

```js
db.restaurants.find()
```

**10.2 Only restaurant_id, name, borough, cuisine**

```js
db.restaurants.find({}, { restaurant_id: 1, name: 1, borough: 1, cuisine: 1 })
```

**Functions used**

| Function | Definition |
|---|---|
| `find()` | Returns all documents of the collection. |
| Projection (2nd argument) | Chooses which fields to show: `1` = include, `0` = exclude. |

## Practical 11: Projection and filter

**11.1 Same fields, excluding `_id`**

```js
db.restaurants.find({}, { restaurant_id: 1, name: 1, borough: 1, cuisine: 1, _id: 0 })
```

**11.2 Restaurants in the Bronx**

```js
db.restaurants.find({ borough: 'Bronx' })
```

**Functions used**

| Function | Definition |
|---|---|
| `_id: 0` | Hides the default `_id` field from the output. |
| Equality filter `{ field: value }` | Matches documents where the field equals the value. |

## Practical 12: Limit and Skip

**12.1 First 5 restaurants in the Bronx**

```js
db.restaurants.find({ borough: 'Bronx' }).limit(5)
```

**12.2 Next 5 restaurants after skipping the first 5**

```js
db.restaurants.find({ borough: 'Bronx' }).skip(5).limit(5)
```

**Functions used**

| Function | Definition |
|---|---|
| `limit(n)` | Restricts the result to `n` documents. |
| `skip(n)` | Skips `n` documents before returning results. Used with `limit()` for pagination. |

## Practical 13: Score comparisons

**13.1 Score more than 90**

```js
db.restaurants.find({ "grades.score": { $gt: 90 } })
```

**13.2 Score more than 80 but less than 100**

```js
db.restaurants.find({ "grades.score": { $gt: 80, $lt: 100 } })
```

**Functions used**

| Operator | Definition |
|---|---|
| `"grades.score"` (dot notation) | Accesses a field inside an embedded document or array of documents. |
| `$gt` | Greater than. |
| `$lt` | Less than. |

## Practical 14: Multiple conditions with sort

Cuisine not American, grade A, not in Brooklyn, sorted by cuisine descending.

```js
db.restaurants.find({
  cuisine: { $ne: "American" },
  borough: { $ne: "Brooklyn" },
  "grades.grade": 'A'
}).sort({ cuisine: -1 })
```

**Functions used**

| Function / Operator | Definition |
|---|---|
| `$ne` | Not equal to. |
| `sort()` | Orders the results. `1` = ascending, `-1` = descending. |
| Multiple conditions | Several fields in one filter are combined with an implicit AND. |

## Practical 15: Name starts with "Wil"

```js
db.restaurants.find(
  { name: /^Wil/ },
  { _id: 0, restaurant_id: 1, name: 1, borough: 1, cuisine: 1 }
)
```

**Concepts used**

| Concept | Definition |
|---|---|
| Regular expression `/pattern/` | Matches text patterns in a string field. |
| `^` | Anchors the match to the start of the string. |

## Practical 16: Name ends with "ces"

```js
db.restaurants.find(
  { name: /ces$/ },
  { _id: 0, restaurant_id: 1, name: 1, borough: 1, cuisine: 1 }
)
```

**Concepts used**

| Concept | Definition |
|---|---|
| `$` | Anchors the match to the end of the string. |

## Practical 17: Name contains "Reg"

```js
db.restaurants.find(
  { name: /Reg/ },
  { _id: 0, restaurant_id: 1, name: 1, borough: 1, cuisine: 1 }
)
```

**Concepts used**

| Concept | Definition |
|---|---|
| `/Reg/` (no anchors) | Matches the text anywhere in the string. Case-sensitive. |

## Practical 18: Bronx and American or Chinese

```js
db.restaurants.find({
  borough: 'Bronx',
  $or: [{ cuisine: 'American' }, { cuisine: 'Chinese' }]
})
```

**Functions used**

| Operator | Definition |
|---|---|
| `$or` | Matches documents that satisfy at least one of the conditions in the array. |

## Practical 19: Borough in a list

```js
db.restaurants.find(
  { borough: { $in: ['Staten Island', 'Queens', 'Bronx', 'Brooklyn'] } },
  { _id: 0, restaurant_id: 1, name: 1, borough: 1, cuisine: 1 }
)
```

**Functions used**

| Operator | Definition |
|---|---|
| `$in` | Matches documents where the field value is equal to any value in the given array. |

## Practical 20: Borough not in a list

```js
db.restaurants.find(
  { borough: { $nin: ["Staten Island", "Queens", "Bronx", "Brooklyn"] } },
  { _id: 0, restaurant_id: 1, name: 1, borough: 1, cuisine: 1 }
)
```

**Functions used**

| Operator | Definition |
|---|---|
| `$nin` | Matches documents where the field value is not in the given array. |

## Practical 21: Score not more than 10

```js
db.restaurants.find(
  { "grades.score": { $lte: 10 } },
  { _id: 0, restaurant_id: 1, name: 1, borough: 1, cuisine: 1 }
)
```

**Functions used**

| Operator | Definition |
|---|---|
| `$lte` | Less than or equal to. |

## Practical 22: Cuisine not American/Chinese, name starts with "Wil"

```js
db.restaurants.find(
  { cuisine: { $nin: ["American", "chinese"] }, name: /^wil/i },
  { restaurant_id: 1, name: 1, borough: 1, cuisine: 1, _id: 0 }
)
```

**Concepts used**

| Concept | Definition |
|---|---|
| `i` flag | Makes the regular expression case-insensitive (`/^wil/i` matches "Wil", "wil", "WIL"). |
| `$nin` | Excludes documents whose value is in the list. |

## Practical 23: Sort by name descending

```js
db.restaurants.find().sort({ name: -1 })
```

**Functions used**

| Function | Definition |
|---|---|
| `sort({ field: -1 })` | Sorts in descending (Z to A, high to low) order. |

## Practical 24: Cuisine ascending, borough descending

```js
db.restaurants.find().sort({ cuisine: 1, borough: -1 })
```

**Functions used**

| Function | Definition |
|---|---|
| Multi-field `sort()` | Sorts by the first field, then by the second field for documents with equal first values. |

## Practical 25: Check whether all addresses contain a street

```js
db.restaurants.find({ "address.street": { $exists: false } })
```

**Functions used**

| Operator | Definition |
|---|---|
| `$exists` | Checks whether a field is present (`true`) or missing (`false`). An empty result means every document has the field. |

## Practical 26: `coord` values of type Double

```js
db.restaurants.find({ "address.coord": { $type: "double" } })
```

**Functions used**

| Operator | Definition |
|---|---|
| `$type` | Matches documents where the field is of the given BSON type (for example `double`, `string`, `int`). |

## Practical 27: Score divisible by 7

```js
db.restaurants.find(
  { "grades.score": { $mod: [7, 0] } },
  { restaurant_id: 1, name: 1, "grades.grade": 1, _id: 0 }
)
```

**Functions used**

| Operator | Definition |
|---|---|
| `$mod: [divisor, remainder]` | Matches documents where the field value divided by the divisor leaves the given remainder. |

## Practical 28: Name contains "mon" (case-insensitive)

```js
db.restaurants.find(
  { name: { $regex: "mon", $options: "i" } },
  { name: 1, borough: 1, "address.coord": 1, cuisine: 1, _id: 0 }
)
```

**Functions used**

| Operator | Definition |
|---|---|
| `$regex` | Matches strings against a regular expression pattern. |
| `$options: "i"` | Makes the `$regex` match case-insensitive. |

## Practical 29: sum, avg, min, max

```js
db.emp_professional_details.aggregate([
  { $group: {
      _id: null,
      min: { $min: '$salary' },
      max: { $max: '$salary' },
      sum: { $sum: '$salary' },
      avg: { $avg: '$salary' }
  } }
])
```

**Functions used**

| Operator | Definition |
|---|---|
| `$min` | Smallest value of the field in the group. |
| `$max` | Largest value of the field in the group. |
| `$sum` | Total of the field values in the group. |
| `$avg` | Average of the field values in the group. |
| `"$salary"` | The `$` prefix refers to the value of a field inside an aggregation stage. |

---

## Quick Reference: Operators

| Type | Operators |
|---|---|
| Comparison | `$gt`, `$gte`, `$lt`, `$lte`, `$ne`, `$in`, `$nin` |
| Logical | `$or` |
| Element | `$exists`, `$type` |
| Evaluation | `$mod`, `$regex` |
| Update | `$set`, `$inc` |
| Aggregation | `$match`, `$group`, `$unwind`, `$sum`, `$avg`, `$min`, `$max` |
| Cursor methods | `sort()`, `skip()`, `limit()` |
| Collection methods | `insertOne()`, `insertMany()`, `find()`, `updateMany()`, `aggregate()`, `createIndex()`, `getIndexes()` |

## Author

Ritesh Chaudhari (25104129)
