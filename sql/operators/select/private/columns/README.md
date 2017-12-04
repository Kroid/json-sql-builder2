# columns Helper
Specifies the `$columns` Helper for the `SELECT` Statement to select
only the listed columns instead of `*` or `ALL`.

**Note:**
If you did not support the $columns Helper on a SELECT Statement the preBuild method of the select class will automatically
add a $columns Object with `*` to the query as single column.

## Supported by
- [MySQL](https://dev.mysql.com/doc/refman/5.7/en/select.html)
- [MariaDB](https://mariadb.com/kb/en/library/select/)
- [PostgreSQL](https://www.postgresql.org/docs/9.5/static/sql-select.html)
- [SQLite](https://sqlite.org/lang_select.html)
- [Oracle](https://docs.oracle.com/cd/B19306_01/server.102/b14200/statements_10002.htm)
- [SQLServer](https://docs.microsoft.com/en-us/sql/t-sql/queries/select-transact-sql)

## Allowed Types and Usage

### as Object:

The Usage of `columns` as **Object** is restricted to childs have the following Type:

- Boolean
- Number
- String
- Object
- Function

#### as Object->String:

Usage of `columns` as **Object** with a child of Type **String** :

**Syntax:**

```javascript
$columns: {
    "<identifier | $Helper | $operator>": <String> [, ... ]
}```

**SQL-Definition:**
```javascript
<key-ident> AS <value-ident>[ , ... ]
```

**Example:**
```javascript
function() {
    return sql.build({
        $select: {
            $columns: {
                first_name: 'fn',
                last_name: 'ln'
            },
            $from: 'people'
        }
    });
}

// SQL output
SELECT
    first_name AS fn,
    last_name AS ln
FROM
    people

// Values
{}
```
#### as Object->Object:

Usage of `columns` as **Object** with a child of Type **Object** :

**Syntax:**

```javascript
$columns: {
    "<identifier | $Helper | $operator>": { ... } [, ... ]
}```

**SQL-Definition:**
```javascript
<value> AS <identifier>[ , ... ]
```

**Example:**
```javascript
function() {
    return sql.build({
        $select: {
            $columns: {
                first_name: true,
                last_name: true,
                top_skill: {
                    $select: {
                        skill: 'top_skill',
                        $from: 'people_skills',
                        $where: {
                            'people.people_id': '~~people_skills.people_id',
                            'people_skills.is_top': 1
                        }
                    }
                }
            },
            $from: 'people',
            $where: {
                age: { $gte: 18 }
            }
        }
    });
}

// SQL output
SELECT
    first_name,
    last_name,
    (
        SELECT
            skill AS top_skill
        FROM
            people_skills
        WHERE
            people.people_id = people_skills.people_id
            AND people_skills.is_top = $ 1
    ) AS top_skill
FROM
    people
WHERE
    age >= $ 2

// Values
{
    "$1": 1,
    "$2": 18
}
```
#### as Object->Function:

Usage of `columns` as **Object** with a child of Type **Function** :

**Syntax:**

```javascript
$columns: {
    "<identifier | $Helper | $operator>": sql.<callee>([params])
}```

**SQL-Definition:**
```javascript
<value> AS <key-ident>[ , ... ]
```

**Example:**
```javascript
function() {
    return sql.build({
        $select: {
            $columns: {
                first_name: true,
                last_name: true,
                total_skills: sql.select({ total: { $count: 'skillname' } }, {
                    $from: 'people_skills',
                    $where: {
                        'people.people_id': '~~people_skills.people_id'
                    }
                })
            },
            $from: 'people',
            $where: {
                age: { $gte: 18 }
            }
        }
    });
}

// SQL output
SELECT
    first_name,
    last_name,
    (
        SELECT
            COUNT(skillname) AS total
        FROM
            people_skills
        WHERE
            people.people_id = people_skills.people_id
    ) AS total_skills
FROM
    people
WHERE
    age >= $ 1

// Values
{
    "$1": 18
}
```
### as Array:

The Usage of `columns` as **Array** is restricted to childs have the following Type:

- String
- Object

#### as Array->String:

Usage of `columns` as **Array** with a child of Type **String** :

**Syntax:**

```javascript
$columns: [
    <String> [, ... ]
]```

**SQL-Definition:**
```javascript
<value-ident>[ , ... ]
```

**Example:**
```javascript
function() {
    return sql.build({
        $select: {
            $columns: ['first_name', 'last_name'],
            $from: 'people'
        }
    });
}

// SQL output
SELECT
    first_name,
    last_name
FROM
    people

// Values
{}
```
#### as Array->Object:

Usage of `columns` as **Array** with a child of Type **Object** :

**Syntax:**

```javascript
$columns: [
    { ... } [, ... ]
]```

**SQL-Definition:**
```javascript
<value> AS <key-ident>[ , ... ]
```

**Example:**
```javascript
function() {
    return sql.build({
        $select: {
            $columns: [
                { total_people: { $count: '*' } },
            ],
            $from: 'people'
        }
    });
}

// SQL output
SELECT
    COUNT(*) AS total_people
FROM
    people

// Values
{}
```
### as String:

Usage of `columns` as **String** with the following Syntax:

**Syntax:**

```javascript
$columns: < String >
```

**SQL-Definition:**
```javascript
<value-ident>
```

**Example:**
```javascript
function() {
    return sql.build({
        $select: {
            $columns: 'id',
            $from: 'people'
        }
    });
}

// SQL output
SELECT
    id
FROM
    people

// Values
{}
```