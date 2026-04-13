Experiment No. 12

Name: Pranav Menon

PRN No.: 25070123085

Batch: ENTC - B1

Aim: To perform Categorical Data Analysis using Python.


THEORY

1) Introduction to Categorical Data

Categorical data consists of values that represent discrete groups or categories rather than
continuous numerical quantities. Examples include Gender (Male/Female), Department (CSE, IT,
ECE, Mechanical), Grade (A, B, C), Payment Method (Cash, Card, UPI), and Delivery Type
(Standard, Express). Categorical data is one of the most common data types in real-world
datasets, appearing in surveys, transaction logs, educational records, and customer data.

Categorical columns in Pandas are typically stored as the object dtype by default, meaning
they are treated as strings. They can also be explicitly cast to Pandas' dedicated category
dtype for more efficient storage and ordered comparisons. Analyzing categorical data involves
understanding the distribution of categories, relationships between different categorical
columns, and patterns within grouped subsets.

Two datasets were used in this experiment:

  Dataset 1 — Student Records (Expt12.csv):
    Columns: Student (S1–S60), Gender (Male/Female), Department (CSE, IT, ECE, Mechanical),
    Grade (A, B, C). Contains 60 rows.

  Dataset 2 — Order Records (created manually):
    Columns: Order_ID (O1–O10), Category (Electronics/Clothing/Grocery),
    Payment_Method (UPI/Card/Cash), Delivery_Type (Standard/Express),
    Customer_Type (New/Returning). Contains 10 rows.


2) Loading Data — pd.read_csv()

Both datasets are loaded using pd.read_csv(), which reads a CSV file from the specified
path into a Pandas DataFrame.

3) value_counts() — Frequency Distribution

The value_counts() method returns a Pandas Series showing how many times each unique value
appears in a column, sorted by descending frequency by default.

Syntax:
    df['ColumnName'].value_counts()


The display() function renders the output as a formatted HTML table in Colab/Jupyter cells,
which is more readable than print() for DataFrames and Series.

For the order dataset:
    display(df['Category'].value_counts())
    display(df['Payment_Method'].value_counts())

These reveal the frequency of each product category and payment method across the 10 orders.


4) value_counts(normalize=True) — Percentage Distribution

Passing normalize=True to value_counts() calculates the relative frequency (proportion) of
each category rather than its raw count. Multiplying by 100 converts this to a percentage.

    print(df['Grade'].value_counts(normalize=True) * 100)

Output:
    A    35.0
    B    33.33...
    C    31.66...
    Name: proportion, dtype: float64

This shows that approximately 35% of students received an A grade. The normalize parameter
is useful for comparing distributions between datasets of different sizes, since raw counts
are not directly comparable across datasets with different numbers of rows.

The same was applied to the order dataset:
    display(df['Category'].value_counts(normalize=True) * 100)


5) Boolean Filtering of Categorical Columns

A subset of rows matching a specific category value can be extracted using Boolean indexing,
where a condition on a categorical column produces a Boolean Series that is passed back into
the DataFrame.

    electronics_order = df[df['Category'] == 'Electronics']
    display(electronics_order)

This selects only the rows where the Category column equals 'Electronics'. The result is a
new DataFrame containing only those rows, while the original df is unchanged. This is the
standard pattern for filtering categorical data in Pandas — identical to the numerical
filtering covered in Experiment 10, applied here to string-valued columns.


6) sort_values() — Sorting by a Categorical Column

DataFrame rows can be sorted by the values in any column using sort_values(). For
categorical columns, sorting is performed alphabetically by default (A–Z).

    sorted_data = df.sort_values(by='Category')
    display(sorted_data)

This reorders all rows so that orders in the 'Clothing' category appear first (alphabetically
before 'Electronics' and 'Grocery'), followed by 'Electronics', then 'Grocery'. The original
df is not modified because inplace=True was not passed.

For ordered categorical columns (where A > B > C), sort_values() respects the defined
category order rather than alphabetical order when the column has been cast to an ordered
Categorical type.


7) unique() — Distinct Category Values

The unique() method returns a NumPy array of all distinct values in a column, without
repetition and without counting. The values are returned in the order they first appear.

    print(df['Category'].unique())

Output:
    ['Electronics' 'Clothing' 'Grocery']

This is different from value_counts() which counts occurrences. unique() simply tells you
what categories exist. It is useful for validating that a column contains only the expected
set of values and for detecting unexpected entries like misspellings or extra whitespace.


8) nunique() — Count of Distinct Categories

nunique() returns a single integer: the number of distinct non-null values in a column.
It is the count version of unique().

    print(df['Category'].nunique())

Output:
    3

This tells us there are exactly 3 product categories. It is more efficient than
len(df['Category'].unique()) when only the count (not the actual values) is needed.
Called on the full DataFrame (df.nunique()), it returns the unique count for every column.


9) pd.crosstab() — Cross-Tabulation Between Two Categorical Columns

pd.crosstab() computes a frequency table (contingency table) that shows how many times each
combination of values from two categorical columns co-occurs. It is one of the most powerful
tools for discovering relationships between categorical variables.

Syntax:
    pd.crosstab(df['Row_Column'], df['Column_Column'])

Multiple cross-tabulations were performed in this experiment:

  a) Category vs Payment Method:
         print(pd.crosstab(df['Category'], df['Payment_Method']))

         Output:
         Payment_Method  Card  Cash  UPI
         Category
         Clothing           3     0    0
         Electronics        0     0    4
         Grocery            0     3    0

     This reveals that all Clothing purchases used Card, all Electronics orders used UPI,
     and all Grocery orders used Cash — a perfect one-to-one relationship between category
     and payment method that would be invisible from inspecting either column alone.

  b) Gender vs Grade:
         print(pd.crosstab(df['Gender'], df['Grade']))

     Shows how many male and female students received each grade.

  c) Department vs Gender:
         print(pd.crosstab(df['Department'], df['Gender']))

     Shows the gender composition within each academic department.

  d) Department vs Grade:
         print(pd.crosstab(df['Department'], df['Grade']))

     Shows how grades are distributed within each department — which departments have more
     A-grade students versus C-grade students.


10) pd.crosstab() with normalize='index' — Row-Wise Percentage

The normalize parameter of pd.crosstab() converts counts into proportions. When
normalize='index' is specified, each row is divided by its row total, producing row-wise
percentages.

    print(pd.crosstab(df['Department'], df['Grade'], normalize='index') * 100)

This shows the percentage distribution of grades within each department. For example, if
the CSE department has 12 students with 6 receiving A, 4 receiving B, and 2 receiving C,
the normalized row would show 50%, 33.3%, and 16.7% respectively. This is more informative
than raw counts when departments have different numbers of students.


11) groupby() with value_counts() — Group-Level Frequency Analysis

The groupby() method splits the DataFrame into groups based on a categorical column and
applies an aggregation function to each group. Chaining value_counts() after groupby()
counts the frequency of each sub-category within every group.

    print(df.groupby('Department')['Grade'].value_counts())

Output:
    Department   Grade
    CSE          A        ...
                 B        ...
                 C        ...
    ECE          A        ...
    ...

This produces a hierarchical (multi-index) Series showing the grade distribution within
each department simultaneously. It provides all the information of multiple separate
value_counts() calls in a single, structured output.

For the order dataset:
    print(df.groupby('Category')['Payment_Method'].value_counts())

    Output:
    Category     Payment_Method
    Clothing     Card              3
    Electronics  UPI               4
    Grocery      Cash              3
    Name: count, dtype: int64

This confirms the exclusive payment method preference per category, consistent with the
crosstab output.


12) Comparison of Analysis Methods

Method                         | Purpose
-------------------------------|------------------------------------------------
value_counts()                 | Frequency of each value in one column
value_counts(normalize=True)   | Percentage distribution in one column
unique()                       | List of distinct values in one column
nunique()                      | Count of distinct values in one column
pd.crosstab()                  | Frequency of value combinations across two columns
pd.crosstab(normalize='index') | Row-percentage distribution across two columns
groupby().value_counts()       | Frequency of sub-categories within each group
Boolean filter df[condition]   | Subset rows matching a specific category value
sort_values(by='Col')          | Sort rows alphabetically by a categorical column

CONCLUSION

Categorical Data Analysis using Python and the Pandas library was successfully performed.
