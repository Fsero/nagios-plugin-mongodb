# Nagios-MongoDB

## Overview

This is a simple Nagios check script to monitor your MongoDB server(s). 

## Installation

In your Nagios plugins directory run

<pre><code>git clone git://github.com/mzupan/nagios-plugin-mongodb.git</code></pre>

## Usage

### Install in Nagios

Edit your commands.cfg and add the following

<pre><code>
define command {
    command_name    check_mongodb
    command_line    $USER1$/nagios-plugin-mongodb/check_mongodb.py -H $HOSTADDRESS$ -A $ARG1$ -W $ARG2$ -C $ARG3$
}
</code></pre>

Then you can reference it like the following. This is is my services.cfg

#### Check Connection

This will check each host that is listed in the Mongo Servers group. It will issue a warning if the connection to the server takes 2 seconds and a critical error if it takes over 4 seconds

<pre><code>
define service {
    use                 generic-service
    hostgroup_name          Mongo Servers
    service_description     Mongo Connect Check
    check_command           check_mongodb!connect!2!4
}   
</code></pre>

#### Check Percentage of Open Connections

This is a test that will check the percentage of free connections left on the Mongo server. In the following example it will send out an warning if the connection pool is 70% used and a critical error if it is 80% used. 

<pre><code>
define service {
    use                 generic-service
    hostgroup_name          Mongo Servers
    service_description     Mongo Free Connections
    check_command           check_mongodb!connections!70!80
}   
</code></pre>

#### Check Replication Lag

This is a test that will test the replication lag of Mongo servers. It will send out a warning if the lag is over 2 seconds and a critical error if its over 5 seconds

<pre><code>
define service {
    use                 generic-service
    hostgroup_name          Mongo Servers
    service_description     Mongo Replication Lag
    check_command           check_mongodb!replication_lag!2!5
}
</code></pre>

#### Check Memory Usage

This is a test that will test the memory usage of Mongo server. In my example my Mongo servers have 32 gigs of memory so I'll trigger a warning if Mongo uses over 20 gigs of ram and a error if Mongo uses over 28 gigs of memory.

<pre><code>
define service {
    use                 generic-service
    hostgroup_name          Mongo Servers
    service_description     Mongo Memory Usage
    check_command           check_mongodb!memory!20!28
}
</code></pre>

#### Check Lock Time Percentage

This is a test that will test the lock time percentage of Mongo server. In my example my Mongo I want to be warned if the lock time is above 5% and get an error if it's above 10%. When you start to have lock time it generally means your db is now disk bound.

<pre><code>
define service {
    use                 generic-service
    hostgroup_name          Mongo Servers
    service_description     Mongo Lock Percentage
    check_command           check_mongodb!lock!5!10
}
</code></pre>