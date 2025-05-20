# replica--set

Use Docker in Linux for run mongodb and set replica
  1. create folder mongodb_data for save data of mongodb amd save setting and de a volumes:
     
         mkdir -p ./mongo_data
         chmod -R 777 ./mongo_data

  2. create key for set auth for database:

         openssl rand -base64 756 > mongodb-key
         chmod 400 mongodb-key
     
  3. use docker-compose.yml and run docker compose up -d:
     
    version: '3.8'

      services:
        mongodb:
          image: mongo:7.0
          command:
            - /bin/sh
            - -c
            - |
              chmod 400 /keys/mongodb-key
              chown 999:999 /keys/mongodb-key
              mongod --replSet rs0 --keyFile /keys/mongodb-key 
          container_name: mongodb
          ports:
            - "27017:27017"
          volumes:
            - ./mongo_data:/data/db
            - ./mongodb-key:/keys/mongodb-key
          environment:
            - MONGO_INITDB_ROOT_USERNAME=admin2024
            - MONGO_INITDB_ROOT_PASSWORD=pass_admin2024
          healthcheck:
            test: >
              mongosh --username admin2024 --password pass_admin2024 --authenticationDatabase admin
              --eval "if (!rs.isMaster().ismaster) { rs.initiate({ _id: 'rs0', members: [{ _id: 0, host: 'mongodb:27017' }] }); }"
            interval: 10s
            start_period: 30s
          restart: unless-stopped
      
      volumes:
        mongodb_data:
        mongodb-keys:

4. go inside container mongodb and use mongosh run command:
          
            1. docker exec -it mongodb /din/sh
            2. mongosh
            3. use admin
            4. db.auth("admin2024","admin2024password")
            5. rs.status() // checker repicaset
   

