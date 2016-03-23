# Go Test Fixtures

> ***Warning***: this package will wipe the database data before loading the
fixtures! It is supposed to be used on a test database. Please, double check
if you are running it against the correct database.

Writing tests is hard, even more when you have to deal with an SQL database.
This package aims to make writing functional tests for web apps written in
Go easier.

Basically this package mimics the "Rails' way" of writing tests for database
applications, where sample data is kept in fixtures files. Before the execution
of every test, the test database is cleaned and the fixture data is loaded in
the database.

The idea is running tests in a real database, instead of relying in mocks,
which is boring to setup and may lead to production bugs not to being catch in
the tests.

## Installation

First, get it:

```bash
go get gopkg.in/testfixtures.v1
```

## Usage

Create a folder for the fixture files. Each file should contain data for a
single table and have the name `<table-name>.yml`:

```
myapp
  - myapp.go
  - myapp_test.go
  - ...
  - fixtures:
    - posts.yml
    - comments.yml
    - tags.yml
    - posts_tags.yml
    - ...
```

The file would look like this (it can have as many record you want):

```yml
# comments.yml
-
    id: 1
    post_id: 1
    content: This post is awesome!
    author_name: John Doe
    author_email: john@doe.com
    created_at: 2016-01-01 12:30:12
    updated_at: 2016-01-01 12:30:12

-
    id: 1
    post_id: 2
    content: Are you kidding me?
    author_name: John Doe
    author_email: john@doe.com
    created_at: 2016-01-01 12:30:12
    updated_at: 2016-01-01 12:30:12
```

Your tests would look like this:

```go
package myapp

import (
    "database/sql"
    "log"

    _ "github.com/lib/pq"
    "gopkg.in/testfixtures.v1"
)

const FIXTURES_PATH = "fixtures"

func TestMain(m *testing.M) {
    // Open connection with the test database.
    // Do NOT import fixtures in a production database!
    // Existing data would be deleted
    db, _ := sql.Open("postgres", "dbname=myapp_test")

    os.Exit(m.Run())
}

func prepareTestDatabase() {
    err = testfixtures.LoadFixtures(FIXTURES_PATH, db, &testfixtures.PostgreSQLHelper{})
    if err != nil {
        log.Fatal(err)
    }
}

func TestX(t *testing.T) {
    prepareTestDatabase()
    // your test here ...
}

func TestY(t *testing.T) {
    prepareTestDatabase()
    // your test here ...
}

func TestZ(t *testing.T) {
    prepareTestDatabase()
    // your test here ...
}
```

## Compatible databases

For now, only PostgreSQL. Contributions are welcome to add support for more.

[godoc]: https://godoc.org/gopkg.in/testfixtures.v1
