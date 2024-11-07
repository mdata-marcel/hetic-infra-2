### 1. Sélection des composants AWS


Pour déployer notre application sur AWS en respectant les contraintes de simplicité de gestion et de haute disponibilité, voici les composants AWS que nous allons utiliser :

### A. **Amazon Elastic Container Service (ECS) avec AWS Fargate**
   - **Pourquoi ?** : ECS avec Fargate nous permet de déployer nos conteneurs Docker (backend et frontend) sans avoir à gérer l'infrastructure sous-jacente. Fargate est un service entièrement managé et serverless qui s’adapte bien à nos besoins de mise à l’échelle automatique, tout en réduisant les coûts et la complexité d’administration.
   - **Utilisation** : Hébergement de nos deux images Docker (backend et frontend) en tant que services indépendants, déployés et gérés automatiquement.

### B. **Elastic Load Balancer (ELB) - Application Load Balancer (ALB)**
   - **Pourquoi ?** : L'Application Load Balancer (ALB) distribue le trafic réseau de manière intelligente, permettant d'acheminer les requêtes vers nos services backend et frontend en fonction des routes (path-based routing). Cela nous permet de gérer plusieurs services sur la même adresse IP.
   - **Utilisation** : Acheminement du trafic des utilisateurs vers les services backend et frontend déployés sur ECS, garantissant une répartition efficace et résiliente.

### C. **Amazon RDS (Relational Database Service)**
   - **Pourquoi ?** : Amazon RDS est un service de base de données managé qui nous offre des fonctionnalités de sauvegarde, de restauration, et de haute disponibilité en multi-AZ. RDS nous permet de nous concentrer sur notre application sans devoir gérer les aspects techniques de la base de données.
   - **Utilisation** : Hébergement de la base de données pour stocker les informations persistantes de notre application, avec une configuration multi-AZ pour assurer une reprise rapide en cas de panne.

### D. **Amazon S3 (Simple Storage Service)**
   - **Pourquoi ?** : Amazon S3 est parfait pour le stockage de fichiers statiques et de médias. Ce service est durable, sécurisé, et évolutif, ce qui le rend idéal pour nos besoins de stockage de fichiers statiques.
   - **Utilisation** : Stockage des fichiers statiques et des médias de notre application. Nous pouvons également l'utiliser pour conserver des fichiers de configuration si nécessaire.

### E. **Amazon CloudFront (CDN)**
   - **Pourquoi ?** : CloudFront est un service de distribution de contenu qui permet de réduire la latence en servant les fichiers depuis des emplacements géographiquement proches de nos utilisateurs. En le couplant avec des URL signées, nous pouvons également renforcer la sécurité d’accès à nos fichiers.
   - **Utilisation** : Accélérer la livraison des fichiers statiques et des médias de notre application en utilisant S3 comme origine, réduisant ainsi le temps de chargement pour les utilisateurs.

### F. **Amazon Route 53**
   - **Pourquoi ?** : Amazon Route 53 est un service DNS fiable et performant, capable de gérer les requêtes DNS et de rediriger le trafic vers nos services ECS via l'ALB.
   - **Utilisation** : Fournir une URL unique pour accéder à notre application et gérer la résolution DNS de manière efficace.

### G. **Amazon ECR (Elastic Container Registry)**
   - **Pourquoi ?** : Amazon ECR est un registre sécurisé pour stocker et gérer nos images Docker. Il s’intègre parfaitement avec ECS, nous permettant de déployer les conteneurs directement depuis le registre.
   - **Utilisation** : Hébergement de nos images Docker (backend et frontend) pour les rendre accessibles pour le déploiement sur ECS.

### H. **VPC (Virtual Private Cloud)**
   - **Pourquoi ?** : Le VPC nous permet de créer un réseau isolé dans le cloud AWS, avec des sous-réseaux publics et privés, des règles de sécurité, et des stratégies de contrôle d'accès. Il nous offre une sécurité accrue en limitant l'accès aux ressources internes.
   - **Utilisation** : Hébergement de tous les composants de l'application dans un environnement sécurisé, avec des sous-réseaux privés pour ECS, RDS, et des sous-réseaux publics pour l'ALB et le NAT Gateway.

### I. **NAT Gateway**
   - **Pourquoi ?** : La NAT Gateway permet aux instances dans les sous-réseaux privés de se connecter à Internet pour des mises à jour ou pour accéder à des API, sans être exposées directement à Internet.
   - **Utilisation** : Permet aux conteneurs dans ECS de se connecter à Internet en toute sécurité pour récupérer des mises à jour ou accéder à d’autres services externes.

Cette architecture vous offre une solution flexible, évolutive et hautement disponible, tout en minimisant la charge de gestion grâce aux services managés.


### 2 - Schéma de l'infrastructure

## Voir fichier Schéma

## Explication : 

**A. VPC (10.0.0.0/16)** : Contient toute l'infrastructure dans un réseau isolé avec des sous-réseaux publics et privés.

**B. Internet Gateway** : Permet aux ressources publiques (comme l'ALB) d'accéder à Internet.

**C. Application Load Balancer (ALB)** : Placé dans les sous-réseaux publics dans chaque zone de disponibilité, gère le routage du trafic vers les services backend et frontend déployés dans ECS Fargate.

**D. ECS Fargate** : Héberge les services Docker pour le backend et le frontend. Chaque service est placé dans des sous-réseaux privés pour garantir la sécurité.

**E. NAT Gateway** : Permet aux instances dans les sous-réseaux privés de se connecter à Internet sans exposition directe.

**F. Amazon RDS (Multi-AZ)** : Une instance de base de données relationnelle déployée en mode multi-AZ pour garantir la haute disponibilité et la récupération rapide en cas de panne.



### 3 - Justifier vos choix techniques (il faut bien convaincre vos futurs investisseurs !)


Pour convaincre nos investisseurs, voici les justifications techniques pour chaque choix de composant de l'infrastructure. L'objectif est de leur montrer comment cette architecture garantit la **fiabilité**, la **sécurité**, l'**évolutivité** et l'**efficacité des coûts**, tout en **minimisant la charge d'administration**.


### A. **Amazon Elastic Container Service (ECS) avec AWS Fargate**

   - **Explication** : Nous avons choisi ECS avec Fargate pour déployer nos conteneurs Docker (backend et frontend) de manière managée et serverless. Fargate nous libère de la gestion des serveurs, réduisant ainsi nos coûts d'administration et nous permettant de nous concentrer sur le développement et l’amélioration de notre application.
   - **Valeur ajoutée** : Grâce à la mise à l’échelle automatique d’ECS Fargate, nous pouvons ajuster notre capacité en fonction de la demande, optimisant nos coûts tout en répondant efficacement aux pics de trafic.
   - **Avantage concurrentiel** : La flexibilité du serverless nous permet de réagir rapidement aux changements du marché sans surinvestir dans des ressources inutilisées.

### B. **Application Load Balancer (ALB)**

   - **Explication** : L'ALB distribue le trafic entre nos services frontend et backend de manière intelligente, en utilisant le routage basé sur les chemins. Cela nous permet de gérer efficacement le trafic et d'offrir une meilleure expérience utilisateur.
   - **Valeur ajoutée** : En utilisant un ALB multi-AZ, nous assurons la résilience de notre application. Même en cas de défaillance d'une zone de disponibilité, l'application continue de fonctionner, garantissant une disponibilité maximale pour nos utilisateurs.
   - **Avantage concurrentiel** : Une répartition efficace du trafic réduit les temps de réponse et améliore l'expérience utilisateur, augmentant ainsi la satisfaction et la fidélité de nos clients.

### C. **Amazon RDS (Relational Database Service)**

   - **Explication** : Nous utilisons Amazon RDS en mode multi-AZ pour offrir une base de données relationnelle managée, fiable et sécurisée. RDS s’occupe des sauvegardes, des mises à jour de sécurité et de la reprise après sinistre, ce qui nous permet de nous concentrer sur l’évolution de notre application.
   - **Valeur ajoutée** : Le mode multi-AZ de RDS garantit la continuité de service en cas de panne, assurant la haute disponibilité des données, un élément crucial pour nos opérations.
   - **Avantage concurrentiel** : La sécurité et la fiabilité des données renforcent la confiance de nos utilisateurs, réduisant le risque de perte de données et valorisant notre image de marque.

### D. **Amazon S3 et CloudFront**

   - **Explication** : Nous stockons les fichiers statiques de notre application sur Amazon S3, tandis que CloudFront (CDN) les distribue rapidement et de manière sécurisée dans le monde entier.
   - **Valeur ajoutée** : Grâce à CloudFront, nous réduisons la latence pour les utilisateurs finaux en servant les fichiers depuis des emplacements proches d'eux, ce qui améliore leur expérience.
   - **Avantage concurrentiel** : Des temps de chargement rapides pour les ressources statiques différencient notre application en termes de performance, un facteur clé pour retenir et fidéliser nos utilisateurs.

### E. **Amazon Route 53**

   - **Explication** : Nous utilisons Amazon Route 53 pour gérer le DNS de notre application. Il redirige le trafic vers notre ALB, assurant une résolution de nom rapide et des performances optimales.
   - **Valeur ajoutée** : Route 53 est conçu pour des temps de réponse rapides et une haute disponibilité, contribuant à offrir une expérience utilisateur de haute qualité.
   - **Avantage concurrentiel** : Une gestion efficace du DNS assure une disponibilité continue de notre application, essentielle pour maintenir la confiance de nos utilisateurs et maximiser les conversions.

### F. **Amazon ECR (Elastic Container Registry)**

   - **Explication** : ECR nous permet de stocker nos images Docker de manière sécurisée et de les rendre facilement accessibles pour ECS. L’intégration directe avec ECS nous facilite les déploiements automatiques.
   - **Valeur ajoutée** : ECR centralise et sécurise nos images Docker, facilitant la gestion des versions et les déploiements en continu.
   - **Avantage concurrentiel** : En ayant un registre sécurisé pour nos conteneurs, nous accélérons les déploiements et minimisons les risques de sécurité, garantissant ainsi une application toujours à jour et sécurisée.

### G. **VPC (Virtual Private Cloud)**

   - **Explication** : Le VPC nous permet de créer un environnement isolé, avec des sous-réseaux publics pour les composants exposés à Internet (comme l’ALB) et des sous-réseaux privés pour les services sensibles (comme ECS et RDS).
   - **Valeur ajoutée** : En segmentant notre infrastructure, nous renforçons considérablement la sécurité en limitant l'exposition des ressources internes.
   - **Avantage concurrentiel** : La sécurité de notre infrastructure inspire la confiance de nos utilisateurs, un atout pour notre marque et une garantie de protection de leurs données.

### H. **NAT Gateway**

   - **Explication** : La NAT Gateway nous permet de sécuriser les instances dans les sous-réseaux privés en leur offrant un accès sortant à Internet sans les exposer directement.
   - **Valeur ajoutée** : Cette solution renforce la sécurité de l'application, permettant aux ressources internes de récupérer des mises à jour et de se connecter à des services externes sans être accessibles depuis l'extérieur.
   - **Avantage concurrentiel** : En garantissant une protection optimale contre les menaces extérieures, nous augmentons la sécurité globale de notre infrastructure, un point fort pour les utilisateurs soucieux de la sécurité de leurs données.


### Conclusion

Notre choix d'infrastructure AWS managée et serverless assure non seulement la haute disponibilité, la sécurité, et l'évolutivité, mais optimise également les coûts tout en minimisant la charge de gestion. Grâce à cette architecture flexible et robuste, nous nous plaçons dans une position favorable pour innover rapidement, offrir une excellente expérience utilisateur et garantir la confiance de nos clients.

Investir dans cette architecture, c’est garantir une infrastructure stable, évolutive et capable de soutenir notre croissance future tout en assurant un retour sur investissement significatif par une gestion efficace des ressources et des coûts.
