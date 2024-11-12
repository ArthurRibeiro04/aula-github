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
