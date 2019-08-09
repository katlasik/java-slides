---
title: Wprowadzenie do Javy

---

### Wprowadzenie do Javy

![Java](assets/duke.png)

---

### Klasy

Klasa to częściowa lub całkowita definicja pozwalająca tworzyć obiekty

```java
class Fruit {
   protected String vitamins;
   private String name;
   
   Fruit(String name, String vitamins) {
     this.name = name;
     this.vitamins = vitamins;
   }
   Fruit(String name) {
      this(name, "all");
   }
   public String getName() {
       return name;
   }
   String makeJuice() {
      return name + " juice";
   }
}
```

---

```java
Fruit apple = new Fruit("Jabłko", "A");
Fruit orange = new Fruit("Pomarańcza", "C,D");
Fruit strawberry = new Fruit("Truskawka");
```

---

###### Dziedziczenie

```java
class Apple extends Fruit {
    
   Apple(String vitamins) {
      super("apple",vitamins);
   }
  
  ApplePie makeApplePie() {
    return new ApplePie(this);
  }
}
```

```java
Apple apple = new Apple("A,B,D");

ApplePie pie = apple.makeApplePie();
String appleJuice = apple.makeJuice();

```
---

##### Przeciążanie metod

```java
class Apple extends Fruit {
    
   Apple(String vitamins) {
      super("Jablko",vitamins);
   }
   
   @Override
   String makeJuice() {
       return "Very tasty " +  super();
   }
   
  ApplePie makeApplePie() {
    return new ApplePie(this);
  }
}
```

```java
Apple apple = new Apple("A,B,D");

apple.makeJuice(); // "Very tasty apple juice
```

---

Obiekty stworzone z klasy dziecka mogą być przypisane do refencji typu rodzica:

```java
Fruit appleFruit = new Apple("A);

appleFruit.makeJuice(); // "Very tasty apple juice
appleFruit.makeApplePie(); //błąd kompilacji

Object appleObject = new Apple("A");

```

Na szczycie hierarchii dziedziczenia *Javy* jest klasa `Object` wszystkie klasy niejawnie są jej dziećmi)

---

#### Modifikatory dostępu

|Modyfikator|Klasa|Pakiet|Podklasa|Poza|Poprawny dla klas|
|--- |--- |--- |--- |--- |--- |
|public|tak|tak|tak|tak|tak|
|protected|tak|tak|tak|nie|nie|
|brak|tak|tak|nie|nie|tak|
|private|tak|nie|nie|nie|nie|

---

### Interfejsy

* Interfejsy nie mają konstruktorów i nie można przy ich pomocy tworzyć bezpośrednio nowych obiektów.
* Metody interfejsu domyślnie są publiczne i abstrakcyjne
* Jeżeli interfejs ma pola, to jego metody są statyczne, publiczne i finalne. 
* Interfejs może posiadać domyślne metody
* Od Javy 9 domyślne metody mogą nie być publiczne.

---

###### Przykład z biblioteki standardowej

Interfejs `Comparable` pozwala na porównanie implementujących go obiektów:

```java
interface Comparable {
    int compareTo(T t);
}
```

---

###### Implementacja interfejsu

Klasa implementuje interfejs za pomocą słowa kluczowego `implements`:  

```java
public class Fruit implements Comparable<Fruit> {

    private final int weight;

    public Fruit(int weight) {
        this.weight = weight;
    }


    @Override
    public int compareTo(Fruit f) {
        return Integer.compare(f.weight, this.weight);
    }
}
```

---

Jedna klasa może implementować wiele interfejsów:

```java
interface HasColour {
    String colour();
}

interface HasQuality {
    String quality();
}

public class GreenApple implements HasQuality, HasColour {
   @Override
   public String quality() {
       return "great";
   }
   @Override
   public String colour() {
      return "green";
  }
}
```
---

Interfejs może zostać użyty jako typ referencji. Tylko publiczne metody danego interfejsu będą dostępne: 

```java
HasColour apple = new GreenApple();

apple.colour(); //Zwróci "green"

apple.quality(); //Spowoduje błąd kompilacji
```

---

Możemy również tworzyć obiekty implementujące interfejs bez tworzenia klasy. Są to tak zwane implementacje **ad-hoc**:

```java
HasColour yellowThing = new HasColour() {

    @Override
    public String colour() {
        return "yellow";
    }
    
};

```

---
Jeżeli interfejs posiada tylko jedną metodę, to jest to tak zwany `FunctionalInterface`. W tym przypadku możemy skrócić
zapis `ad-hoc` do, używająć `lambda expressions`:

```java
HasColour yellowThing = () -> "yellow";

HasWeight heavyThing = () -> 1000;
```

---

Metody domyślne to metody w interfejsie posiadające implementację:

```java
interface HasWeight {
    
    int weightInKg();
    
    default int weightInPounds() {
        return weightInKg() * 2.2;
    }
    
}
``` 

---

Wszystkie pola w interejsie zachowują się tak, jakby posiadały modyfikatory `static`, `public` i `final`:

```java
interface Contants {
    long SPEED_OF_LIGHT = 299_792_458;
    long RADIUS_OF_EARTH = 6_371;
}

```

---
###### Klasy abstrakcyjne

Klasy abstrakcyjne to klasy, które posiadają modyfikator `abstract`. Mogą posiadać abstrakcyjne (niezdefiniowane) metody:

```java
public abstract class Person {
	
	private final String name;
	
	public Person(String name){
		this.name = name;
	}
	
	public String fullName() {
	    return firstName + " " + lastName;
	}

	public abstract void introduce();
	
}
```
---

```java
class Agent extends Person {

    public Agent(String firstName, String lastName) {
        super(firstName, lastName);
    }

    @Override
    public String introduction() {
        return "My name is " + firstName + ", " + fullName();
    }
}

new Agent("James", "Bond").introduce() //My name is Bond, James Bond
```
---

Podobnie jak interfejsy, możemy instancje klasy anonimowej *ad-hoc*:

```java
 Person regularGuy = new Person("Andrzej", "Nowak") {
    @Override
    public String introduction() {
        return "Hi, I'm " + fullName();
    }
}

```

---


###### Interfejs vs klasa abstrakcyjna

* Klasa może dziedziczyć wiele interfejsów, ale tylko jedną klasę abstrakcyjną
* Interfejs nie może mieć pól ani konstruktorów
* Domyślnie wszystkie metody interfejsu są publiczne.

---

###### Kompozycja i delegacja

Kompozycja oznacza, że na funkcjonalność naszej finałowej klasy będą składać się funkcjonalności obiektów, które do niej należą.
Delegacja oznacza, że metoda klasy zewnętrznej odnosi się do metody obiektu wewnątrz.

Kompozycja powinna być preferowana nad dziedziczeniem.

```java
class Car extends Automobile {
  private Engine engine;
  private Horn horn;
  
  void sound() {
      horn.sound();
  }
  
  void start() {
      engine.start();
  }
  
}
```

---

###### Mutowalność obiektu

Mutowalny obiekt to taki, w którym możemy uaktualniać pola:

```java
class Animal {
    private String name;

    public Animal(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---

Niemutowalny obiekt, to taki, którego stanu nie można zmienić. Zmiany stanu dokonuje się
przez wykonanie kopii:

```java
class Animal {
    private final String name;

    public Animal(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
```

Przykład z biblioteki standardowej to `String`, `LocalDate` itd.

---

### Wyjątki

---

Wyjątki służą do zgłaszania nieprawidłowości w pracy programu. Brak obsłużenia wyjątku, który 
wystąpił, doprowadzi do zakończenia wątku, w ramach którego się pojawił. 
W najprostszej sytuacji spowoduje to wyłączenie programu.

```java
Integer safeParse(String maybeNumber) {
    try {
        return Integer.parseInt(maybeNumber);
    } catch (NumberFormatException e) {
        return 0;
    }
}

safeParse("100"); //100
safeParse("STO"); //0
```
---

```java
try {
   somethingUnsafe()
} catch (IllegalArgumentException e) {
    System.out.println("Error: " + e);
} catch (FileNotFoundException | IndexOutOfBoundsException e) {
    System.out.println("Error: " + e);
} finally {
    System.out.println("Always displayed");
}
```

---

##### Rodzaje wyjątków

![Exceptions](/assets/exception-hierarchy.png)

---
Wyjątki typu `CheckedException` musimy obsłużyć albo zadeklarować jako rzucane go w sygnaturze metody za pomocą `throws`:

```java
class Main { 
    public static void main(String[] args) throws InterruptedException 
    { 
        Thread.sleep(10000); 
        System.out.println("Hello Geeks"); 
    } 
} 
```

---

Wyjątki zgłaszamy za pomocą `throw`:

```
if(name.size() <= 3) {
   throw new IllegalArgumentException("Nazwa musi być dłuższa niż 3.")
}

```

---

### Varargs

---

*Varags* to mechanizm pozwalający na tworzenie metod o zmiennej ilości argumentów:

```java
public int sum(int... args) {

	int result = 0;

	for(int i=0; i<args.length; i++) {
		result += args[i];
	}
	return result;
}

sum(1); //1;
sum(1,2); //3
sum(1,2,3); //6
sum(new int[]{1,2,3});
```

---

### Enumy

---

Enum to inaczej *typ wyliczeniowy*. Pozwala nam na stworzenie kolekcji stałych:

```java
public enum Colour{
    BLACK, RED, BLUE
}
```

```java
Colour color = Colour.BLUE;

switch(color) {
    case BLACK:
        System.out.println("Czarny kolor");
        break;
    case RED:
        System.out.println("Czerwony kolor");
        break;
    case BLUE:
        System.out.println("Niebieski kolor");
        break;
}

```
---

```java
public enum ShirtSize {
    S(48),
    M(52),
    L(56),
    XL(61);
 
    private int chestWidth;
 
    ShirtSize(int chestWidth) {
        this.chestWidth = chestWidth;
    }   
 
    public int getChestWidth() {
        return chestWidth;
    }
}

ShirtSize.XL.getChestWidth(); //61
```

---


### Wyrażenia regularne


---

**RegEx** czyli wyrażenia regularne to potężne narzędzie pozwalające na zaawansowaną pracę z tekstem:

* Sprawdzenie czy łańcuch spełnia dane warunki
* Znajdowanie i wyłuskiwanie tekstu

---

Najprostsze wyrażenie regularne to po prostu tekst bez znaków specjalnych:

```
abcde
```

 spasuje: 

```
abcde
```

---

#### Kwantyfikatory

---

|Kwantyfikator|Znacze­nie|Przykład|Przykład dopasowuje|
|--- |--- |--- |--- |
|*|Zero lub więcej wystąpień|**a*b**|ab, b, aab, aaaaaab, aaab (i podobne)|
|+|Jedno lub więcej wystąpień|**a+b**|ab, aab, aaaaaaab, aab (i podobne)|
|?|Zero lub jedno wystąpienie|**a?b**|ab, b|

---

|Kwantyfikator|Znacze­nie|Przykład|Przykład dopasowuje|
|--- |--- |--- |--- |
|{n,m}|Co najmniej **n** i maksymalnie **m** wystąpień|**a{1,4}b**|ab, aab, aaab, aaaab|
|{n,}|Co najmniej **n** wystąpień|**a{3,}b**|aaab, aaaab aaaaab (i podobne)|
|{,n}|Maksymalnie **n** wystąpień|**a{,3}b**|b, ab, aab, aaab|
|{n}|Dokładnie **n** wystąpień|**a{3}b**|aaab|

---

#### Grupy

---

|Wyrażebnie|Opis|
|--- |--- |
|[abcde]|Jedna z liter: a, b, c, d lub e|
|[a-zA-Z]|Jedna z liter od a do Z mała lub duża|
|[a-c3-5]|Litera od a do c lub cyfra od 3 do 5|
|[a-c14-7]|Litera od a do c lub cyfra 1 lub cyfra od 4 do 7|
|.|Dowolny znak|

---

Grupy tworzymy za pomocą nawiasów `(` i `)`.

Znaki specjalne musimy poprzedzić znakiem ukośnika `\`.

Znak `^` oznacza początek łańcucha, a `$` koniec.

---

Przykłady:

```
.*
a(b(cd)?)+
a(bcd)*
[a-zA-Z]+
^[a-z0-9_-]{3,16}$

```

---


```java
Pattern pattern = Pattern.compile("regex");
Matcher matcher = pattern.matcher("ciąg do sprawdzenia");
matcher.matches(); //zwraca true lub false

```
```java
"str".matches("[a-z]+")

```

---

<span style="display:flex; align-items: center;">![Mail](assets/mail.png)&nbsp;&nbsp;&nbsp;[krzysztof.atlasik@pm.me](mailto:krzysztof.atlasik@pm.me)</span>
