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
- maintance and upgrading self-owned data warehouse is a pain. multi-day outages due to software and hardware upgrades, replacing failed hardware, etc.
- if we move to cloud, could lower overall costs. we don't need sys admins anymore if we move to cloud.

Benefits of public cloud:
- infinite scale and cpu and ram control is big.
- you don't need sys admins for the on prem system anymore. lower costs. so more time doing software and not infrastructure provisioning work.
- you can tame it once it gets to a certain size with ease. you can even make it go away and spin it up and shut it down when yo udon't need it anymore
- think about if you needed a 300 node data mining workflow. paying for all of this, storing it, installing it, would be so much money. and if you need it only once a year, it sits around all year.
- pay as you go model is great, it's cheaper. scale up or shrink down when we need it.
- but, you give up control, user needs understanding of provisioning, could be more expensive in heavy load scenarios. users need to understand what they are doing (what a cpu is, costs, ram, etc). easy to forget that you left it on until bill arrives too.
- vendor lock in is also a disadvantage, you might become dependent on the vendor. 
- could also increase latency, time to move from one system to another could affect costs. 
- security is good, but if you're like teh NSA you might not be able to use AWS. Nasdaq too.


**Benefits of redshift:**
- low costs. pay per hour, per instance, for every instance (server) in the cluster. as low as $1000 per year per terabyte. using compression, the cost is lower. 
- master slave model, leader node is free
- intra-region backups are free, redshift backs up data to s3 and s3 data is free up to size of redshift cluster size.
- transparent mirroring is free. makes exactly 2 copies of block, chops files into blocks. if you have a 10 tb cluster, it gives you 10 extra tb for free. makes 2.
- monitoring free through cloudwatch service.
- provides columnar storage and automagic compression

**Redshift speed**
- reduced i/o disk and network via compression, columnar storage. transferring less data over network because compression via columnar storage. loading less data into memory too because of columnar storage.
- backups are automatic and transparent
- provision a cluser in 3 minutes
- resizing a cluster to more nodes doesn't bring your cluster down. aws creaets a new cluster with a new leader, puts the old clutser in read-only mode, and copies teh data over.
- deployed into vpc, so it's secure.
- you can load sources of data for Redshift from s3, rds, date pipeline, dynamo, kinesis, emr into Redshift.
- redshift has on demand backups to s3, full snapshot of redshift. never deleted by redshift.

What about disaster recovery?
- does full mirror of all data on 2 drives.
- Kinesis can stream data to 2 different clusters.

**Redshift security**
- security is like an onion, to product stuff in middle
- How is security down: IAM support - can lockdown action by action what users can do. put users into groups and policies to groups or users, policies can say to access only certain schemas.
- policies can be attached to IAM accounts so this limits who can spin up database, delete info, make backups, various API calls.
- encrypted data load to s3 supported if you're like the nasdaq
- vpc support. built for private cloud, gives control into the server
- encrypted SSL, for control and querying data.

- redshift supports native users and groups. controls who can access database and select data, delete data, etc.
- permissions similar to mysql
- native logging of - connections to redshift, users in system, user actions (select columns, created tables, etc)
- also cloudtrail integration - lets you log IAM requests. when you turn cloudtrail on - selects, and requests is logged, and can be dropped to s3 to do audit logging and so forth. 
- both logging is free (s3) cloudtrail is free. and native logging is free

## Redshift monitoring**
- native cloudwatch metrics
- query and load performance data 

redshift is very compatible
- jdbc and odc endpoints
- can write sql, based around postgresql 8.0.2. the leader ndoe exposes postgres interface
- but removes postgresl OLTP single row functions

