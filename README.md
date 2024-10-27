# Data Warehouse and MapReduce Programming Project

This project comprises two tasks: designing a Hive data warehouse (Task A) and implementing MapReduce programming to analyze a computer science bibliography dataset in Hadoop (Task B). The project is part of coursework requirements and is designed to demonstrate proficiency in Hive data warehousing and MapReduce for large-scale data analysis.

## Table of Contents

- [Task A: Hive Data Warehouse Design](#task-a-hive-data-warehouse-design)
  - [Objective](#objective)
  - [Data and Tables](#data-and-tables)
  - [Queries](#queries)
  - [Results and Screenshots](#results-and-screenshots)
- [Task B: MapReduce Programming](#task-b-mapreduce-programming)
  - [Objective](#objective)
  - [Task Selection](#task-selection)
  - [MapReduce Algorithm](#mapreduce-algorithm)
  - [Efficiency Discussion](#efficiency-discussion)
- [Setup](#setup)
- [License](#license)

## Task A: Hive Data Warehouse Design

### Objective
Design a Hive data warehouse with three tables and at least 50 records to support complex data queries. Implement ten queries with varying complexity to demonstrate data manipulation and retrieval capabilities in Hive.

### Data and Tables
For this task, we created a dataset with three tables:
1. **Authors**: Contains information about authors, including author ID, name, and affiliation.
2. **Papers**: Contains paper details, such as paper ID, title, and publication year.
3. **Conferences**: Contains conference information, including conference ID, name, and location.

Each table has at least 50 records, ensuring sufficient data for the queries and analysis.

### Queries
We implemented 10 queries to demonstrate variety and complexity, including:
1. **Basic Filtering**: Retrieve all papers published in a specific year.
2. **Join Operation**: List papers along with author names and conference information.
3. **Aggregation**: Count the number of papers by each author.
4. **Grouping**: Group papers by conference and display counts.
5. **Sorting**: Sort papers based on publication year.
6. **Nested Queries**: Find conferences with the highest number of publications per year.
7. **Subqueries**: Display the top authors based on paper count.
8. **String Functions**: Extract specific keywords from paper titles.
9. **Date Functions**: Filter papers by publication date range.
10. **Conditional Statements**: Apply conditional formatting to display conferences by region.

### Results and Screenshots
Each query’s output, alongside execution screenshots, is included to showcase the implementation and verify the data and query results.

---

## Task B: MapReduce Programming

### Objective
Develop a MapReduce algorithm to analyze a computer science bibliography file stored on Hadoop. The file includes information about papers in the following format:

### Task Selection
Based on the student ID number (e.g., `011340894`), the last digit determines the specific task:
- **Last Digit 4**: Task B.3 - Output the number of authors by each conference for each year.

For this example, our focus is **Task B.3**.

### MapReduce Algorithm

#### Pseudo-Code

1. **Mapper**:
    - **Input**: Each line of the bibliography file.
    - **Output**: `(key, value)` pairs where `key` is a combination of `conference|year` and `value` is the number of unique authors for that conference and year.
    - **Process**:
      - Parse each line to extract `authors`, `conference`, and `year`.
      - For each author in `authors`, emit `(conference|year, author)`.

2. **Reducer**:
    - **Input**: Aggregated `(conference|year, [author1, author2, ...])`.
    - **Output**: `(conference|year, author_count)`.
    - **Process**:
      - Count unique authors for each `conference|year` key and emit the result.

#### Example Code Snippet (Java)
```java
// Mapper Class
public class AuthorCountMapper extends Mapper<LongWritable, Text, Text, Text> {
    public void map(LongWritable key, Text value, Context context) {
        String[] parts = value.toString().split("\\|");
        String[] authors = parts[0].split(",");
        String conference = parts[2];
        String year = parts[3];
        String outputKey = conference + "|" + year;

        for (String author : authors) {
            context.write(new Text(outputKey), new Text(author.trim()));
        }
    }
}

// Reducer Class
public class AuthorCountReducer extends Reducer<Text, Text, Text, IntWritable> {
    public void reduce(Text key, Iterable<Text> values, Context context) {
        Set<String> uniqueAuthors = new HashSet<>();
        for (Text author : values) {
            uniqueAuthors.add(author.toString());
        }
        context.write(key, new IntWritable(uniqueAuthors.size()));
    }
}
