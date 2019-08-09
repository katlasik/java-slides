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
   Fruit() {
      this("kiwi");
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
       return "Very tasty " +  super.makeJuice();
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
Fruit appleFruit = new Apple("A,B,C");

appleFruit.makeJuice(); // Very tasty apple juice
appleFruit.makeApplePie(); //błąd kompilacji

Object appleObject = new Apple("A");

```

Na szczycie hierarchii dziedziczenia *Javy* jest klasa `Object` wszystkie klasy niejawnie są jej dziećmi)

---

###### Konstruktory

* Konstruktory nie podlegają dziedziczeniu.
* Klasa dziedzicząca musi wywołąć za pomocą `super` przynajmniej jeden
 z konstruktorów rodzica.

```
class Animal {
    private String kind;

    Animal(String kind) {
        this.kind = kind;
    }
}

class Dog extends Animal {
    Dog() {
        super("Pies");
    }
}
```


---

#### Modifikatory dostępu

|Modyfikator|Klasa|Pakiet|Podklasa|Poza|Poprawny dla klas|
|--- |--- |--- |--- |--- |--- |
|public|**tak**|**tak**|**tak**|**tak**|**tak**|
|protected|**tak**|**tak**|**tak**|nie|nie|
|brak|**tak**|**tak**|nie|nie|**tak**|
|private|**tak**|nie|nie|nie|nie|

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
interface HasColor {
    String Color();
}

interface HasQuality {
    String quality();
}

public class GreenApple implements HasQuality, HasColor {
   @Override
   public String quality() {
       return "great";
   }
   @Override
   public String Color() {
      return "green";
  }
}
```
---

Interfejs może zostać użyty jako typ referencji. Tylko publiczne metody danego interfejsu będą dostępne: 

```java
HasColor apple = new GreenApple();

apple.Color(); //Zwróci "green"

apple.quality(); //Spowoduje błąd kompilacji
```

---

Możemy również tworzyć obiekty implementujące interfejs bez tworzenia klasy. Są to tak zwane implementacje **ad-hoc**:

```java
HasColor yellowThing = new HasColor() {

    @Override
    public String Color() {
        return "yellow";
    }
    
};

```

---
Jeżeli interfejs posiada tylko jedną metodę, to jest to tak zwany `FunctionalInterface`. W tym przypadku możemy skrócić
zapis `ad-hoc` do, używająć `lambda expressions`:

```java
HasColor yellowThing = () -> "yellow";

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
	
	private final String firstName;
	private final String lastName;
	
	public Person(String firstName, String lastName){
		this.firstName = firstName;
		this.lastName = lastName;
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


###### Interfejs kontra klasa abstrakcyjna

* Klasa może dziedziczyć wiele interfejsów, ale tylko jedną klasę abstrakcyjną
* Interfejs nie może mieć pól ani konstruktorów
* Domyślnie wszystkie metody interfejsu są publiczne.

---

###### Kompozycja kontra delegacja

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

Aby stworzyć nową klasę wyjątków musimy stworzyć nową klasę dziedziczącą po
`Exception` albo `RuntimeException`:

```java
class MyException extends RuntimeException {
  public MyException() {
  }

  public MyException(String message) {
    super(message);
  }

  public MyException(String message, Throwable cause) {
    super(message, cause);
  }

  public MyException(Throwable cause) {
    super(cause);
  }
}
```

---

### Wyjątki z biblioteki standardowej

**NullPointerException** jest rzucany gdy próbujemy odwołać sie do pola lub metody referencji, 
która jest *nullem*. Jest to *RuntimeException*.

```java
String name = null
name.toUpperCase();
```

**ArithmeticException** jest rzucany gdy próbujemy wykonać niepoprawną operację arytmetyczną. Jest to *RuntimeException*.

```java
1 / 0
```
---
### Generatory losowych wartości

Klasa `java.util.Random` pozwala nam wygenererować losowe wartości:

```java
Random random = new Random(seed);
random.nextInt();
random.nextInt(50); //liczby do 50 włącznie
random.nextBoolean();
random.nextDouble();

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

### Typy wyliczeniowe

---

Enum to inaczej *typ wyliczeniowy*. Pozwala nam na stworzenie kolekcji stałych:

```java
public enum Color{
    BLACK, RED, BLUE
}
```

```java
Color color = Color.BLUE;

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

###### Przydatne metody

Metoda `ordinal` zwraca indeks enumeracji.

```java
Color.BLUE.ordinal(); //2
```

Metoda `valueOf` pozwala zwrócić enumację ze stringa

```java
Color.valueOf("BLUE");
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
|\d|To samo co `[0-9]`|
|\w|To samo co `[a-zA-Z_]`|
|\s|To samo co `[ \t\r\n\f]`|
---

Grupy tworzymy za pomocą nawiasów `(` i `)`.

Znaki specjalne musimy poprzedzić znakiem ukośnika `\`.

Znak `^` oznacza początek łańcucha, a `$` koniec.

Możemy zanegować wyrażenie za pomocą `^`.

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
"str".matches("[a-z]+") //true
"123 456 543".replaceAll("\\d{3}", "X) //X X X
"123\t456    543 999".split("\\w+") //["123","456","543","999"]
```


```java
Pattern pattern = Pattern.compile("regex");
Matcher matcher = pattern.matcher("ciąg do sprawdzenia");
matcher.matches(); //zwraca true lub false
```

---

Grupy w wyrażeniach regularnych możemy wykorzystywać także do znajdowania łancuchów w tekście:

```java
String line = "555-123-1235";
String pattern = "\d{3}-(\d{3})-(\d{4})";

Pattern r = Pattern.compile(pattern);
Matcher m = r.matcher(line);

if (m.find( )) {
  System.out.println(m.group(0)); //555-123-1235
  System.out.println(m.group(0)); //555-123-1235
  System.out.println(m.group(1)); //123
  System.out.println(m.group(2)); //1235
} else {
  System.out.println("Nic nie znalazłem!");
}
```

---

##### Klasy i metody generyczne
Typy generyczne pozwalają nam parametryzować typy klas i metod.

---

```java
public class Apple {
}

```

```java

public class AppleBox {
    private Apple apple;

    public AppleBox(Apple apple) {
        this.apple = apple;
    }

    public Apple getApple() {
        return apple;
    }
}
```
---
```java
public class Car {
}
```

```java
public class CarBox {
    private Car car;

    public CarBox(Car car) {
        this.car = car;
    }

    public Car getCar() {
        return car;
    }
}
```
---

##### Obliczenia rówloległe, a wielowątkowowść

![Concurrency vs Parallelism](assets/convspar.png)

**Obliczenia równoległe** - forma wykonywania obliczeń, w której wiele instrukcji jest wykonywanych jednocześnie.
 
**Wielowątkowość** to podzielenie programu na niezależne od siebie zadania - wątki. 

---

<span style="display:flex; align-items: center;">![Mail](assets/mail.png)&nbsp;&nbsp;&nbsp;[krzysztof.atlasik@pm.me](mailto:krzysztof.atlasik@pm.me)</span>
