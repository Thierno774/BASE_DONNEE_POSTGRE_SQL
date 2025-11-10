# Mise PLACDE DE L'ENVIRONNEMENT 
```yaml
version: '3.8'
services:
  db:
    container_name: postgres_container
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: Thierno
      POSTGRES_PASSWORD: Mettre le mot de passe 
      POSTGRES_DB: Thierno_Database
    ports:
      - "5432:5432
    networks:
      - db_network
  pgadmin:
    container_name: pgadmin4_container
    image: dpage/pgadmin4:latest
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: thiernosidybah232@gmail.com
      PGADMIN_DEFAULT_PASSWORD: mettre le mot de pass  
    ports:
      - "5050:80"
    depend_on:
      postgres:
        condition: service_healthy

    networks:
      - db_network
networks:
  db_network:
    driver: bride
```
2 . COMPRENDRE L'ENVIRONNEMENT DE TRAVAIL 

![Sparkify Data Model](/images/Model_de_travail.png)    

