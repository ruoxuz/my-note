Since Redis 2.6 it is possible to pass Redis configuration parameters using the command line directly. This is very useful for testing purposes.

    redis-server --daemonize yes
Check if the process started or not:

    ps aux | grep redis-server
    
https://stackoverflow.com/questions/24221449/want-to-run-redis-server-in-background-nonstop