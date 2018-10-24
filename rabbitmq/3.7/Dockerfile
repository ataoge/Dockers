FROM rabbitmq:3.7

RUN rabbitmq-plugins enable --offline rabbitmq_mqtt
RUN rabbitmq-plugins enable --offline rabbitmq_web_stomp
RUN rabbitmq-plugins enable --offline rabbitmq_management

EXPOSE 15671 15672 1883 8883 15673 15674