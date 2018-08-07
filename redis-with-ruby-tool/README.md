`Redis` is an open source key-value store that functions as a data structure server.

## docker-compose.yml

```
version: '3'
services:
  redis1:
    restart: always
    image: iwaltgen/redis-with-ruby-tool
    ports:
      - 6379-6380:6379-6380
      - 16379-16380:16379-16380
    volumes:
      - ./docker/redis.6379.conf:/data/redis.6379.conf
      - ./docker/redis.6380.conf:/data/redis.6380.conf
    working_dir: /data
    command: >
      bash -c "
        redis-server redis.6380.conf &
        redis-server redis.6379.conf"
    networks:
      default:
        ipv4_address: 10.5.0.10

  redis2:
    restart: always
    image: iwaltgen/redis-with-ruby-tool
    ports:
      - 6379-6380
      - 16379-16380
    volumes:
      - ./docker/redis.6379.conf:/data/redis.6379.conf
      - ./docker/redis.6380.conf:/data/redis.6380.conf
    working_dir: /data
    command: >
      bash -c "
        redis-server redis.6380.conf &
        redis-server redis.6379.conf"
    networks:
      default:
        ipv4_address: 10.5.0.11

  redis3:
    restart: always
    image: iwaltgen/redis-with-ruby-tool
    ports:
      - 6379-6380
      - 16379-16380
    volumes:
      - ./docker/redis.6379.conf:/data/redis.6379.conf
      - ./docker/redis.6380.conf:/data/redis.6380.conf
    working_dir: /data
    command: >
      bash -c "
        redis-server redis.6380.conf &
        redis-server redis.6379.conf"
    networks:
      default:
        ipv4_address: 10.5.0.12

  redis-cluster:
    image: iwaltgen/redis-with-ruby-tool
    depends_on:
      - redis1
      - redis2
      - redis3
    working_dir: /data
    command: >
      bash -c "
        sleep 1;
        /usr/local/bin/redis-trib create --replicas 1 \
          10.5.0.10:6379 10.5.0.11:6379 10.5.0.12:6379 \
          10.5.0.11:6380 10.5.0.12:6380 10.5.0.10:6380 <<< 'yes';
        sleep 1;
        /usr/local/bin/redis-trib info 10.5.0.10:6379;"

networks:
  default:
    ipam:
     config:
       - subnet: 10.5.0.0/16
```

### redis-cli

```
$> docker exec -it ${docker-compose-name}/redis1 redis-cli -c
127.0.0.1:6379> set test1 val1
OK
127.0.0.1:6379> get test1
"val1"
127.0.0.1:6379> del test1
(integer) 1
```