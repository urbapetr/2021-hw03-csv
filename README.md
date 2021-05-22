Homework assignment no. 3, CSV
====================================

**Publication date:**  May 22, 2021

**Submission deadline:** June 6, 2021

General information
-------------------
In this assignment you will create a CSV parsing library.
Unlike many advanced libraries which allow parsing and mapping CSV data directly to Java objects (POJOs),
our library will rely on collections, and the only supported data type will be ``String``.
Even so, this will be a useful library as data conversion can be easily deferred and sometimes 
all you need is a simple dynamic solution. 

Given a CSV data set
```csv
atomicNumber, symbol, name, standardState, groupBlock, yearDiscovered
1, H , Hydrogen,gas,nonmetal,1766
2, He , Helium,gas,noble gas,1868
```

Our library will support two types of parsing depending on whether we want to use the first line as a header.

For plain (headerless) data sets, each line will be converted into ``List<String>`` and thus the entire data set
can be converted into ``List<List<String>``. With the example above, our parser will yield
```
[
    [atomicNumber, symbol, name, standardState, groupBlock, yearDiscovered],
    [1, H , Hydrogen,gas,nonmetal,1766],
    [2, He , Helium,gas,noble gas,1868],
]
```

For data sets with a header, each line will be converted into ``Map<String, String>`` with 
column names used as keys. Thus the entire data set can be converted into ``List<Map<String, String>>``.
With the example above, our parser will yield
```
[
    {
        atomicNumber: 1, 
        symbol: H, 
        name: Hydrogen, 
        standardState: gas, 
        groupBlock: nonmetal, 
        yearDiscovered: 1766
    }.
    {
        atomicNumber: 2,
        symbol: He, 
        name: Helium, 
        standardState: gas, 
        groupBlock: noble gas,
        yearDiscovered: 1868
    }
]
```

### Evaluation
Beside functional correctness, this assignment is focused on object-oriented design and understanding how IO works in Java.
This means that the way you structure your program will be an important part of its evaluation.
On the other hand, the given set of tests is not trying to provide an elaborate test coverage and **incorrect behaviour in corner-cases should not have a large impact on the evaluation**.

*Note that all this is at your seminar teacher's discretion.*

The maximum number of points for this assignment is **15**.

- **11 points** for passing the tests (attached tests do not guarantee 100% correctness).
- **4 points** for correct and clean implementation (evaluated by your class teacher).

### Preconditions
To successfully implement this assignment you need to know the following

1. Understanding of OOP in Java
2. Working with collections
3. Basic knowledge of generic types
4. Understanding of IO in Java (packages ``java.io`` and ``java.nio``)

### Project structure
The structure of the project provided as a base for your implementation should meet the following criteria.

1. Package ```cz.muni.fi.pb162.hw03.csv``` contains classes and interfaces provided as a part of the assignment.
  - **Do not modify or add any classes or subpackages into this package.**
2. Package  ```cz.muni.fi.pb162.hw03.csv.impl``` should contain your implementation.
  - **Anything outside this package will be ignored during evaluation.**

### Names in this document
Unless fully classified name is provided, all class names are relative to the package ```cz.muni.fi.pb162.hw03.csv``` or ```cz.muni.fi.pb162.hw03.csv.impl``` for classes implemented as a part of your solution.


### Compiling the project
The project can be compiled and packaged in the same way you already know

```bash
$ mvn clean install
```

The only difference is that unlike with seminar project, this time checks for missing documentation and style violation will produce an error.
You can disable this behavior by running this command temporarily.

```bash
$ mvn clean install -Dcheckstyle.fail=false
```

You can consult your seminar teacher on setting the ```checkstyle.fail``` property in your IDE (or just google it).

### Submitting the assignment
The procedure to submit your solution may differ based on your seminar group. However, in general, it should be OK to submit ```target/homework03-2021-1.0-SNAPSHOT-sources.jar``` to the homework vault.

## Implementation notes
Generally speaking, there are no mandatory requirements on the structure of your code other than what is enforced by provided interfaces

* Provided tests should give you a good idea about the intended use of this library
* Think about how IO streams are structured in Java, ``CsvReader`` is essentially another layer.
* Read JavaDoc carefully

Example library usage 

```java
public static void main(String[] args) throws IOException {
    Path path = Paths.get("test.csv");
    CsvParser parser = CsvToolkit.parser();

    // Direct usage
    List<List<String>> data = parser.readAll(path);
    List<Map<String, String>> dataHeaded = parser.readAllWithHeader(path);

    System.out.println("Direct (eager)");
    System.out.println(data);
    System.out.println(dataHeaded);
    System.out.println();

    // Reader Usage: Consumer
    System.out.println("Reader: Consumer");
    try (var reader = parser.open(path)) {
        reader.forEach(System.out::println);
    }

    try (var reader = parser.openWithHeader(path)) {
        reader.forEach(System.out::println);
    }
    System.out.println();

    // Reader Usage: iterative
    System.out.println("Reader: Iterative");
    try (var reader = parser.open(path)) {
        List<String> line;
        while ((line = reader.read()) != null) {
            System.out.println(line);
        }
    }

    try (var reader = parser.openWithHeader(path)) {
        Map<String, String> line;
        while ((line = reader.read()) != null) {
            System.out.println(line);
        }
    }
}
```
