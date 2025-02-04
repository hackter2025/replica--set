# replica--set

Use Docker in Linux
  1. docker-compose.yml
       version: '3.8'
        services:
          mongodb:
            user: "1000:1000"
            image: mongo:7.0
            container_name: mongodb
            command: [ 
              "--replSet", "rs0",
              "--bind_ip_all",
              "--auth",
              "--keyFile", "/etc/mongodb-keyfile",
              "--port", "27017"
            ]
            ports:
              - "27017:27017"
            volumes:
              - ./mongo_data:/data/db
              - type: bind
                source: ./mongodb-keyfile
                target: /etc/mongodb-keyfile
                read_only: true
            environment:
              - MONGO_INITDB_ROOT_USERNAME=rms_admin2024
              - MONGO_INITDB_ROOT_PASSWORD=tripleS_admin2024
            healthcheck:
              test: >
                mongosh --username rms_admin2024 --password tripleS_@admin2024 --authenticationDatabase admin 
                --eval "if (!rs.isMaster().ismaster) { rs.initiate({ _id: 'rs0', members: [{ _id: 0, host: 'mongodb:27017' }] }); }"
              interval: 10s
              start_period: 30s
            restart: unless-stopped
        
        volumes:
          mongodb_data:
