
[TOC]

Simple Learning Note about MySQL++
==================================

## 1.	the basic usage pattern

Mysql++ actually doesn’t work all that differently than other database access APIs. The usage pattern looks like this:

**1. Open the connection**

**2. Form and execute the query**

**3. If successful, iterate through the result set**

**4. Else, deal with errors**

Each of these steps corresponds to a Mysql++ class or class hierarchy.

---

## 2. Overview

### 2.1 The Connection Object

A Connection object manages the connection to the MySQL server. You need at least one of these objects to do anything. Because the other MySQL++ objects your program will use often depend (at least indirectly) on the Connection instance, the Connection object needs to live at least as long as all other MySQL++ objects in your program.

[reference](https://tangentsoft.net/mysql++/doc/html/refman/classmysqlpp_1_1Connection.html)

---

### 2.2 The Query Object

Most often, you create SQL queries using a Query object created by the Connection object.

Query acts as a standard C++ output stream, so you can write data to it like you would to std::cout or std::ostringstream. This is the most C++ish way Mysql++ provides for building up a query string.

[Query reference](https://tangentsoft.net/mysql++/doc/html/refman/classmysqlpp_1_1Query.html)

Query also has a feature callled [Template Queries](https://tangentsoft.net/mysql++/doc/html/userman/tquery.html).

---

### 2.3 Result Sets

The field data in a result set are stored in a special std::string-like class called [String](https://tangentsoft.net/mysql++/doc/html/refman/classmysqlpp_1_1String.html). This class has conversion operators that let you automatically convert these objects to any of the basic C data types.

As for the result sets as a whole, MySQL++ has a number of different ways of representing them:

1.	Queries That Do Not Return data : SimpleResult
2.	Queries That Return Data : Mysql++ Data Structures The most direct way to retrieve a result set is to use Query::store(). This returns a [StoreQueryResult](https://tangentsoft.net/mysql++/doc/html/refman/classmysqlpp_1_1StoreQueryResult.html) object, which derives from std::vectormysqlpp::Row, making it a random-access container of Rows. In turn, each Row object is like a std::vector of String objects, one for each field in the result set. Therefore, you can treat StoreQueryResult as a two-dimensional array: you can get the 5th field on the 2nd row by simply saying result[1][4]. You can also access row elements by field name, like this: result[2]["price"].
3.	Queries That Return Data: Specialized SQL Structures

---

### 2.4 Exceptions

(skip it ... )

---

## 3. Using Example

### 3.1 Example-1

```cpp
#include "cmdline.h"
#include "printdata.h"

#include <mysql++.h>

#include <iostream>
#include <iomanip>

using namespace std;

int
main(int argc, char *argv[])
{
    // Get database access parameters from command line
    mysqlpp::examples::CommandLine cmdline(argc, argv);
    if (!cmdline) {
        return 1;
    }

    // Connect to the sample database.
    mysqlpp::Connection conn(false);
    if (conn.connect(mysqlpp::examples::db_name, cmdline.server(),
            cmdline.user(), cmdline.pass())) {
        // Retrieve a subset of the sample stock table set up by resetdb
        // and display it.
        mysqlpp::Query query = conn.query("select item from stock");
        if (mysqlpp::StoreQueryResult res = query.store()) {
            cout << "We have:" << endl;
            mysqlpp::StoreQueryResult::const_iterator it;
            for (it = res.begin(); it != res.end(); ++it) {
                mysqlpp::Row row = *it;
                cout << '\t' << row[0] << endl;
            }
        }
        else {
            cerr << "Failed to get item list: " << query.error() << endl;
            return 1;
        }

        return 0;
    }
    else {
        cerr << "DB connection failed: " << conn.error() << endl;
        return 1;
    }
}
```

### 3.2 Example-2

```cpp
#include "cmdline.h"
#include "printdata.h"

#include <mysql++.h>

#include <iostream>
#include <iomanip>

using namespace std;

int
main(int argc, char *argv[])
{
    // Get database access parameters from command line
    mysqlpp::examples::CommandLine cmdline(argc, argv);
    if (!cmdline) {
        return 1;
    }

    // Connect to the sample database.
    mysqlpp::Connection conn(false);
    if (conn.connect(mysqlpp::examples::db_name, cmdline.server(),
            cmdline.user(), cmdline.pass())) {
        // Retrieve the sample stock table set up by resetdb
        mysqlpp::Query query = conn.query("select * from stock");
        mysqlpp::StoreQueryResult res = query.store();

        // Display results
        if (res) {
            // Display header
            cout.setf(ios::left);
            cout << setw(31) << "Item" <<
                    setw(10) << "Num" <<
                    setw(10) << "Weight" <<
                    setw(10) << "Price" <<
                    "Date" << endl << endl;

            // Get each row in result set, and print its contents
            for (size_t i = 0; i < res.num_rows(); ++i) {
                cout << setw(30) << res[i]["item"] << ' ' <<
                        setw(9) << res[i]["num"] << ' ' <<
                        setw(9) << res[i]["weight"] << ' ' <<
                        setw(9) << res[i]["price"] << ' ' <<
                        setw(9) << res[i]["sdate"] <<
                        endl;
            }
        }
        else {
            cerr << "Failed to get stock table: " << query.error() << endl;
            return 1;
        }

        return 0;
    }
    else {
        cerr << "DB connection failed: " << conn.error() << endl;
        return 1;
    }
}
```

---

**END**
