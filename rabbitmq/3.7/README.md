#用法

docker run -d --name dcirabbit --hostname dcirabbit -e RABBITMQ_ERLANG_COOKIE='CHINADCI' -p 15672:15672 -p 5672:5672 -p 1883:1883 -p 15674:15674 ataoge/rabbitmq