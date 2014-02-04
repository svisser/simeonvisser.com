<!--
.. title: Notes On Amazon RDS Replication Lag
.. slug: notes-on-replication-lag-on-amazon-rds
.. date: 2013/11/13 21:30:00
.. tags: amazon-aws, amazon-rds, cloud, mysql, database
.. link:
.. description:
-->

### What is replication lag? ###

If your system runs on Amazon Relational Database Service (RDS) you may have
opted to configure one or more replicas for your main MySQL database(s).
This means you have a master RDS instance and at least one slave RDS instance
which receives updates from the master. This process is called replication.

Replication ensures that changes made on the master database also happen on
the slave after some period of time. For a variety of reasons this period of time
can increase. For example, a long-running query or erroneous query can cause
replication to slow down or stop entirely. This results in replication lag: changes
made on your main database aren't showing up on the slave replica because the
replica is lagging behind.

### Being informed when replication lag occurs ###

Amazon provides monitoring functionality that can alert
you when replication lag becomes too high.
One of the properties of a slave instance is `Seconds_Behind_Master`
which can be viewed by executing the query `show slave status;` on the slave.

This field contains the replication lag as measured in seconds and it is usually a small
number, such as zero, but it can increase as the lag goes up.
When the number becomes too high you can configure Amazon's CloudWatch
alert system to send you an e-mail.

This works fine as long as replication occurs normally. There are issues which
can cause replication to stop entirely. In that case `Seconds_Behind_Master`
could become `NULL` and you won't receive an e-mail (as that e-mail is only
sent when the delay behind master exceeds a preconfigured threshold).

### Resolving replication issues ###

When there is a replication issue the output of `show slave status;` is
quite useful in debugging and resolving it.

You need to review the values of:

- `Slave_SQL_Running`
- `Last_Error`
- `Last_SQL_Error`

When a particular SQL query failed
on the slave it could be that execution of queries in general has stopped. This
is indicated by `Slave_SQL_Running` having the value `No`.

In that case you'll either need to:

- Remedy the error by fixing the issue that caused the SQL query to fail.
- Decide to resume replication by letting the slave ignore that error.

The former situation can be tricky as it requires you to figure out what
data or query is problematic based on the values of `Last_Error` and
`Last_SQL_Error`. These fields may provide enough information to determine
any incorrect records but this is not always the case.

In the latter case you would execute the following command on the slave:

    CALL mysql.rds_skip_repl_error;

You should only run this command when you've determined that skipping the
SQL query won't lead to inconsistent data or incorrect data on the slave (or,
at least, that this is allowed to occur by skipping that particular SQL query).