# Singleton:
O padrão Singleton é um padrão de projeto criacional que garante que uma classe tenha apenas uma instância, fornecendo um ponto de acesso global para essa instância. Ele resolve dois problemas principais:

Primeiro ele garante que uma classe tenha apenas uma única instância, útil para controlar o acesso a recursos compartilhados, como uma base de dados ou arquivo.

Segundo ele fornece um ponto de acesso global para essa instância, evitando que o código sobrescreva a instância e mantendo-a acessível de qualquer lugar do programa.

A implementação do Singleton geralmente envolve tornar o construtor da classe privado e fornecer um método estático de criação que retorna a única instância da classe, criando-a se ainda não existir e armazenando-a em um campo estático.

Uma analogia com o mundo real seria o governo de um país, que é uma única entidade globalmente acessível e tem apenas uma instância, independentemente das pessoas que o compõem.

O uso do Singleton é muito útil para para controlar o acesso a algum recurso compartilhado—por exemplo, uma base de dados ou um arquivo.

## Exemplo:
```python:
class SingletonMeta(type):
    """
    The Singleton class can be implemented in different ways in Python. Some
    possible methods include: base class, decorator, metaclass. We will use the
    metaclass because it is best suited for this purpose.
    """

    _instances = {}

    def __call__(cls, *args, **kwargs):
        """
        Possible changes to the value of the `__init__` argument do not affect
        the returned instance.
        """
        if cls not in cls._instances:
            instance = super().__call__(*args, **kwargs)
            cls._instances[cls] = instance
        return cls._instances[cls]


class Singleton(metaclass=SingletonMeta):
    def some_business_logic(self):
        """
        Finally, any singleton should define some business logic, which can be
        executed on its instance.
        """

        # ...


if __name__ == "__main__":
    # The client code.

    s1 = Singleton()
    s2 = Singleton()

    if id(s1) == id(s2):
        print("Singleton works, both variables contain the same instance.")
    else:
        print("Singleton failed, variables contain different instances.")
```

Acima temos o codigo exemplo fornecido no site refactoring.guru, que implementa um singleton, codigo este que poderiamos facilmente modificar na função "some_business_logic()", para por exemplo se comunicar com um arquivo de texto,pois usando deste padrão podemos garantir que ira existir apenas uma instancia dessa classe, assim evitando conflitos de escrita e leitura. 

## UML Singleton:
![UML Singleton](UMLSingleton.png)

# Decorator:

O padrão Decorator permite adicionar novos comportamentos aos objetos, envolvendo-os em invólucros que contêm esses comportamentos. Ele resolve o problema de estender o comportamento de objetos de forma dinâmica, sem a necessidade de criar subclasses para cada combinação possível de comportamentos.

O padrão emprega a composição ao invés de herança, permitindo que os objetos sejam envolvidos em invólucros que adicionam novos comportamentos. Cada invólucro (decorator) implementa a mesma interface que o objeto alvo, de modo que o cliente pode interagir com ele da mesma forma que interage com o objeto original.

No exemplo de uma biblioteca de notificações, diferentes tipos de notificação (email, SMS, Facebook, Slack) são implementados como decorators que envolvem o objeto base de notificação. O cliente pode criar pilhas complexas de decorators para configurar as preferências de notificação.

Uma analogia comum para entender o padrão Decorator é vestir-se com roupas. Adicionamos camadas de roupas adicionais (decorators) para estender nosso comportamento básico (objeto original), como vestir um casaco para nos aquecer ou uma capa de chuva para nos proteger da chuva.

O padrão Decorator proporciona flexibilidade e extensibilidade ao permitir a adição dinâmica de comportamentos aos objetos, sem a necessidade de modificar sua estrutura original.

## Exemplo:

```python:
class Component():
    """
    The base Component interface defines operations that can be altered by
    decorators.
    """

    def operation(self) -> str:
        pass


class ConcreteComponent(Component):
    """
    Concrete Components provide default implementations of the operations. There
    might be several variations of these classes.
    """

    def operation(self) -> str:
        return "ConcreteComponent"


class Decorator(Component):
    """
    The base Decorator class follows the same interface as the other components.
    The primary purpose of this class is to define the wrapping interface for
    all concrete decorators. The default implementation of the wrapping code
    might include a field for storing a wrapped component and the means to
    initialize it.
    """

    _component: Component = None

    def __init__(self, component: Component) -> None:
        self._component = component

    @property
    def component(self) -> Component:
        """
        The Decorator delegates all work to the wrapped component.
        """

        return self._component

    def operation(self) -> str:
        return self._component.operation()


class ConcreteDecoratorA(Decorator):
    """
    Concrete Decorators call the wrapped object and alter its result in some
    way.
    """

    def operation(self) -> str:
        """
        Decorators may call parent implementation of the operation, instead of
        calling the wrapped object directly. This approach simplifies extension
        of decorator classes.
        """
        return f"ConcreteDecoratorA({self.component.operation()})"


class ConcreteDecoratorB(Decorator):
    """
    Decorators can execute their behavior either before or after the call to a
    wrapped object.
    """

    def operation(self) -> str:
        return f"ConcreteDecoratorB({self.component.operation()})"


def client_code(component: Component) -> None:
    """
    The client code works with all objects using the Component interface. This
    way it can stay independent of the concrete classes of components it works
    with.
    """

    # ...

    print(f"RESULT: {component.operation()}", end="")

    # ...


if __name__ == "__main__":
    # This way the client code can support both simple components...
    simple = ConcreteComponent()
    print("Client: I've got a simple component:")
    client_code(simple)
    print("\n")

    # ...as well as decorated ones.
    #
    # Note how decorators can wrap not only simple components but the other
    # decorators as well.
    decorator1 = ConcreteDecoratorA(simple)
    decorator2 = ConcreteDecoratorB(decorator1)
    print("Client: Now I've got a decorated component:")
    client_code(decorator2)
```

Acima temos o codigo exemplo fornecido no site refactoring.guru, nele, temos uma estrutura de classes que implementam o padrão Decorator. Cada componente pode ser decorado com funcionalidades adicionais sem que isso afete a interface comum definida pela classe Component. Isso permite uma grande flexibilidade na composição de funcionalidades em tempo de execução. O cliente pode trabalhar tanto com componentes simples quanto com componentes decorados, pois todos eles seguem a mesma interface definida por Component. Isto é util pois da ao mesmo uma grande flexibilidade, para por exemplo lidar com uma variedade de tipos de notificações, presentes em uma biblioteca de notificações, assim como citamos anteriormente.

## UML Decorator:
![UML Decorator](UMLDecorator.png)

# Observer:

O Observer é um padrão de projeto comportamental que permite notificar múltiplos objetos sobre quaisquer eventos que aconteçam com um objeto observado.

O problema surge quando um objeto precisa notificar outros sobre suas mudanças de estado, mas não quer depender diretamente desses objetos observadores. Isso evita que o objeto observado e os observadores fiquem fortemente acoplados.

A solução oferecida pelo padrão Observer envolve o estabelecimento de um mecanismo de assinatura. O objeto observado mantém uma lista de observadores interessados em seus eventos. Quando ocorre um evento de interesse, o objeto observado notifica todos os observadores através de um método de notificação comum.

Essa abordagem promove um desacoplamento entre o objeto observado e os observadores, garantindo que eles possam reagir de forma independente a eventos sem conhecer detalhes internos uns dos outros.

Um exemplo comum é a assinatura de revistas e jornais, onde os assinantes recebem novas edições diretamente em suas caixas de correio após a publicação, sem a necessidade de verificar ativamente a disponibilidade da próxima edição.

## Exemplo:

```python:
from __future__ import annotations
from abc import ABC, abstractmethod
from random import randrange
from typing import List


class Subject(ABC):
    """
    The Subject interface declares a set of methods for managing subscribers.
    """

    @abstractmethod
    def attach(self, observer: Observer) -> None:
        """
        Attach an observer to the subject.
        """
        pass

    @abstractmethod
    def detach(self, observer: Observer) -> None:
        """
        Detach an observer from the subject.
        """
        pass

    @abstractmethod
    def notify(self) -> None:
        """
        Notify all observers about an event.
        """
        pass


class ConcreteSubject(Subject):
    """
    The Subject owns some important state and notifies observers when the state
    changes.
    """

    _state: int = None
    """
    For the sake of simplicity, the Subject's state, essential to all
    subscribers, is stored in this variable.
    """

    _observers: List[Observer] = []
    """
    List of subscribers. In real life, the list of subscribers can be stored
    more comprehensively (categorized by event type, etc.).
    """

    def attach(self, observer: Observer) -> None:
        print("Subject: Attached an observer.")
        self._observers.append(observer)

    def detach(self, observer: Observer) -> None:
        self._observers.remove(observer)

    """
    The subscription management methods.
    """

    def notify(self) -> None:
        """
        Trigger an update in each subscriber.
        """

        print("Subject: Notifying observers...")
        for observer in self._observers:
            observer.update(self)

    def some_business_logic(self) -> None:
        """
        Usually, the subscription logic is only a fraction of what a Subject can
        really do. Subjects commonly hold some important business logic, that
        triggers a notification method whenever something important is about to
        happen (or after it).
        """

        print("\nSubject: I'm doing something important.")
        self._state = randrange(0, 10)

        print(f"Subject: My state has just changed to: {self._state}")
        self.notify()


class Observer(ABC):
    """
    The Observer interface declares the update method, used by subjects.
    """

    @abstractmethod
    def update(self, subject: Subject) -> None:
        """
        Receive update from subject.
        """
        pass


"""
Concrete Observers react to the updates issued by the Subject they had been
attached to.
"""


class ConcreteObserverA(Observer):
    def update(self, subject: Subject) -> None:
        if subject._state < 3:
            print("ConcreteObserverA: Reacted to the event")


class ConcreteObserverB(Observer):
    def update(self, subject: Subject) -> None:
        if subject._state == 0 or subject._state >= 2:
            print("ConcreteObserverB: Reacted to the event")


if __name__ == "__main__":
    # The client code.

    subject = ConcreteSubject()

    observer_a = ConcreteObserverA()
    subject.attach(observer_a)

    observer_b = ConcreteObserverB()
    subject.attach(observer_b)

    subject.some_business_logic()
    subject.some_business_logic()

    subject.detach(observer_a)

    subject.some_business_logic()
```

Acima temos o codigo exemplo fornecido no site refactoring.guru, nele, estamos lidando com o padrão Observer, o mesmo é usado para notificar múltiplos objetos sobre quaisquer eventos que aconteçam com o objeto que eles estão observando. Neste exemplo, temos duas interfaces principais: Subject e Observer. A Subject define métodos para gerenciar assinantes (observadores). Ela declara métodos para anexar um observador, desanexar um observador e notificar todos os observadores sobre um evento, e a classe ConcreteSubject implementa tal interface. Ela possui um estado importante e notifica os observadores quando o estado muda. Ela mantém uma lista de observadores e fornece métodos para anexar, desanexar e notificar observadores. Enquanto isso a interface Observer define o método update, que é usado pelos Subjects para notificar os observadores e as classes ConcreteObserverA e ConcreteObserverB implementam tal interface e reagem aos eventos emitidos pelo Subject ao qual foram anexadas. Tal padrão é muito util em aplicações dependentes de dados em tempo real, por exemplo se eu possuir uma lista de salas disponiveis para reserva, eu poderia ter diversos assinantes, que no caso seriam usuários que teriam sua lista local atualizada, assim que fosse observado uma alteração na lista de salas disponiveis na nuvem.

## UML Observer:
![UML Observer](UMLObserver.png)



