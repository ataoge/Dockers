#用法
创建一个测试数据库CentOS

docker run --name testdb -p 5432:5432 -v /data/postgis/testdb:/var/lib/postgresql/data:z -e POSTGRES_PASSWORD=chinadci -e POSTGRES_DB=test -e POSTGRES_USER=test -d ataoge/postgis25