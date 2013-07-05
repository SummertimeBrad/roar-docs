# Roar Test Suites

Roar bundles with a detailed set of **Unit tests** and **Functional tests** that can be run by executing the instructions below. These tests are an excellent way to delve into how Roar works, if you're looking to make any code level changes.

## Unit Tests

* We need [saru](https://github.com/roarengine/saru)!

    ~~~
    git clone git@github.com:roarengine/saru.git
    ~~~

* We need [google mock](https://code.google.com/p/googlemock/)

    ~~~
    sudo apt-get install google-mock (THIS SEEMS TO NOT WORK)
    Build from source
    curl https://googlemock.googlecode.com/files/gmock-1.6.0.zip -o gmock-1.6.0.zip
    sudo apt-get install unzip
    unzip gmock-1.6.0.zip
    cd gmock-1.6.0
    g++ -isystem ./gtest/include -I./gtest -isystem ./include -I. -pthread -c ./gtest/src/gtest-all.cc
    g++ -isystem ./gtest/include -I./gtest -isystem ./include -I. -pthread -c ./src/gmock-all.cc
    ar -rv libgmock.a gtest-all.o gmock-all.o
    cd ..
    ~~~

* We need a few environment variables

    ~~~
    export PATH=$PATH:~/Code/saru/src/
    export SARUDIR=~/Code/saru/
    export GMOCKDIR=~/Code/gmock-1.6.0/
    ~~~

* Run the tests:

    ~~~
    cd chameleon/engine/tests
    make suite
    ~~~

    Should get `741 / 742 [ 11 skipped ]`

## Functional Tests

**WARNING:** If you run this against your real leaderboards and databases you will lose/corrupt your data, and it may be hard to clean up afterwards. Run this on a completely different server to the one you want to have live data on to be safe.

### Disable memcache
Memcache is used by the leaderboard to stores results in memory to reduce the need for the leaderboard PHP layer to talk to the core PHP and C++ layers. However this means that leaderboard data may be "stale" - this slightly out of date data causes tests to fail. So disable memcache before running the tests:

~~~
service memcached stop
~~~

Once you've finished running the tests you can reenable memcache by running

~~~
service memcached start
~~~

### Fix the database settings
There are three files that will need to be tweaked to match your database settings

 * `functional_tests/libs/postgres.py`
 * `leaderboard_server/tests/libs/mysql.py`
 * `functional_tests/libs/mysql.py`

### Switch to using the test config and install it
Edit the last line of `install.bash` to change `config/default` to `config/test`.
Run `sudo ./install.bash`.

### Run the tests

~~~
cd functional_tests
make suite
~~~

**Note**:  Since the tests check handling of many error conditions, it is normal to see error messages in the server log while the tests are running.

These tests mush a lot of the leaderboard settings - so you'll need to rerun the leaderboard database user setup code from the setup instructions.
