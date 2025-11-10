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
# 2. COMPRENDRE L'ENVIRONNEMENT DE TRAVAIL 

![Sparkify Data Model](/images/schemal_Model_des_donnees.png)   

# 3. CRÉATION DES TABLES 

```sql

DROP TABLE IF EXISTS departement; 
 CREATE TABLE departement (
    id SERIAL PRIMARY KEY, 
    nom VARCHAR(50) NOT NULL, 
    localisation VARCHAR(100), 
    budget DECIMAL(15,2) DEFAULT 0 CHECK (budget>=0),
    date_creation DATE DEFAULT CURRENT_DATE CHECK (date_creation <= CURRENT_DATE) 
); 
 
-- CREATION DE LA TABLE EMPLOYES
 DROP TABLE IF EXISTS employes; 
 CREATE TABLE employes(
    id SERIAL PRIMARY KEY, 
    nom VARCHAR(50) NOT NULL, 
    prenom VARCHAR(50) NOT NULL, 
    email VARCHAR(100) UNIQUE NOT NULL CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'), 
    telephone VARCHAR(20), 
    salaire DECIMAL(10,2)  CHECK (salaire >=0), 
    date_embauche DATE DEFAULT CURRENT_DATE CHECK (date_embauche <= CURRENT_DATE), 
    poste VARCHAR(100), 
    departement_id INTEGER, 
    FOREIGN KEY (departement_id) REFERENCES departement(id) ON DELETE SET NULL

 ); 

 -- INSERTION DES DONNEES DANS LES TABLES 
 INSERT INTO departement (nom, localisation, budget) VALUES
('Informatique', 'Bâtiment A, Étage 3', 500000.00),
('Ressources Humaines', 'Bâtiment B, Étage 1', 300000.00),
('Marketing', 'Bâtiment A, Étage 2', 400000.00),
('Finance', 'Bâtiment C, Étage 1', 450000.00),
('Production', 'Bâtiment D, RDC', 600000.00);


-- Insertion de données d'exemple dans la table employes
INSERT INTO employes (nom, prenom, email, telephone, salaire, date_embauche, poste, departement_id) VALUES
('Dupont', 'Jean', 'jean.dupont@entreprise.com', '01 23 45 67 89', 45000.00, '2020-03-15', 'Développeur Senior', 1),
('Martin', 'Marie', 'marie.martin@entreprise.com', '01 34 56 78 90', 38000.00, '2021-06-20', 'Chef de Projet', 1),
('Bernard', 'Pierre', 'pierre.bernard@entreprise.com', '01 45 67 89 01', 52000.00, '2019-11-10', 'Responsable RH', 2),
('Dubois', 'Sophie', 'sophie.dubois@entreprise.com', '01 56 78 90 12', 35000.00, '2022-01-30', 'Chargée de Recrutement', 2),
('Moreau', 'Luc', 'luc.moreau@entreprise.com', '01 67 89 01 23', 48000.00, '2020-08-22', 'Responsable Marketing', 3),
('Petit', 'Isabelle', 'isabelle.petit@entreprise.com', '01 78 90 12 34', 42000.00, '2021-09-05', 'Analyste Financier', 4);
```
# 4. INTEROGATION DE LA BASE DE DONNÉES
## LES EMPLOYÉS DONT LE SALAIRE EST SUPÉRIEURE AU SALAIRE MOYEN 
```sql
-- LES EMPLOYÉS DONT LE SALAIRE EST SUPÉRIEUR AU SALAIRE MOYENNE 
SELECT E1.*
FROM EMPLOYES AS E1
JOIN (SELECT AVG(salaire) AS Salaire_Moyenne
		FROM EMPLOYES) AS E2
	ON E1.salaire > E2.salaire_moyenne
```
## LE SALARIÉ LE PLUS PAYÉ DANS CHAQUE DEPARTEMENT 
```sql 
SELECT E1.nom, E1.PRENOM,E1.Salaire, D.nom
FROM EMPLOYES AS E1
				JOIN DEPARTEMENT AS D 
					ON D.ID = E1.DEPARTEMENT_ID
WHERE E1.SALAIRE = (SELECT MAX (E2.SALAIRE)	
					FROM EMPLOYES AS E2
					WHERE E1.DEPARTEMENT_ID = E2.DEPARTEMENT_ID); 
-- ON PEUT REPINDRE CETTE QUESTION D'UNE AUTRE MANIÈRE 
SELECT E1.nom, E1.prenom, D.nom, E1.DEPARTEMENT_ID,E1.SALAIRE
FROM EMPLOYES AS E1
JOIN DEPARTEMENT AS D
	ON E1.DEPARTEMENT_ID = D.ID
WHERE (E1.DEPARTEMENT_ID, E1.SALAIRE) IN (SELECT E2.DEPARTEMENT_ID, MAX(E2.SALAIRE) 
											FROM EMPLOYES E2
											GROUP BY E2.DEPARTEMENT_ID,D.NOM)

```
## LES DEPRATEMENTS QUI N'ONT PAS D'EMPLOYÉS.
```sql
  SELECT *
  FROM DEPARTEMENT AS D 
  WHERE d.ID NOT IN ( SELECT DISTINCT E.DEPARTEMENT_ID
  					FROM EMPLOYES AS E)
-- ON PEUT AUSSI REPONDRE À CETTE QUESTION EN UTILISANT UNE REQUÊTE CORRELÉE
SELECT *
FROM DEPARTEMENT AS D 
WHERE NOT EXISTS (SELECT 1
				 FROM EMPLOYES AS E
				 WHERE  E.DEPARTEMENT_ID = D.ID
)

-- AU LIEU D'UTILISER DES SOUS REQUÊTES QUI EST MOINS OPTIMISÉ, ON PEUT REPONDRE À CETTE QUESTION EN UTILISANT UNE JOINTURE
SELECT *
FROM EMPLOYES AS E
RIGHT JOIN DEPARTEMENT AS D 
ON E.DEPARTEMENT_ID = D.ID
WHERE E.DEPARTEMENT_ID IS NULL
```
## LES DÉPARTEMENTS DONT LE NOMBRE D'EMPLOYÉS EST SUPÉRIEUR À LA MOYENNE GÉNÉRALE 
![Sparkify Data Model](/images/requetes_moyennes_employes.png)   
### ON PEUT REPONDRE À CETTE QUESTION EN UTILISANT LES CTE, C'EST PLUS LISIBLE QUE LES REQUÊTES IMBRIQUÉES 
![Sparkify Data Model](/images/requetes_cte.png)   

