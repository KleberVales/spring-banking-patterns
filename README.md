# Sistema Bancário com Padrões de Projeto em Spring Boot
Sistema bancário demonstrando padrões de projeto com Spring Boot 3, Java 17 e deploy no Railway

## 🛠️ Tecnologias Principais
- Java 21
- Spring Boot 3
- Spring Data JPA
- OpenAPI 3 (Swagger)
- Railway (para deploy)
- H2 (banco em memória para desenvolvimento)

## 📂 Estrutura do Projeto

```
banking-system-spring/
├── src/
│   ├── main/
│   │   ├── java/com/banking/system/
│   │   │   ├── config/
│   │   │   ├── controller/
│   │   │   ├── dto/
│   │   │   ├── exception/
│   │   │   ├── model/
│   │   │   ├── repository/
│   │   │   ├── service/
│   │   │   ├── strategy/  # Padrão Strategy
│   │   │   └── BankingSystemApplication.java
│   │   └── resources/
│   │       ├── application.properties
│   │       └── data.sql
├── .gitignore
├── pom.xml
└── README.md
```
