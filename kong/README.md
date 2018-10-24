#迁移数据
docker run --rm -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=192.168.200.139" -e "KONG_PG_PASSWORD=chinadci" -e "KONG_PG_USER=kong" -e "KONG_PG_DATABASE=kong" kong kong migrations up

#运行KONG
docker run  -d --name kong -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=192.168.200.139" -e "KONG_PG_PASSWORD=chinadci" -e "KONG_PG_USER=kong" -e "KONG_PG_DATABASE=kong" -e "KONG_PROXY_ACCESS_LOG=/dev/stdout"  -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" -e "KONG_PROXY_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" -p 8000:8000 -p 8443:8443 -p 8001:8001 -p 8444:8444 kong


#KONG-Dashboard
docker run -d --name dashboard --link kong:kong -p 8080:8080 pgbi/kong-dashboard start   --kong-url http://kong:8001   --basic-auth admin=kong
