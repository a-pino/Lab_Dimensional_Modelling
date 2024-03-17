# Dimensional Modelling

## Instructions

Answer the following:

1. **Write down the steps involved in the enlistment process and the data that can be gathered from each process.**
2. **What information do you have to include to describe each row in the diagram?**
3. **Draw the enrollment schema based on the metrics needed to be monitored.**
4. **Based on your schema:**
    -  Fill in the fact and dimension tables with mock data.
    -  Join the dimension tables to the fact table.
    -  Create a dashboard out of your mock data. Generate the 4 metrics mentioned in the case and add 6 more metrics. Use OLAP operations to generate new insights from your data.

### Deliverables

- Submit a Google Drive link containing the following files:
  1. Certificate of Authorship
  2. Answers to questions in a PPT format.
     - 1 slide each for questions 1, 2, and 3
     - 1 slide for each of the 10 metrics
     - Can also include a Jupyter notebook
  3. CSV/Excel files containing your mock data

Ensure that the links are open by the due date and by the time of checking. A deduction of 5 pts will be given when the links are inaccessible.

## The Case

The enlistment information of Ateneo allows the school administration to have an insight into the profitability of each class. The departments under each school plan for the classes and open several slots before the start of the semester. The opened slots serve as the inventory of the administration for the upcoming enlistment season. During enlistment, students reserve slots for their required classes for that semester. After having the set of classes, they will proceed with the payment to confirm their slots.

To get the profitability of each class, the costs incurred by each class are subtracted from the revenue earned to establish the class. The margin is computed by dividing the difference between total revenue and cost per course by the total revenue. The revenue is computed by multiplying the rate per unit for the current school year by the number of units per class and the number of students who registered in the class. Each class is uniquely labeled by its section label, semester, school year, and the department and school that offered the class. Each class has a corresponding cost, but it is not constant as each class may be conducted in different types of rooms.

After the enlistment period, the administration wants to compare the financial performance of the current enlistment period versus the previous enlistment period. They want to see the following metrics:

- Revenue per school and department for the current semester vs the last semester of the same school year
- Revenue per school and department for the current semester of the current year vs the same semester of the last school year
- Which school offers the most profitable classes?
- Which department offers the most profitable classes?

As a data warehouse analyst, you need to develop the schema so that the administration can easily extract the comparison report.

## Ideas

1. **Students enlist in a class**
   - We can obtain which classes have been enlisted in.
2. **Students pay for the class they enlisted in**
   - We can obtain how much revenue a class earns per student.

### Enlistment Facts & Student Dimension & Class Dimension

| Enlistment Facts | Student Dimension | Class Dimension  |
|------------------|-------------------|------------------|
| `student_id`     | `student_id`      | `class_id`       |
| `class_id`       | `Name`            | `Course Name`    |
| `payment`        | `Year`            | `Section Label`  |
|                  | `Major`           | `Units`          |
|                  | `Semester`        | `Department`     |
|                  | `School Year`     | `School`         |
|                  |                   | `Cost`           |


- To get the first metric, join Enlistment Facts and Class Dimension on `class_id`; group by school year, semester, department, and school; aggregate/get sum of payments for total revenue.
- To get the second metric, look at the output from above.
- To get the third metric, join Enlistment Facts and Class Dimension on `class_id`; group by `class_id`; aggregate/get sum of payments for total revenue; generate new feature `Profit` by subtracting cost from total revenue; group by school; aggregate/get sum of profits for total profit per school.
- To get the third metric, group by department instead of school.

| Fact Table: ClassEnrollmentFacts |  |
|----------------------------------|--------------------------------|
| **Column**                       | **Description**               |
| ClassID (PK)                     | Primary key for Class          |
| SchoolID (FK)                    | Foreign key referencing Schools|
| DepartmentID (FK)                | Foreign key referencing Departments|
| StudentID (FK)                   | Foreign key referencing Students|
| PaymentID (FK)                   | Foreign key referencing Payments|
| Semester                         | Semester of the class          |
| SchoolYear                       | Year of the school              |
| NumberOfUnits                    | Number of units for the class  |
| RatePerUnit                      | Rate per unit for the class    |
| NumberOfStudents                 | Total number of students       |
| TotalRevenue                     | Total revenue generated        |
| TotalCost                        | Total cost incurred            |
| ProfitMargin                     | Margin of profit               |

| Dimension Tables | |
|------------------|------------------------|
| **Table**            | **Columns**                |
| Schools          | SchoolID (PK)          |
|                  | SchoolName             |
| Departments      | DepartmentID (PK)      |
|                  | DepartmentName         |
|                  | SchoolID (FK)          |
| Classes          | ClassID (PK)           |
|                  | DepartmentID (FK)      |
|                  | SchoolID (FK)          |
|                  | Semester               |
|                  | SchoolYear             |
|                  | NumberOfUnits          |
|                  | RatePerUnit            |
|                  | RoomType               |
|                  | MaximumSlots           |
| Students         | StudentID (PK)         |
|                  | FullName               |
|                  | EnrollmentYear         |
| Payments         | PaymentID (PK)         |
|                  | StudentID (FK)         |
|                  | AmountPaid             |
|                  | PaymentDate            |

