# Assignment 2: Design a Logical Model and Advanced SQL

🚨 **Please review our [Assignment Submission Guide](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md)** 🚨 for detailed instructions on how to format, branch, and submit your work. Following these guidelines is crucial for your submissions to be evaluated correctly.

#### Submission Parameters:

* Submission Due Date: `November 12, 2025`
* Weight: 70% of total grade
* The branch name for your repo should be: `assignment-two`
* What to submit for this assignment:
  * This markdown (Assignment2.md) with written responses in Section 1 and 4
  * Two Entity-Relationship Diagrams (preferably in a pdf, jpeg, png format).
  * One .sql file
* What the pull request link should look like for this assignment: `https://github.com/<your_github_username>/sql/pulls/<pr_id>`
  * Open a private window in your browser. Copy and paste the link to your pull request into the address bar. Make sure you can see your pull request properly. This helps the technical facilitator and learning support staff review your submission easily.

Checklist:

- [ ] Create a branch called `assignment-two`.
- [ ] Ensure that the repository is public.
- [ ] Review [the PR description guidelines](https://github.com/UofT-DSI/onboarding/blob/main/onboarding_documents/submissions.md#guidelines-for-pull-request-descriptions) and adhere to them.
- [ ] Verify that the link is accessible in a private browser window.

If you encounter any difficulties or have questions, please don't hesitate to reach out to our team via our Slack. Our Technical Facilitators and Learning Support staff are here to help you navigate any challenges.

---

## Section 1:

You can start this section following *session 1*, but you may want to wait until you feel comfortable wtih basic SQL query writing.

Steps to complete this part of the assignment:

- Design a logical data model
- Duplicate the logical data model and add another table to it following the instructions
- Write, within this markdown file, an answer to Prompt 3

### Design a Logical Model

#### Prompt 1

Design a logical model for a small bookstore. 📚

At the minimum it should have employee, order, sales, customer, and book entities (tables). Determine sensible column and table design based on what you know about these concepts. Keep it simple, but work out sensible relationships to keep tables reasonably sized.

Additionally, include a date table.

There are several tools online you can use, I'd recommend [Draw.io](https://www.drawio.com/) or [LucidChart](https://www.lucidchart.com/pages/).

**HINT:** You do not need to create any data for this prompt. This is a conceptual model only.

#### Prompt 2

We want to create employee shifts, splitting up the day into morning and evening. Add this to the ERD.

#### Prompt 3

The store wants to keep customer addresses. Propose two architectures for the CUSTOMER_ADDRESS table, one that will retain changes, and another that will overwrite. Which is type 1, which is type 2?

**HINT:** search type 1 vs type 2 slowly changing dimensions.

```
Type one keeps address fields directly in the customer table like customer_street, customer_city, customer_postal_code, and customer_province. When someone moves, just UPDATE their row with the new address. The old address gets overwritten and is gone forever. This is Type 1 SCD because it doesn't retain any history of changes.

Type two keeps separate customer_address table with address_id (PK), customer_id (FK), street, city, postal_code, province, effective_date, end_date, and is_current flag. When someone moves, you set their old address record's is_current to FALSE and end_date to today, then INSERT a new row with the new address where is_current is TRUE. This preserves the full timeline of everywhere they've lived. This is Type 2 SCD because it retains all changes as new rows.

Type 1 overwrites and loses history, Type 2 creates new records and maintains complete history. Type 1 is simpler for querying current data, Type 2 is better when you need to track changes over time.
```

---

## Section 2:

You can start this section following *session 4*.

Steps to complete this part of the assignment:

- Open the assignment2.sql file in DB Browser for SQLite:
  - from [Github](./02_activities/assignments/assignment2.sql)
  - or, from your local forked repository
- Complete each question

### Write SQL

#### COALESCE

1. Our favourite manager wants a detailed long list of products, but is afraid of tables! We tell them, no problem! We can produce a list with all of the appropriate details.

Using the following syntax you create our super cool and not at all needy manager a list:

```
SELECT 
product_name || ', ' || product_size|| ' (' || product_qty_type || ')'
FROM product
```

But wait! The product table has some bad data (a few NULL values).
Find the NULLs and then using COALESCE, replace the NULL with a blank for the first column with nulls, and 'unit' for the second column with nulls.

**HINT**: keep the syntax the same, but edited the correct components with the string. The `||` values concatenate the columns into strings. Edit the appropriate columns -- you're making two edits -- and the NULL rows will be fixed. All the other rows will remain the same.

<div align="center">-</div>

#### Windowed Functions

1. Write a query that selects from the customer_purchases table and numbers each customer’s visits to the farmer’s market (labeling each market date with a different number). Each customer’s first visit is labeled 1, second visit is labeled 2, etc.

You can either display all rows in the customer_purchases table, with the counter changing on each new market date for each customer, or select only the unique market dates per customer (without purchase details) and number those visits.

**HINT**: One of these approaches uses ROW_NUMBER() and one uses DENSE_RANK().

2. Reverse the numbering of the query from a part so each customer’s most recent visit is labeled 1, then write another query that uses this one as a subquery (or temp table) and filters the results to only the customer’s most recent visit.
3. Using a COUNT() window function, include a value along with each row of the customer_purchases table that indicates how many different times that customer has purchased that product_id.

<div align="center">-</div>

#### String manipulations

1. Some product names in the product table have descriptions like "Jar" or "Organic". These are separated from the product name with a hyphen. Create a column using SUBSTR (and a couple of other commands) that captures these, but is otherwise NULL. Remove any trailing or leading whitespaces. Don't just use a case statement for each product!

| product_name               | description |
| -------------------------- | ----------- |
| Habanero Peppers - Organic | Organic     |

**HINT**: you might need to use INSTR(product_name,'-') to find the hyphens. INSTR will help split the column.

2. Filter the query to show any product_size value that contain a number with REGEXP.

<div align="center">-</div>

#### UNION

1. Using a UNION, write a query that displays the market dates with the highest and lowest total sales.

**HINT**: There are a possibly a few ways to do this query, but if you're struggling, try the following: 1) Create a CTE/Temp Table to find sales values grouped dates; 2) Create another CTE/Temp table with a rank windowed function on the previous query to create "best day" and "worst day"; 3) Query the second temp table twice, once for the best day, once for the worst day, with a UNION binding them.

---

## Section 3:

You can start this section following *session 5*.

Steps to complete this part of the assignment:

- Open the assignment2.sql file in DB Browser for SQLite:
  - from [Github](./02_activities/assignments/assignment2.sql)
  - or, from your local forked repository
- Complete each question

### Write SQL

#### Cross Join

1. Suppose every vendor in the `vendor_inventory` table had 5 of each of their products to sell to **every** customer on record. How much money would each vendor make per product? Show this by vendor_name and product name, rather than using the IDs.

**HINT**: Be sure you select only relevant columns and rows. Remember, CROSS JOIN will explode your table rows, so CROSS JOIN should likely be a subquery. Think a bit about the row counts: how many distinct vendors, product names are there (x)? How many customers are there (y). Before your final group by you should have the product of those two queries (x\*y).

<div align="center">-</div>

#### INSERT

1. Create a new table "product_units". This table will contain only products where the `product_qty_type = 'unit'`. It should use all of the columns from the product table, as well as a new column for the `CURRENT_TIMESTAMP`.  Name the timestamp column `snapshot_timestamp`.
2. Using `INSERT`, add a new row to the product_unit table (with an updated timestamp). This can be any product you desire (e.g. add another record for Apple Pie).

<div align="center">-</div>

#### DELETE

1. Delete the older record for the whatever product you added.

**HINT**: If you don't specify a WHERE clause, [you are going to have a bad time](https://imgflip.com/i/8iq872).

<div align="center">-</div>

#### UPDATE

1. We want to add the current_quantity to the product_units table. First, add a new column, `current_quantity` to the table using the following syntax.

```
ALTER TABLE product_units
ADD current_quantity INT;
```

Then, using `UPDATE`, change the current_quantity equal to the **last** `quantity` value from the vendor_inventory details.

**HINT**: This one is pretty hard. First, determine how to get the "last" quantity per product. Second, coalesce null values to 0 (if you don't have null values, figure out how to rearrange your query so you do.) Third, `SET current_quantity = (...your select statement...)`, remembering that WHERE can only accommodate one column. Finally, make sure you have a WHERE statement to update the right row, you'll need to use `product_units.product_id` to refer to the correct row within the product_units table. When you have all of these components, you can run the update statement.

---

## Section 4:

You can start this section anytime.

Steps to complete this part of the assignment:

- Read the article
- Write, within this markdown file, <1000 words.

### Ethics

Read: Boykis, V. (2019, October 16). _Neural nets are just people all the way down._ Normcore Tech. `<br>`
    https://vicki.substack.com/p/neural-nets-are-just-people-all-the

**What are the ethical issues important to this story?**

Consider, for example, concepts of labour, bias, LLM proliferation, moderating content, intersection of technology and society, ect.

To me, the most striking ethical conundrum in Vicki Boykis' article is the exploitation of content moderation labor. Boykis describes moderators in developing countries who spend their days viewing traumatic content for minimal pay and under precarious working conditions. This echoes Marxian concerns about alienated labor, where workers become mere instruments in a production process that strips them of dignity and agency. These workers are simultaneously essential to platform function and rendered invisible in platform narratives that emphasize artificial intelligence and automation. This invisibility is not accidental but structural. AI is a tool for crystallizing labour into capital. When platforms tout their AI capabilities, they obscure the human labor that makes these systems possible. The "neural nets" that moderate content are indeed "just people all the way down": people who label training data, people who review edge cases, talented experts who set the benchmarks for the AI's abilities in specific domains like coding. The ethical issue here, however, is more of a structural than a moral dilemma.

The demand for AI is mediated by an economy where each participant has different interests. At the risk of sounding like a vulgar Marxist, owners of capital have an interest in a form of capital that does not need to pay human labour to produce goods. This sets up a tremendous interest to maximize the capabilities of capital and to seek out a capital that can produce goods without having to exchange value for labour. Previous technologies expanded the need for labour by extending the universe that could be produced and profited from, but AI represents a potentially more dismal outcome for those who only have labour to profit from: AI is able to contain, replicate, and refine labour.

The difference of interests is reflected in AI's creation. AI would not have been as immediately profitable if it had to pay for all the labour required to create it. The invention of neural nets sets up the possibility of storing our collective and most effective efforts into a tool that can take credit for it. It is the brilliant invention that finally figured out to maximally extract the value of our gift economies (stack overflow, github, social media, and other tools that accrued value in coordinating our tendency to give up information and ideas for social honor alone) and convert it into an organized and profitable reflection of our generosity. While a process already set forth by search algorithms and social media, this system shows that it was designed to construct virtual gift economies with an interest of capturing free labour. Previously, however, the most these algorithms could do was surveil us. Now, in a clumsy and rapidly improving way, AI has shown that this data has learned us with an interest to filtering the useless aspects of us (our hedonism, chaoticness, value-uncertainty and narrowness) to what is profitable about us (service, concrete problem-solving, value-plurality)
