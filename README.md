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
## 💡 Padrões de Projeto Implementados
1. Strategy Pattern (Para diferentes tipos de transações)
```
// TransactionStrategy.java
public interface TransactionStrategy {
    void execute(Account account, BigDecimal amount);
}

// DepositStrategy.java
@Service
public class DepositStrategy implements TransactionStrategy {
    @Override
    public void execute(Account account, BigDecimal amount) {
        account.setBalance(account.getBalance().add(amount));
    }
}

// WithdrawStrategy.java
@Service
public class WithdrawStrategy implements TransactionStrategy {
    @Override
    public void execute(Account account, BigDecimal amount) {
        if (account.getBalance().compareTo(amount) < 0) {
            throw new InsufficientFundsException();
        }
        account.setBalance(account.getBalance().subtract(amount));
    }
}

// TransactionContext.java
@Service
public class TransactionContext {
    private final Map<TransactionType, TransactionStrategy> strategies;

    @Autowired
    public TransactionContext(List<TransactionStrategy> strategyList) {
        this.strategies = strategyList.stream()
            .collect(Collectors.toMap(
                s -> TransactionType.valueOf(s.getClass().getSimpleName().replace("Strategy", "").toUpperCase()),
                Function.identity()
            ));
    }

    public void executeTransaction(Account account, BigDecimal amount, TransactionType type) {
        TransactionStrategy strategy = strategies.get(type);
        if (strategy == null) {
            throw new IllegalArgumentException("Invalid transaction type");
        }
        strategy.execute(account, amount);
    }
}
```
2. Factory Method (Para criação de contas)
```
// AccountFactory.java
@Component
public class AccountFactory {
    public Account createAccount(AccountType type, String owner, BigDecimal initialBalance) {
        return switch (type) {
            case CHECKING -> new CheckingAccount(owner, initialBalance);
            case SAVINGS -> new SavingsAccount(owner, initialBalance);
            case BUSINESS -> new BusinessAccount(owner, initialBalance);
        };
    }
}
```
3. Observer (Para notificações de transações)
```
// TransactionObserver.java
public interface TransactionObserver {
    void update(Transaction transaction);
}

// EmailNotificationService.java
@Service
public class EmailNotificationService implements TransactionObserver {
    @Override
    public void update(Transaction transaction) {
        // Lógica para enviar e-mail
        System.out.println("Enviando e-mail sobre transação: " + transaction);
    }
}

// TransactionSubject.java
@Service
public class TransactionSubject {
    private final List<TransactionObserver> observers = new ArrayList<>();

    public void addObserver(TransactionObserver observer) {
        observers.add(observer);
    }

    public void notifyObservers(Transaction transaction) {
        observers.forEach(observer -> observer.update(transaction));
    }
}
```
## 🔄 Fluxo Principal
- Criação de Conta (Factory Method)
- Realização de Transações (Strategy Pattern)
- Notificações (Observer Pattern)

## 🚀 Como Executar
- Clone o repositório
- Execute mvn spring-boot:run
- Acesse a documentação Swagger em: http://localhost:8080/swagger-ui.html

## 🌐 Deploy no Railway
1. Crie um arquivo railway.json na raiz:
```
{
  "build": {
    "builder": "maven",
    "buildCommand": "mvn clean package",
    "runCommand": "java -jar target/*.jar"
  }
}
```
