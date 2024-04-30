# Learning Aggregation Pipeline MongoDB

Hey there I am Learning Aggregation Pipeline in MongoDB from the YouTube Videos of Hitesh Choudhary.

## NOTES:

### Group and Sum Pipeline

#### Q1. What is the average age of all users ?

#### Solution:

```json
[
  {
    $group: {
      _id: null,
      averageAge: {
        $avg: "$age",
      },
    },
  },
]
```

#### Q2. Which country is having highest number of users ?

#### Solution:

```json
[
  {
    $group: {
      _id: "$company.location.country",
      totalUsers: {
        $sum: 1,
      },
    },
  },
  {
    $sort: {
      totalUsers: -1,
    },
  },
  {
    $limit: 1,
  },
]
```

### Dealing with arrays in aggregation

#### Q1. Find the average number of tags per user ?

#### Solution (1):

```json
[
  {
    $unwind: {
      path: "$tags"
    }
  },
  {
    $group: {
      _id: "$_id",
      numberOfTags: {
        $sum: 1
      }
    }
  },
  {
    $group: {
      _id: null,
      average: {
        $avg: "$numberOfTags"
      }
    }
  }
]

```

#### Solution (2):

```json
[
  {
    $addFields: {
      numberOfTags: {
        $size: { $ifNull: ["$tags", []] },
      },
    },
  },
  {
    $group: {
      _id: null,
      averageNumberOfTags: {
        $avg: "$numberOfTags",
      },
    },
  },
]
```

### Match and Project Pipeline

#### Q1. What is the name and age of users who are inactive and have 'velit' as a tag ?

#### Solution:

```json
[
  {
    $match: {
      tags: "velit",
      isActive: false,
    },
  },
  {
    $project: {
      name: 1, age:1
    }
  }
]

```

#### Q2. How many users have a phone no statring with '+1 (940)' ?

#### Solution:

```json
[
  {
    $match: {
      "company.phone": /^\+1\s\(940\)/,
    },
  },
  {
    $count: 'totalUsers'
  }
]
```

#### Q3. Provide four most recently registered and also provide only registered, name, age, gender

#### Solution:

```json
[
  {
    $sort: {
      registered: -1,
    },
  },
  {
    $limit: 4,
  },
  {
    $project: {
      registered: 1,
      name: 1,
      age: 1,
      gender: 1,
    },
  },
]
```

#### Q4. Categorize users by their favorite fruit

#### Solution:

```json
[
  {
    $group: {
      _id: "$favoriteFruit",
      user: { $push: "$name" },
    },
  },
]
```

#### Q5. How many users have 'ad' as the second tag in their list of tags ?

#### Solution:

```json
[
  {
    $match: {
      "tags.1": "ad",
    },
  },
  {
    $count: "totalUsers",
  },
]
```

#### Q6. Find the users who have both 'enim' and 'id' as their tags ?

#### Solution:

```json
[
  {
    $match: {
      tags: {
        $all: ["ad", "enim"],
      },
    },
  },
  {
    $count: "totalUsers",
  },
]
```

#### Q7. List all the companies located in USA with their corresponding user count

#### Solution:

```json
[
  {
    $match: {
      "company.location.country": "USA",
    },
  },
  {
    $group: {
      _id: "$company.title",
      users: { $sum: 1 },
    },
  },
]
```

### Lookup (Same as left outer join)

#### Q1. Using in authors schema and books schema

#### Solution (If you want to provide an array of objects):

```json
[
    {
    $lookup: {
      from: "books",
      localField: "_id",
      foreignField: "author_id",
      as: "author_details",
    },
  },
]
```

#### Solution (If you want to provide an object directly):

```json
[
  {
    $lookup: {
      from: "books",
      localField: "_id",
      foreignField: "author_id",
      as: "author_details",
    },
  },
  {
    $addFields: {
      author_details: {
        $arrayElemAt: ["$author_details", 0],
      },
    },
  },
]
```
