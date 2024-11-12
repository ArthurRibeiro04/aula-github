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


Referência: https://github.com/Pramoth-N/Advanced-Java/blob/master/day3-Polymorphism/workout/AnimalSound.java


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

Referência: https://gist.github.com/jimmykurian/1732868
