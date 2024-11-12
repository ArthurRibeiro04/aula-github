# Refused Bequest

## O que é Refused Bequest

O code smell conhecido como Refused Bequest ocorre quando uma subclasse herda métodos ou propriedades de uma superclasse, mas não os utiliza ou os sobrescreve para lançar exceções. Isso indica que a subclasse não deseja ou não precisa da funcionalidade herdada, sugerindo uma hierarquia de classes inadequada.

```java

public class Animal {
    public void makeSound() {
        System.out.println("Som de animal");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        throw new UnsupportedOperationException("Cachorros Latem!");
    }
}

```

Neste exemplo, a classe Dog herda de Animal, mas sobrescreve o método makeSound para lançar uma exceção, indicando que não deseja a implementação herdada.

## Refatoração

Uma abordagem para resolver o Refused Bequest é substituir a herança por delegação (Replace Inheritance with Delegation.). Isso envolve remover a relação de herança e, em vez disso, incluir uma instância da classe original como um campo na nova classe, delegando a ela as responsabilidades necessárias.

```java

public class Animal {
    public void makeSound() {
        System.out.println("Som de animal");
    }
}

public class Dog {
    private Animal animalDelegate = new Animal();

    public void bark() {
        System.out.println("Au Au!");
    }

    // Outros comportamentos específicos de Cachorros
}

```

## Correção

A classe Dog não herda mais de Animal, eliminando a relação de herança inadequada.

Dog agora possui uma instância privada de Animal (animalDelegate). Isso permite que Dog utilize funcionalidades de Animal quando necessário, sem estar preso a uma hierarquia de herança.

Foi adicionado o método bark à classe Dog, representando o comportamento específico de um cachorro.


Referência: https://github.com/Pramoth-N/Advanced-Java/blob/master/day3-Polymorphism/workout/AnimalSound.java.


# Shotgun Surgery

## O que é Shotgun Surgery

O code smell conhecido como Shotgun Surgery ocorre quando uma única alteração no sistema requer modificações em múltiplas classes ou métodos espalhados pelo código. Isso dificulta a manutenção e aumenta a probabilidade de erros, pois uma pequena mudança pode ter impactos em diversas partes do sistema.

```java

public class Account {
    private String type;
    private String accountNumber;
    private int amount;

    public Account(String type, String accountNumber, int amount) {
        this.amount = amount;
        this.type = type;
        this.accountNumber = accountNumber;
    }

    public void debit(int debit) throws Exception {
        if (amount <= 500) {
            throw new Exception("Minimum balance should be over 500");
        }
        amount = amount - debit;
        System.out.println("Now amount is" + amount);
    }

    public void transfer(Account from, Account to, int creditAmount) throws Exception {
        if (from.amount <= 500) {
            throw new Exception("Minimum balance should be over 500");
        }
        to.amount = to.amount + creditAmount;
    }

    public void sendWarningMessage() {
        if (amount <= 500) {
            System.out.println("Amount should be over 500");
        }
    }
}

```

Neste exemplo, a classe Account possui métodos que verificam se o saldo é superior a 500 antes de realizar operações. Essa verificação é repetida em vários métodos.

## Refatoração

Para resolver esse problema, podemos extrair a lógica de verificação para um método privado comum, evitando a repetição de código e facilitando futuras manutenções (Extract Method).

```java

public class Account {
    private String type;
    private String accountNumber;
    private int amount;

    public Account(String type, String accountNumber, int amount) {
        this.amount = amount;
        this.type = type;
        this.accountNumber = accountNumber;
    }

    private void validateMinimumBalance() throws Exception {
        if (amount <= 500) {
            throw new Exception("Minimum balance should be over 500");
        }
    }

    public void debit(int debit) throws Exception {
        validateMinimumBalance();
        amount = amount - debit;
        System.out.println("Now amount is" + amount);
    }

    public void transfer(Account to, int creditAmount) throws Exception {
        validateMinimumBalance();
        to.amount = to.amount + creditAmount;
    }

    public void sendWarningMessage() {
        if (amount <= 500) {
            System.out.println("Amount should be over 500");
        }
    }
}

```

## Correção

Criamos o método privado validateMinimumBalance() que contém a lógica de verificação do saldo mínimo.

Nos métodos debit() e transfer(), substituímos a verificação direta pela chamada ao método validateMinimumBalance(), eliminando a duplicação de código.

Com a lógica centralizada, futuras alterações na regra de saldo mínimo podem ser feitas em um único local, reduzindo a chance de erros e facilitando a manutenção.

Referência: https://gist.github.com/jimmykurian/1732868.


# Dead Code

## O que é Dead Code?

O code smell conhecido como Dead Code refere-se a partes do código que nunca são executadas ou utilizadas, como funções, classes ou variáveis não referenciadas. A presença de código morto pode aumentar a complexidade do sistema e dificultar sua manutenção.

```python

class UserManager:
    def __init__(self):
        self.users = []

    def add_user(self, user):
        self.users.append(user)
        print(f"User {user} added.")

    def remove_user(self, user):
        if user in self.users:
            self.users.remove(user)
            print(f"User {user} removed.")
        else:
            print("User not found.")

    def list_users(self):
        for user in self.users:
            print(user)

    # Função não utilizada
    def find_user_by_id(self, user_id):
        for user in self.users:
            if user.get('id') == user_id:
                return user
        return None

    # Função não utilizada
    def update_user_email(self, user_id, new_email):
        for user in self.users:
            if user.get('id') == user_id:
                user['email'] = new_email
                print(f"User {user_id} email updated to {new_email}")
                return
        print("User not found.")

```

Neste código, as funções **find_user_by_id** e **update_user_email** não são chamadas em nenhuma parte do sistema e não são necessárias no contexto atual. Elas representam código morto, o que pode causar confusão para desenvolvedores que acharem que essas funcionalidades ainda estão em uso.

## Refatoração

A abordagem mais direta para lidar com código morto é identificá-lo e removê-lo do código-base. Boas IDE`s podem auxiliar na detecção de tais trechos.

```python

class UserManager:
    def __init__(self):
        self.users = []

    def add_user(self, user):
        self.users.append(user)
        print(f"User {user} added.")

    def remove_user(self, user):
        if user in self.users:
            self.users.remove(user)
            print(f"User {user} removed.")
        else:
            print("User not found.")

    def list_users(self):
        for user in self.users:
            print(user)

```

## Correção

Identificamos que as funções find_user_by_id e update_user_email não são utilizadas em nenhuma parte do sistema.

Removemos ambas as funções para simplificar o código, tornando-o mais limpo e fácil de entender.

Com menos código morto, o sistema fica mais legível e o entendimento das funcionalidades ativas se torna mais claro.

Código feito por nós, com base nos códigos neste repositório: https://github.com/jendrikseipp/vulture.
