# Configuring DBT
## To get started 
Set the warehouse configuration in profiles.yml
Set profiles.yml in <i>user/.dbt/profiles.yml </i>

<br>

## Using the starter project

Try running the following commands:
<br> * dbt run will run the whole models folder

- dbt run
- dbt test

<br>

## Continuous Integration Reference
Awesome 1 hour video, mostly using <b>github actions</b>:
https://www.youtube.com/watch?v=snp2hxxWgqk&t=169s

<br>

## Resources:
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](http://slack.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices <br><br><br>

# Building some examples

## Python Environment with virtualenv
To activate the python environment open the terminal and go to the root of this project, after that execute the following

    # For linux and macos, pip virtualenv or pipenv if needed
    virtualenv env/bin/activate

## Loading sample data into the warehouse and getting started
https://courses.getdbt.com/courses/take/fundamentals/texts/17703453-loading-training-data-into-your-warehouse <br><br>
### BQ: <br>

    select * from `dbt-tutorial.jaffle_shop.customers`;
    select * from `dbt-tutorial.jaffle_shop.orders`;
    select * from `dbt-tutorial.stripe.payment`;

### Snowflake:
    create schema raw.jaffle_shop;

    -- create this one directly in the schema
    create table raw.jaffle_shop.customers
    (
        id integer,
        first_name varchar,
        last_name varchar
    );

    copy into raw.jaffle_shop.customers (id, first_name, last_name)
        from 's3://dbt-tutorial-public/jaffle_shop_customers.csv'
            file_format = (
                type = 'CSV'
                field_delimiter = ','
                skip_header = 1
            )
    ;

    create table raw.jaffle_shop.orders
    (
    id integer,
    user_id integer,
    order_date date,
    status varchar,
    _etl_loaded_at timestamp default current_timestamp
    );

    copy into raw.jaffle_shop.orders (id, user_id, order_date, status)
        from 's3://dbt-tutorial-public/jaffle_shop_orders.csv'
            file_format = (
                type = 'CSV'
                field_delimiter = ','
                skip_header = 1
            )
    ;

    create schema raw.stripe;

    create table raw.stripe.payment (
    id integer,
    orderid integer,
    paymentmethod varchar,
    status varchar,
    amount integer,
    created date,
    _batched_at timestamp default current_timestamp
    );

    copy into raw.stripe.payment (id, orderid, paymentmethod, status, amount, created)
    from 's3://dbt-tutorial-public/stripe_payments.csv'
        file_format = (
            type = 'CSV'
            field_delimiter = ','
            skip_header = 1
        )
    ;

### Redshift <br>
Create Schemas:
    create schema if not exists jaffle_shop
        
    create schema if not exists stripe
Create Tables:

    create table jaffle_shop.customers(
    id integer,
    first_name varchar(50),
    last_name varchar(50)
    )
        
    create table jaffle_shop.orders(
    id integer,
    user_id integer,
    order_date date,
    status varchar(50),
    _etl_loaded_at timestamp default current_timestamp
    )
	
    create table stripe.payment(
    id integer,
    orderid integer,
    paymentmethod varchar(50),
    status varchar(50),
    amount integer,
    created date,
    _batched_at timestamp default current_timestamp
    )

Copy Data (Note: You will need to replace the 'arn:aws:iam::XXXXXXXXXX:role/RoleName' in each of the statements below.) <br>

    copy jaffle_shop.customers( id, first_name, last_name)
    from 's3://dbt-tutorial-public/jaffle_shop_customers.csv'
    iam_role 'arn:aws:iam::XXXXXXXXXX:role/RoleName'
    delimiter ','
    ignoreheader 1
    acceptinvchars
        
    copy jaffle_shop.orders(id, user_id, order_date, status)
    from 's3://dbt-tutorial-public/jaffle_shop_orders.csv'
    iam_role 'arn:aws:iam::XXXXXXXXXX:role/RoleName'
    delimiter ','
    ignoreheader 1
    acceptinvchars

    copy stripe.payment(id, orderid, paymentmethod, status, amount, created)
    from 's3://dbt-tutorial-public/stripe_payments.csv'
    iam_role 'arn:aws:iam::XXXXXXXXXX:role/RoleName'
    delimiter ','
    ignoreheader 1
    acceptinvchars
    Now that you have the training data in your data warehouse, we can start setting up your GitHub repository.

## After getting data loaded, time to create our first table with a model
We'll be running a shell command to create the table proposed by our SQL Select Query <br>
Check the query there.
<br><br>

### To run our model and create our table:
First open your terminal, then go to the root of this dbt project, then open your terminal and run:

    dbt run -m dim_customers 
    

