PHP front-end for Supervisor daemon manager
========================================================================

This library provides a basic PHP interface to connect to a running Supervisor server via a TCP or UNIX socket.

Supervisor allows you to manage daemon processes, you can:
-   Group Processes
-   Start + Stop Processes & Process Groups
-   Tail stdout and stderr
-   Send stdin
-   Read process logs

The PHP API matches the Supervisor XML-RPC API as closely as possible and therefore
is not the ideal PHP interface for process management.


Feel free to extend, or build higher level abstractions on top of this library.

Install
--------

```bash
sh install.sh
```

Example Usage
-------------

```php
use \Supervisord\Connection\Stream;
use \Supervisord\Connection\StreamConnection;
use \Supervisord\Connection\StreamException;

/** @see example.php */

// Connect to server
try {
    $stream = new Stream( '127.0.0.1:9900' );
    $connection = new StreamConnection( $stream );
}

// Connection Error
catch( StreamException $e ) {
    die( "Can't connect to server at 127.0.0.1:9900\n" );
}

// Create client
$client = new Client( $connection );

// Add a group
$client->addGroup( 'log', 999 );

// Create a new process
$client->addProgramToGroup( 'log', 'syslog01', array(
    'command' => 'tail -f /var/log/syslog',
    'autostart' => 'false',
    'autorestart' => 'true',
    'startsecs' => 1,
));

// Start the process
$client->startProcess( 'log:syslog01', 'false' );

// Start the whole group
$client->startProcessGroup( 'log', 'false' );

// Output single process info
printf( "Single Process:\n%s\n", print_r( $client->getProcessInfo( 'log:syslog01' ), true ) );

// Output process stdout
printf( "StdOut:\n%s\n", print_r( $client->tailProcessStdoutLog( 'log:syslog01', 0, 1024 ), true ) );

// Output process stderr
printf( "StdErr:\n%s\n", print_r( $client->tailProcessStderrLog( 'log:syslog01', 0, 1024 ), true ) );

// Send process stdin
$client->sendProcessStdin( 'log:syslog01', 'hello world!' );

// Stop the process
$client->stopProcess( 'log:syslog01', 'false' );

// Stop the whole group
$client->stopProcessGroup( 'log', 'false' );

// Add a group
$client->removeProcessGroup( 'log' );

// Reset server
$client->reloadConfig();
```

Tests
--------

```bash
phpunit
```

Travis CI
---------

![travis-ci](http://cdn-ak.favicon.st-hatena.com/?url=http%3A%2F%2Fabout.travis-ci.org%2F)&nbsp;http://travis-ci.org/#!/missinglink/supervisord

![travis-ci](https://secure.travis-ci.org/missinglink/supervisord.png?branch=master)

Regenerate the client library after installing RPC extensions
-------------------------------------------------------------

```bash
php reflect.php
```

License
------------------------

Released under the MIT(Poetic) Software license

    This work 'as-is' we provide.
    No warranty express or implied.
    Therefore, no claim on us will abide.
    Liability for damages denied.

    Permission is granted hereby,
    to copy, share, and modify.
    Use as is fit,
    free or for profit.
    These rights, on this notice, rely.
