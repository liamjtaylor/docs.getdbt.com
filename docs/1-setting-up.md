---
title: Setting up
id: setting-up
---
<iframe width="640" height="400" src="https://www.loom.com/embed/cb99861ab1034f7fab5fa48529e61f85" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

This tutorial is geared at first-time users who want detailed instructions on
how to go from zero to a deployed dbt project. You'll need a working knowledge
of SQL in order to do this tutorial.

We recommend you go through this project once from beginning to end. Once you've
completed it, you should go back through and read some of the FAQs to broaden
your understanding of dbt.

This tutorial is based on a fictional business named `jaffle_shop`, so you'll
see this name used throughout the project.

In this tutorial, we will be turning the following query into a dbt project that
is tested, documented, and deployed.
```sql
with customers as (

    select
        id as customer_id,
        first_name,
        last_name

    from `dbt-tutorial`.jaffle_shop.customers

),

orders as (

    select
        id as order_id,
        user_id as customer_id,
        order_date,
        status

    from `dbt-tutorial`.jaffle_shop.orders

),

customer_orders as (

    select
        customer_id,

        min(order_date) as first_order_date,
        max(order_date) as most_recent_order_date,
        count(order_id) as number_of_orders

    from orders

    group by 1

),


final as (

    select
        customers.customer_id,
        customers.first_name,
        customers.last_name,
        customer_orders.first_order_date,
        customer_orders.most_recent_order_date,
        coalesce(customer_orders.number_of_orders, 0) as number_of_orders

    from customers

    left join customer_orders using (customer_id)

)

select * from final
```

## Create a BigQuery project
We'll be using BigQuery as our data warehouse in this tutorial since anyone with
a Google Account can access BigQuery, but dbt works with [many data warehouses](https://docs.getdbt.com/docs/supported-databases).
We've created a public dataset for this tutorial that anyone can `select` from.

<iframe width="640" height="400" src="https://www.loom.com/embed/9b8d852c7e754d978209c3a60b53464e" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

1. Go to [the BigQuery console](https://console.cloud.google.com/bigquery) — if
you don't have a BigQuery account you will be asked to create one.
2. Create a new project — you can use the default name for the project.
<div class='text-left'>
    <a href="#" data-featherlight="/img/create-bigquery-project.png">
        <img
            data-toggle="lightbox"
            width="300px"
            alt="Create a BigQuery project"
            src="/img/create-bigquery-project.png"
            class="docImage" />
    </a>
</div>
3. Copy and paste the above query into the BigQuery console to confirm that you
can run it.

### FAQs
* The data in this tutorial is already loaded into BigQuery. [How do I get my
data into my warehouse?](faqs/loading-data)

## Generate BigQuery credentials
In order to let dbt connect to your warehouse, you'll need generate a keyfile.
This is analogous to using a database user name and password with most other
data warehouses.

<iframe width="640" height="400" src="https://www.loom.com/embed/2b5a8ec255bd4dce91374f6941d279e5" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

1. Go to the [BigQuery credential wizard](https://console.cloud.google.com/apis/credentials/wizard). Ensure that your new project is selected at the top of the screen.
2. Generate credentials with the following options:
    * **Which API are you using?** BigQuery API
    * **Are you planning to use this API with App Engine or Compute Engine?** No
    * **Service account name:** `dbt-user`
    * **Role:** BigQuery User
    * **Key type:** JSON
3. Download the JSON file and save it in an easy-to-remember spot, with a clear
filename (e.g. `dbt-user-creds.json`)

### FAQs
* [What privileges does my database user need to use dbt?](faqs/database-privileges)

## Choose the way you want to develop
There’s two main ways of working with dbt:
1. Edit files and run projects using the web-based Integrated Development
Environment (IDE) in **dbt Cloud**.
2. Edit files locally using a code editor, and run projects using the Command
Line Interface (**dbt CLI**).

To use the CLI, it's important that you know some basics of your terminal. In
particular, you should understand `cd`, `ls` and `pwd` to navigate through the
directory structure of your computer easily. As such, if you are new to
programming, we recommend using **dbt Cloud** for this tutorial.

If you wish to use the CLI, please follow the [installation instructions](https://docs.getdbt.com/docs/installation)
for your operating system.