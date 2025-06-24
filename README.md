# Peoplesoft Development

- [Peoplesoft Development](#peoplesoft-development)
  - [Basic Programming Concepts](#basic-programming-concepts)
    - [Variables](#variables)
      - [Data Types](#data-types)
        - [Basic Data Types](#basic-data-types)
        - [Object Data Types](#object-data-types)
    - [Variable Scope and Lifetime](#variable-scope-and-lifetime)
      - [Variable Scope](#variable-scope)
        - [Local Scope](#local-scope)
        - [Component Scope](#component-scope)
        - [Global Scope](#global-scope)
      - [Variable Lifetime](#variable-lifetime)
        - [Local Lifetime](#local-lifetime)
        - [Component Lifetime](#component-lifetime)
        - [Global Lifetime](#global-lifetime)
      - [Declaring Variables](#declaring-variables)
      - [Undeclared Variables](#undeclared-variables)
    - [Comments](#comments)
    - [Operators](#operators)
      - [Math operators](#math-operators)
      - [Comparison Operators](#comparison-operators)
      - [String Concatenation](#string-concatenation)
      - [@ Operator](#-operator)
    - [Conditional Statements](#conditional-statements)
    - [Loops](#loops)
    - [Arrays](#arrays)
  - [Peoplesoft Programming Concepts](#peoplesoft-programming-concepts)
    - [Rowsets](#rowsets)
    - [Application Classes](#application-classes)

## Basic Programming Concepts

### Variables

- [Variables Documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcd/Variables-074b27.html?pli=ul_d1761e41_tpcd)

Variables behave as a container for data inside of your application. They may contain different types of data, like text, numbers, booleans, and more complex data types like objects.

Variables exist temporarily and the data will be lost unless it is stored permanently in a file or database. The lifespan of the variable depends on how it is declared.

Peoplecode, just like most programming languages, uses variables extensively.

#### Data Types

Data types indicate what type of data is being stored in the variable.

##### Basic Data Types

Peoplesoft uses these basic data types:

- any
  - The any type is indeterminate and Peoplecode will determine its type at runtime.
- boolean
  - Booleans store two possible values, `True` or `False`
- date
- datetime
- float
  - Floats are numbers that have partial values. `10.25` is a float. `10.00` is a float.
- integer
  - Integers are whole numbers. `12` is an integer, but `12.1` is not.
- number
  - In most cases, you should use a number type instead of a float or integer, per [Oracle's documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcd/DataTypes-074b53.html?pli=ul_d1761e41_tpcd#:~:text=Note%3A%20The%20float%20and%20integer%20data%20types%20should%20be%20used%20instead%20of%20Number%20only%20when%20a%20performance%20analysis%20indicates%20that%20the%20increased%20speed%20is%20useful%20and%20an%20application%20analysis%20indicates%20that%20the%20different%20representations%20will%20not%20affect%20the%20results%20of%20the%20computations.).
- string
  - Strings are sets of characters. They can be any type, so `0` can be a string. In peoplecode, strings are surrounded by quotation marks. `"10"` is a string, but `10` is not.
- time

##### Object Data Types

Object data types correspond with objects you instantiate. These are complex data types that can contain many different values and can perform special operations. The most common types you will use involve accessing the component's buffers:

- Field
- Record
- Row
- Rowset

There are many more built-in object types and you can create your own using [Application Classes](#application-classes).

#### Variable Scope and Lifetime

Variables have a scope and a lifespan. Scope indicates when and where a variable can be accessed.

Peoplesoft has three scopes: `Local`, `Component`, and `Global`.

Scope indicates in what sections of code a variable may be accessed.

##### Local Scope

Locally scoped variables may only be accessed inside the block of code it is written in. In component buffer events such as `SavePreChange`, that is the entire section of code. In application packages, the scope would be inside the method.

In peoplesoft, the scope is anything proceeding the variable declaration inside the code block. Trying to access a value before it is declared will result in an error.

```peoplecode
Local string &greeting = "Hello, " | &name; /* results in an error */

Local string &name = "John";

Local string &greeting = "Hello, " | &name; /* does not result in an error, as the &name variable is declared. */
```

For application packages, methods are locally scoped, so a Local variable declared in one method cannot be accessed in another method.

```peoplecode
class SomeClass
    method SomeClass();
    method SomeMethod();
end-class;

method SomeClass();
    Local string &name = "John";
end-method;

method SomeMethod()
    /* results in error since &name cannot be accessed inside the SomeMethod() method */
    Local string &storedName = &name; 
end-method;
```

##### Component Scope

Component variables are scoped to the component buffer you're accessing. When you access different sections of a peoplesoft system, they are usually a component. If you go to edit a person's Job Information, you are loading a component.

When a variable is scoped to a component, it means that anything within that component may access the variable. This means that a variable declared with a value in the component buffer event `SavePreChange` may also be accessed by the Component's `SavePostChange`. It also may be accessed by a Record's peoplecode, so a `FieldChange` event on a Record used by the component. It may also be accessed by Application Packages that are declared in your component.

So, if you declare a variable with a value in on component:

```peoplecode
/* SavePreChange */
Component string &name = "John";
```

It can then be accessed in another part of the component. Note that you must re-declare the variable in the block of code you're working on.

```peoplecode
/* SavePostChange */

Component string &name; /* declare it again so it may access the variable */
Local string &greeting = "Hello, " | &name;
```

##### Global Scope

Global variables behave similarly to component variables but they persist through the entire Peoplesoft session once it's been declared. That means that it may be accessed by any component in the system once it's been instantiated.

```peoplecode
Global string &name = "John";
```

```peoplecode
/* Some other component */
Global string &name;
Local string &greeting = "Hello, " | &name;
```

#### Variable Lifetime

Lifetime is the duration for which a variable is stored by the system.

##### Local Lifetime

The variable will remain accessible while executing the block of code it is instantiated in.

##### Component Lifetime

The variable will remain accessible while the user/system has the current component open. Once you navigate away from a page to another component, it is lost.

##### Global Lifetime

Global variables remain accessible for the entirety of the peoplesoft session. That means even when navigating away from the page, the variable is still accessible by other components.

#### Declaring Variables

Variables have a scope/lifespan, type, and name. Names of variables declared by developers are prefixed with `&`.

```peoplecode
Scope Type &Name;
Local string &name;
Component number &number1;
Global boolean &shouldRun;
```

You may also instantiate a variable with a value on declaration:

```peoplecode
Local number &number1 = 10;
Global string &name = "John";
```

#### Undeclared Variables

In Peoplecode, in many cases you may instantiate (create a new instance of) a variable without declaring it first.

```peoplecode
&number = 10;
&string = "Hello";
```

The data type is inferred from the value that it is given. Generally speaking, you should avoid creating varaibles this way.

```peoplecode
/* Peoplecode */
Local number &number = 10;
Local number &decimal = 10.2;
Local string &string = "Hello, World!";
Local any &any;
Local ApiObject &object;

Local number &num1  = 2;
Local number &num2  = 8;

/* You may reference other variables inside your variable */
Local number sumOfNums = &num1 + &num2; /* value = 10 */

/* Variables may reference themselves */
sumOfNums = &sumOfNums + 1; /* value = 11 */

Local number &sum = &number + &decimal; /* 20.2 */
```

### Comments

- [Peopletools Documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcd/Comments-074b4e.html?pli=ul_d1761e41_tpcd)

Comments are blocks of code that are ignored by the system executing your code.

They serve as a means for developers to annotate their work. This can be to explain the functionality of a method, the logic flow of a block of code, or other explainers to aid the developer and any future developers that may work on it in the future to understand intent. There are varying opinions on how to write comments and many places have standards to adhere to.

You may be required to annotate changes you make, the date, and the user story/ticket that is associated with the changes in your code.

Peoplecode has three different types of comments. There is single line comments, which begin with `Rem` and and with a semicolon `;`. Additionally, there are two multi-line comments, `<* *>` and `/* */`. The `<* *>` supersedes all other comment types, so you may wrap other comment types inside of it.

```peoplecode

Rem Single line comment;

/* 
Multi-line
Comment
*/

<*
Also
Multi-line
Comment
*>
```

Using the `/* */` comment, it will terminate if there is another in the block of code you're commenting out, breaking intent:

```peoplecode
/* 
/* embedded comment */
Local number &num1 = 10; rem block of code;
*/
```

To fix this, use the `<* *>` comment instead:

```peoplecode
<* 
/* embedded comment */
Local number &num1 = 10; rem block of code;
*>
```

### Operators

- [Operators Documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcd/Operators-074b1b.html?pli=ul_d1761e41_tpcd)

#### Math operators

In Peoplecode, math operators function similarly to how they do in regular math.

- `+` - Addition
- `-` - Subtraction
- `*` - Multiplication
- `/` - Division
- `**` - Exponential

Exponential operations will occur first, and then operations happen left to right. It does not respect tradtional order of operation.

```peoplecode
Local &result = 10 + 14 * 2 / 12 + 3 ** 2;
 
/* Exponent operation completed first: */
&result = 10 + 14 * 2 / 12 + 9;

/* Operations occur from left to right: */
&result = 24 * 2 / 12 + 9;
&result = 48 / 12 + 9;
&result = 4 + 9;
&result = 13;
```

#### Comparison Operators

Comparison operators are used to compare two expressions of the same type and returns a boolean value (True or False). These are used in conditional statements to execute code based on the outcome of the statement.

| Operator  | Meaning                  |
|-----------|--------------------------|
| `=`       | Equal                    |
| `!=`      | Not equal                |
| `<>`      | Not equal                |
| `<`       | Less than                |
| `<=`      | Less than or equal to    |
| `>`       | Greater than             |
| `>=`      | Greater than or equal to |

#### String Concatenation

Strings may be concatenated together using the `|` operator. Place these between two different string values. You may also concatenate strings with other string variables.

```peoplecode
Local string &greeting = "Hello";
Local string &name = "John";
Local string &namedGreeting;

&namedGreeting = &greeting | ", " | &name | "!"; i
/* "Hello, John!" */
```

Any variable you concatenate with a string must also be a string. This will not work, since the `&age` variable is a number type, not a string type:

```peoplecode
Local number &age = 25;
Local string &ageMessage; &ageMessage = "Your age is: " | &age; /* This will not work since &age is not a string */
```

In order to use the value, you must convert a string using the `String()` function. This will work for basic data types, but will not work with Objects:

```peoplecode
Local number &age = 25;
Local string &ageMessage;
&ageMessage = "Your age is: " | String(&age); 
/* "Your age is: 25" */

```

#### Logic Operators

Logic operators allow you to combine multiple boolean expressions so you may evluate whether code should run based on multiple conditions.

`And` requires both expressions to be True in order to evaluate as True. If either is False, it is False.

`Or` will evaluate to True if either of the expressions are True.

`Not` reverses the value, so True becomes False and False becomes True.

From the [Peoplebooks documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcd/Operators-074b1b.html):

| Expression 1 | Operator | Expression 2 | Result |
|--|--|--|--|
| False | And | False | False |
| False | And | True  | False |
| True  | And | True  | False |
| False | Or  | False | False |
| False | Or  | True  | True  |
| True  | Or  | True  | True  |

#### @ Operator

The `@` operator converts a string to a definition reference. So in case you need to reference a  variable programmatically. For example, you might have a function that is expecting a `Record` object but you might not know which Record it is accessing until runtime.

```peoplecode
Local string &recordName = GetRecordName(); /* GetRecordName returns a string of the name of the record. Let's say it returns "JOB" */

/* SomeFunction expects a reference to a Record Object*/
SomeFunction(@("Record." | &recordName));
/* This expands into */
SomeFunction(Record.JOB);

/* We could change the */
&recordName = GetNextRecordName(); /* Returns "JOB_MIL" */
SomeFunction(@("Record." | &recordName));
/* This expands into */
SomeFunction(Record.JOB_MIL);
```

### Conditional Statements

See: [Logic Operators](#logic-operators)

Conditional statements are used to add logic to your application. There are many cases where you will only want to execute code under certain circumstances.

For example, you might have a different bonus rate depending on the time a person has worked at your company.

#### If Statement

If statements are used to evaluate a conditional and execute code inside it only when it returns True.

```peoplecode
Local string &emplid = "12345";

Local number &yearsOfService = GetYearsOfServiceByEmplid(&emplid); /* returns the number of years the employee has worked for the company */
Local number &salary = GetSalaryByEmplid(&emplid); /* returns salary of employee by Employee ID */
Local number &bonusMultiplier;
If &yearsOfService >= 5 Then
  /* If the years of service for the employer is greater than or equal to 5 */
  &bonusMultiplier = 0.1;
Else
  /* Years of service is less than 5 years */
  &bonusMultiplier = 0.05;
End-if;

Local number &bonus = &salary * &bonusMultiplier;
Local number &totalCompensation = &salary + &bonus;
```

If a person's salary is 100,000/year and they have worked at the company for 7 years, because the years of service is greater than or equal to 5, the bonus multiplier variable would be set to `0.1` and their total compensation would be 110,000.

If the person's years of service was 3, however, the variable would be set to `0.05` and their total compensation would be 105,000.

### Loops

- [Loop Documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcd/Statements-074b4d.html)

Loops are used in programming when you'd like to repeat the same code multiple times. o

#### For Loops

- [For Loop Documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcl/PeopleCodeBuilt-inFunctionsAndLanguageConstructs_F.html?pli=ul_d1852e35_tpcl#u761e9afe-0ded-45f1-83ee-e5460d5d6151)

```peoplecode
/* For Loop Syntax */
For &count = expression1 to expression2 [Step i]
  /* statement_list; */
End-For;
```

`&count` is a variable that is used to iterate through the for loop. Once everything inside of the loop is executed, it is incremented to the next number. If &count = 1, then the next step of the loop it will become 2, and so on.

`expression1` and `expression2` are integers (whole numbers). They may be numbers or an expression that evaluates to a number like a variable.

`Step i` is by how much an iterator should increment every loop. It is optional, and if excluded, the iterator will increment by 1 each loop.

Let's say that we want to take a number, `&result` that starts at `1` and you want to increment it by `1` `10` times. You can accomplish this without a loop like this:

```peoplecode
Local number &result = 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
&result  = i + 1;
/* &result = 11 */
```

A For loop allows you to accomplish like so:

```peoplecode
Local number &i, &result;
&result = 1;
For &i = 1 to 10
  /* For each row in the set of rows, do something. */
  &result = &result + 1;
End-For;
/* &result = 11 */
```

Commonly you will iterate through a Rowset object. Rowsets have a length property that may change as you iterate through it. Because of this, you may want to start the loop at the last item and move backwards towards 1.

```peoplecode
Local number &i;
Local Rowset &rs;
For &i = &rs.length to 1 Step -1
  /* execute code for each row in the rowset. */
End-For;
```

You can also step through a for loop by different value. By default it will increment the iterator by 1 each time.

```peoplecode
For &i = 1 To &rs.length Step 2 
  /* Assuming a length of 10, the iterator would start at 1,
  then increment to 3, and then continue until it reaches the
  length of the rowset. */
End-For;
```

#### While Loops

- [While Loop Documentation](https://docs.oracle.com/cd/G10810_01/pt860pbr4/eng/pt/tpcl/PeopleCodeBuilt-inFunctionsAndLanguageConstructs_W-Z.html#u96474e01-a7d3-4646-8609-166f6aa858dc)

While loops will evaluate a logical expression and execute the code inside it until it is no longer True.

You can accomplish something similar to the For Loop from the previous example:

```peoplecode
Local number &result = 1;
Local number &i = 1;
While &i <= 10
  &result = &result + 1;
  &i = &i + 1;
End-While;
/* &result = 11 */
```

While for loops require an iterator, while loops can run with any logical expression and will continue to re-run the code as long as it evaluates to True. This means that you must carefully think about your logic so that it does not result in an infinite loop.

```peoplecode
While True
  /* This loop will run infinitely */
End-While;

While False
  /* This code will never execute */
End-While;
```

#### Repeat While Loops

Repeat While loops (more commonly called Do While loops) are the same as while loops, except that you execute the code at least once before checking if the conditions are of the logical expression are True.

```peoplecode
Repeat
  /* code that you run once before evaluating the logical expression
  and continue until the expression evaluates to False */ 
While expression1;
```

Even if the While expression is False, it will run at least once:

```peoplecode
Repeat
  /* code that will run once and then exit the loop */
While False;
```

### Arrays

Arrays are a collection of values.

## Peoplesoft Programming Concepts

### Rowsets

### Application Classes
