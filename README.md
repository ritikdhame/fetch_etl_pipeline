# Fetch_etl_pipeline
## Fetch Rewards Data Engineering Take Home: ETL off an SQS Queue ETL solution

Please find the repository as the ETL solution for the project "ETL off an SQS Queue."

## Prerequisites:
- Python 3.9 or higher
- Docker
- Docker Compose
- AWS CLI Local (`pip install awscli-local`)

### To run the code
1. Clone this repository.
```bash
git clone https://github.com/ritikdhame/fetch_etl_pipeline.git
```

2. Enter the cloned repo.
```bash
cd fetch_etl_pipeline
```

3. Run `make` command to install dependencies.
```bash
make <command>
```
Some of the option available are

Available options:
**make check-python** : Verify if Python3 is installed
**make check-docker** : Verify if Docker is installed
**make check-docker-compose**: Verify if Docker Compose is installed
**make install-dependencies**: Install all Python application dependencies
**make docker-start**: Start Docker containers for Localstack and Postgres
**make docker-stop**: Stop Docker containers for Localstack and Postgres
**make docker-clean**: Remove unused Docker images
**make python_etl_start**: Run the ETL Python application to transfer data from SQS to Postgres
**make python_decrypt_start**: Run the Python application to decrypt and display data

3.1 Run `make` command to check if python3 is installed.
```bash
make check-python
```

3.2 Run `make` command to check if docker is installed.
```bash
make check-docker
```

3.3 Run `make` command to check if docker-compose is installed.
```bash
make check-docker-compose
```

3.4 Run `make` command to install all python3 library dependencies.
```bash
make install-dependencies
```
      
3.5 Download the docker images localstack and postgres required for the application
```bash
docker pull fetchdocker/data-takehome-postgres
docker pull fetchdocker/data-takehome-localstack
```      
      
3.6 Run `make` command to run and start all docker image required for the application.
```bash
make docker-start
```

3.7 Before pushing the data to postgres we need to alter the table column "app_version" from integer to varchar(16) considering xxx.xxx.xxx.xxx. maximum character long.

      - Database credentials
      - **username**=`postgres`
      - **password**=`postgres`
      - **database**=`postgres`
      
```bash
docker exec -it fetch_postgres_1 /bin/bash
psql -d postgres -U postgres -p 5432 -h localhost -W
alter table user_logins alter column app_version type varchar(16);
``` 

      
3.8 Update cred.ini with AWS, Localstack, Postgres, and hashing credentials
**Note**: Once the salt and key variables are set in the file after the initial run, do not change them as they are required for decryption.
```bash
vi cred.ini
```
      
3.9 Run `make` command start python application to push data from sqs to postgres.
```bash
make python_etl_start
```

4. Run `make` command to stop docker containers.
```bash
make docker-stop
```
    
### Validating data loaded in Postgres    
Once the data is pushed to Postgres connect to postgres and verify

      - Database credentials
      - ** username **=`postgres`
      - ** password **=`postgres`
      - ** database **=`postgres`
      
```bash
docker exec -it fetch_postgres_1 bin/bash
psql -d postgres -U postgres -p 5432 -h localhost -W
SELECT * FROM USER_LOGINS;
``` 



## Decrypting data loaded in Postgres
To decrypt the data, run the following command:
```bash
make python_decrypt_start
```
Enter the query on table user_logins ex **SELECT user_id, masked_ip, masked_device_id FROM USER_LOGINS;**

