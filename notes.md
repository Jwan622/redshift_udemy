ETL and why we separate OLTP and OLAP
- many fast reads and writes.
- but if people internal start doing BI work and tryign to see the most popular posts or users, who is abusing the system, etc. then problems can occur.
- if these people make long-running queries, and they run several minutes or hours or days, the database locking and table locking, it might interfere and cause system to be slow.
- instead, you should prune OLTP database to be small, and move data to an OLAP.
- how do we prune? we run an etl operation to an OLAP database. OLAP-online analytics processing system.
- so users of OLTP see speed and quick responses and queries and analytics poeple can run longer queries and see speed improvements too.

OLTP vs OLAP
OLTP
disadvantage:
- not meant for heavy volume or to be distributed
- so the upper limit is just a few TB

advantage:
- AKA RDBMS systems
- fast for small insert and update
- typically row-oriented
- heavily normalized
- heavily concurrent

OLAP
known as DW or data warehouse
- purpose: analytics queries on HUGE datasets
- query speed not priority
- only a few dozen or few hundred users at a time. non-concurrent access pattern.
- storage is more the priority.
- few internal users
- data is prematerialzied or use known queries meaning data just sits there waiting for the query, and when query comes, the data is super optimized for that query pattern.
- typically column-oriented
- aggregated data for speed
- hugely scalable

Data Mart:
- there might be contention within the OLAP systems.  marekint and busineses might want different information but are fighting over single OLAP
- separate OLAP to data marts
- data marts- spsecific to business function, data wareshousse for each business function.
- so our ETL from OLTP to datamart might pull specific data for marketing and business to each data mart or two separate data warehouses but business specific.

what is redshift?
Redshift is OLAP whereas Aurora is OLTP.
joins in a RDMBS or OLTP database would be expensive but OLAP or datawarehouses get around this by prematerializing the data together
- when we pre-materialize data, we load data into cubes.

- what is a data warehouse?
- columar storage: why? In an analytics database when we analyze the data only interested in a small subset of columns, not all of the columns. So if we have like 100 columns and used row-database, it'd be really slow to read out all of the columns. That'd be slow. 

how to describe dimension and fact tables:
- long table: lots of rows
- skinny: few columns in the table itself.
- short: not dealing with a lot of rows in a table
- wide: may have a lot of columns.
fact tables are in the center and dimension tables in the edges. This is a star schema.
- we usually query the fact table.


terms to know:
-data mart
-edw or dw (enterprise data warehouse)
- data cube
- star schema
- facts and dimensions
- columar vs row storage
- etl
queries:
- group by gives you aggregate data

shortcomings of self owned data warehouse:
- eventually we hit a size limit. Our data grows too fast for our OLAP system and it becomes super expensive
- having 6 dimensions of data would push BI into a different licensing tier and a lot more money.
- redshift is a good relatively cheap option for OLAP. Let's look at public cloud data warehouses which are better than self-owned data warehouses


