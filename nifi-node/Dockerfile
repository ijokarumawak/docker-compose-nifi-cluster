FROM mkobit/nifi:1.0.0-openjdk-8
ADD ./nifi.properties conf
ADD ./start-nifi.sh .

EXPOSE 8080 8081 8082 9001
CMD ["./start-nifi.sh"]
