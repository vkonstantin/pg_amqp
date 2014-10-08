pg_amqp 0.3.1
=============

The pg_amqp package provides the ability for postgres statements to directly
publish messages to an [AMQP](http://www.amqp.org/) broker.

Fork features
-------------
- Persistent delivery mode

Building
--------

To build pg_amqp, just do this:

    make
    make install

Loading
-------

Once amqp is installed, you can add it to a database. Add this line to your
postgresql config

    shared_preload_libraries = 'pg_amqp.so'

Then, If you're running PostgreSQL 9.1.0 or greater, loading amqp is as simple
as connecting to a database as a super user and running:

    CREATE EXTENSION amqp;


Using
-----

Insert AMQP broker information (host/port/user/pass) into the
`amqp.broker` table.

    Example: 
    INSERT INTO amqp.broker(host,port,vhost,username,password) 
        VALUES ('localhost', 5672, NULL, 'guest', 'guest') 
        RETURNING broker_id

Declare exchange if need:

    Example: 
    SELECT amqp.exchange_declare(1, 'router', 'direct', false, true, false)

A process starts and connects to PostgreSQL and runs:

    SELECT amqp.publish(broker_id, 'amqp.direct', 'foo', 'message');

    Example: 
    SELECT amqp.publish(1, 'router', null, 'Hello' );

Upon process termination, all broker connections will be torn down.
If there is a need to disconnect from a specific broker, one can call:

    select amqp.disconnect(broker_id);

which will disconnect from the broker if it is connected and do nothing
if it is already disconnected.

