[![Build Status](https://travis-ci.org/wtanaka/ansible-role-apache-kafka.svg?branch=master)](https://travis-ci.org/wtanaka/ansible-role-apache-kafka)
[![CircleCI](https://circleci.com/gh/wtanaka/ansible-role-apache-kafka.svg?style=svg)](https://circleci.com/gh/wtanaka/ansible-role-apache-kafka)

wtanaka.apache-kafka
====================

Installs Apache kafka.  Keep it up via monit.

Requires:

* Java install, e.g. using https://galaxy.ansible.com/wtanaka/oracle-java/
* Zookeeper install, e.g. using https://galaxy.ansible.com/wtanaka/zookeeper/

Example Playbook
----------------

Just including the role will install a simple default installation

    - hosts: servers
      roles:
         - role: wtanaka.apache-kafka

You can configure the role in the playbook:

    - hosts: servers
      roles:
         - role: wtanaka.apache-kafka
           # Does not support embedded single quote characters
           kafka_env_variables:
             KAFKA_HEAP_OPTS: "-Xmx512m -Xms512m"

Or using variables in an appropriate `host_vars` or `group_vars` file.

The full set of configuration options available are visible in
[defaults/main.yml](defaults/main.yml)

      kafka_version: "0.10.0.1"
      kafka_scala_version: "2.11"
      kafka_mirror: 'http://archive.apache.org/dist/kafka'
      kafka_install_chdir: "/opt"
      kafka_user: "kafka"
      kafka_monit_conf_dir: "/etc/monit/conf.d"
      kafka_init_wrapper_prefix: "kafka-"
      kafka_pid_prefix: "/var/run/kafka-"
      kafka_config_prefix: "server-"
      kafka_log_prefix: "/var/log/kafka-"
      # kafka_delete_topic_enable: "false"

      # kafka_brokers is a list of brokers to run on the machine.
      # Usually you'd run one broker.  Each broker needs to specify
      # either:
      #
      # A broker_id
      #
      # A file_basename and a log_dirs -- this will automatically
      # generate a broker id in Kafka 0.9+
      kafka_brokers:
      - broker_id: 0
        listeners: "PLAINTEXT://:9092"
      - listeners: "PLAINTEXT://:9093"
        file_basename: kafka-9093
        log_dirs: /var/lib/kafka/broker-9093
      kafka_config_contents:
        # The number of threads handling network requests
        num.network.threads: 3
        # The number of threads doing disk I/O
        num.io.threads: 8
        # The send buffer (SO_SNDBUF) used by the socket server
        socket.send.buffer.bytes: 102400
        # The receive buffer (SO_RCVBUF) used by the socket server
        socket.receive.buffer.bytes: 102400
        # The maximum size of a request that the socket server will accept
        # (protection against OOM)
        socket.request.max.bytes: 104857600
        # The default number of log partitions per topic. More partitions
        # allow greater parallelism for consumption, but this will also
        # result in more files across the brokers.
        num.partitions: 1
        # The number of threads per data directory to be used for log
        # recovery at startup and flushing at shutdown.  This value is
        # recommended to be increased for installations with data dirs
        # located in RAID array.
        num.recovery.threads.per.data.dir: 1
        # The minimum age of a log file to be eligible for deletion
        log.retention.hours: 168
        # The maximum size of a log segment file. When this size is reached a new log segment will be created.
        log.segment.bytes: 1073741824
        # The interval at which log segments are checked to see if they can be deleted according
        # to the retention policies
        log.retention.check.interval.ms: 300000
        # Zookeeper connection string (see zookeeper docs for details).
        # This is a comma separated host:port pairs, each corresponding to a zk
        # server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
        # You can also append an optional chroot string to the urls to specify the
        # root directory for all kafka znodes.
        zookeeper.connect: "localhost:2181"
        # Timeout in ms for connecting to zookeeper
        zookeeper.connection.timeout.ms: "6000"
      kafka_more_config: {}
      # Set environment variables here, like KAFKA_HEAP_OPTS or
      # KAFKA_JVM_PERFORMANCE_OPTS etc.  Does not support embedded single
      # quote "'" characters.
      kafka_env_variables:
        KAFKA_HEAP_OPTS: "-Xmx1G -Xms1G"

### `kafka_monit_enabled`

Default: `true`

Set this to `false` to disable installation of a monit `.conf` file and to avoid setting monit service to `started`

### `kafka_user`

Default: `kafka`

The operating system user that the kafka process will run as.  This role will also ensure that this user exists on the operating system.

License
-------

GPLv2

Author Information
------------------

http://wtanaka.com/
