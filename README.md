# Doctrine DBAL Fork with Enhanced Master Slave Support

Powerful database abstraction layer with many features for database schema introspection, schema management and PDO abstraction.

* Master: [![Build Status](https://secure.travis-ci.org/doctrine/dbal.png?branch=master)](http://travis-ci.org/doctrine/dbal)
* 2.3: [![Build Status](https://secure.travis-ci.org/doctrine/dbal.png?branch=2.3)](http://travis-ci.org/doctrine/dbal)
* 2.2: [![Build Status](https://secure.travis-ci.org/doctrine/dbal.png?branch=2.2)](http://travis-ci.org/doctrine/dbal)

## More resources:

* [Website](http://www.doctrine-project.org/projects/dbal.html)
* [Documentation](http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/)
* [Issue Tracker](http://www.doctrine-project.org/jira/browse/DBAL)
* [Downloads](http://github.com/doctrine/dbal/downloads)

## Enhanced Master Slave Support
Current implementation of MasterSlaveConnection in original Doctrine/Dbal is very restrictive. The slave is picked up only when `executeQuery` is used. If appliacation uses `prepare` method in queries, every `SELECT` is done on master and this is bad for performance of master.

This PR changes how and when it picks the slave or master:

1. Slave if master was never picked before and ONLY if `getWrappedConnection`, `query`, `prepare`, or `executeQuery` is used and the query begins with `SELECT` (case insensitive) string.

2. Master picked when `exec`, `executeUpdate`, `insert`, `delete`, `update`, `createSavepoint`, `releaseSavepoint`, `beginTransaction`, `rollback`, `commit` is called.

3. Master picked when `query`, `prepare` or `executeQuery` is called and the query doesn't begin with `SELECT` (case insensitive) string.

4. If master was picked once during the lifetime of the connection it will always get picked afterwards.

5. One slave connection is randomly picked ONCE during a request.
