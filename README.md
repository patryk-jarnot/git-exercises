# Ćwiczenia

## Przygotowanie

Do wykonania ćwieczeń konieczne jest zainstalowanie git-a. Większość ćwiczeń będzie wykonywane w trybie tekstowym.

## Tworzenie repozytorium

Repozytorium możemy stworzyć na pare różnych sposobów. M. in. możemy sklonować istniejące, lub stworzyć nowe od początku.

Może ono zostać sklonowane przy pomocy polecenia `git clone [path]`. Przykładowo:

```bash
git clone https://github.com/patryk-jarnot/git-fizzbuzz.git
ls git-fizzbuzz
```

Możemy też zainicjować nowe repozytorium w pustym katalogu:

```bash
mkdir myproject
cd myproject
git init
```

## Zapisywanie zmian

Stwórzmy w naszym repozytorium nowy plik `main.cpp` z przykładowym kodem:

```cpp
#include <iostream>

using namespace std;

int main() {
    cout << "Hello world!\n";
    return 0;
}
```

Chcemy teraz zapisać wprowadzone zmiany w repozytorium. W tym celu wykonujemy następującą sekwencje poleceń:

```bash
git status
git add main.cpp
git status
git commit
git status
git log
```

Polecenie `git status` pokaże nam wszystkie zmiany wprowadzone do naszego projektu, których nie ma w repozytorium.
`git add` dodaje plik do tzw. "stage area", które jest przestrzenią do modelowania zmian, które chcemy dodać do repozytorium.
Drugie wywołanie `git status` pokazuje, że zmiany w pliku `main.cpp` są dodane właśnie do stage area. 
Polecenie `git commit` wprowadza wszystkie zmiany wprowadzone do stage area.
Ważne jest, że umożliwia nam wprowadzenie komentarza do zmian (commit message).
Komentarz powinien składać się z lini tytułowej opisującej wprowadzane zmiany w 50-ciu znakach, następnie powinna być pusta linia, a po niej informacja o tym co zostało zrobione i dlaczego.
W komentarzu nie zawieramy informacji o tym jak rozwiązaliśmy dany problem, ponieważ to można wywnioskować z kodu źródłowego.
Trzecie wywołanie `git status` pokazuje, że nie ma zmian w projekcie w stosunku do stanu repozytorium.
`git log` zwraca informacje o utworzonym kommicie.

## Ignorowanie zmian

Często projekt zawiera automatycznie generowane pliki, których nie chcemy wrzucać do repozytorium, bo np. musielibyśmy wrzucać te zmiany za każdym razem kiedy kompilujemy projekt.

Wykonaj następującą sekwencje poleceń:

```
g++ -o hello main.cpp
git status
```

Polecenie `git status` pokazuje, że wprowadzono nowe zmiany do pliku `hello`.
Chcielibyśmy, żeby git pomijał zawsze tę informację.
W tym celu dodaj następującą zawartość do pliku `.gitignore`:

```
hello
```

Wykonaj teraz polecenie `git status` i przekonaj się, że git ignoruje już plik `hello`.
Widzi natomiast zmiany wprowadzone do pliku `.gitignore`.
Dodaj je do repozytorium, aby inni programiści również nie mieli problemu z tym, automatycznie generowanym plikiem.
Wykonaj polecenia:

```bash
git add .
git status
git commit
git status
git log -p
```

Co daje dodanie przełącznika `-p` do polecenia `git log`?

Nasze projekt kompiluje się i działa, możemy oznaczyć aktualny stan jako pierwsza wersja projektu.
W tym celu wykonaj następujące polecenie:

```bash
git tag v1.0.0
```

## Praca na gałęziach (branch-ach)

### Przypadek podstawowy

Do tej pory pracowaliśmy na głównej historii repozytorium projektu.
Zgodnie ze zwinnymi metodykami tworzenia oprogramowania powinna ona zawsze zawierać ostatnią działającą wersję repozytorium.
Z drugiej strony, do repozytorium powinniśmy dodawać drobne zmiany, żeby móc nimi wygodnie zarządzać.
Rozwiązaniem tego problemu jest tworzenie branch-y, czyli odgałęzień od głównej wersji projektu na czas pracy nad funkcjonalnością.
Po zakończeniu i przetestowaniu rozwijanej funkcjonalności można zmiany z takiej gałęzi przyłączyć do głównej wersji projektu.

Utwórz nową gałąź:

```bash
git checkout -b xy_print_foo
```

Nazwa gałęzi powinna składać się z Twoich inicjałów i krótkiego opisu funkcjonalności nad którą pracujesz.

Następnie dodaj plik `foo.hpp` z następującą zawartością:

```cpp
void foo();
```

Zatwierdź zmiany w repozytorium.

```bash
git status
git add .
git status
git commit
git status
git log
```

Dodaj plik `foo.cpp` z następującą zawartością:

```cpp
#include "foo.hpp"
#include <iostream>

using namespace std;

void foo() {
    cout << "foo\n";
}
```

Zatwierdź zmiany w repozytorium.

```bash
git status
git add .
git status
git commit
git status
git log
```

Zmodyfikuj plik `main.cpp` tak, aby wywoływał funkcję foo:

```cpp
#include "foo.hpp"
#include <iostream>

using namespace std;

int main() {
    foo();
    cout << "Hello world!\n";
    return 0;
}
```

Zatwierdź zmiany w repozytorium.

```bash
git status
git add .
git status
git commit
git status
git log
```

Skompiluj projekt i sprawdź czy działa:

```
g++ -o hello main.cpp
./hello
```

Przejdź na główną gałąź projektu:

```bash
git checkokut main
```

Przyłącz gałąź `xy_print_foo` do głównej gałęzi projektu:

```bash
git merge xy_print_foo
```

Prześledź historię kommitów:

```bash
git log --oneline --graph --all
```

Zauważ, że przy każdym commicie wypisywana jest tylko linia tytułowa komentarza.
To właśnie dlatego powinna ona być krótka.


### Konflikt

Korzystając z polecenia `git checkout` możemy przemieścić się w dowolne miejsce w historii.

Przejdź do `v1.0.0` wersji projektu, wylistuj wszystkie pliki i sprawdź zawartość pliku `main.cpp`.

```bash
git checkout v1.0.0
ls
cat main.cpp
```

Następnie postępuj podobnie jak przy tworzeniu funkcjonalności `foo`, żeby dodać do projektu funkcjonalność `bar`.

Utwórz nową gałąź:

```bash
git checkout -b xy_print_bar
```

Następnie dodaj plik `bar.hpp` z następującą zawartością:

```cpp
void bar();
```

Zatwierdź zmiany w repozytorium.

```bash
git status
git add .
git status
git commit
git status
git log
```

Dodaj plik `bar.cpp` z następującą zawartością:

```cpp
#include "bar.hpp"
#include <iostream>

using namespace std;

void bar() {
    cout << "bar\n";
}
```

Zatwierdź zmiany w repozytorium.

```bash
git status
git add .
git status
git commit
git status
git log
```

Zmodyfikuj plik `main.cpp` tak, aby wywoływał funkcję bar:

```cpp
#include "bar.hpp"
#include <iostream>

using namespace std;

int main() {
    bar();
    cout << "Hello world!\n";
    return 0;
}
```

Zatwierdź zmiany w repozytorium.

```bash
git status
git add .
git status
git commit
git status
git log
```

Skompiluj projekt i sprawdź czy działa:

```
g++ -o hello main.cpp
./hello
```

Przejdź na główną gałąź projektu:

```bash
git checkokut main
```

Przyłącz gałąź `xy_print_bar` do głównej gałęzi projektu:

```bash
git merge xy_print_bar
```

Zauważ, że git przeszedł w stan `merge`.
Wykonaj polecenie `git status` i rozeznaj się co się stało i jakie masz możliwości.
Zmodyfikuj plik `main.cpp` tak, aby rozwiązać konflikt.
Zakommituj zmiany:

```bash
git status
git add .
git status
git commit
git status
git log
```

Prześledź drzewko kommitów:

```bash
git log --oneline --graph --all
```

## Operacje na historii

### Przygotowanie repozytorium

Utwórz branch `xy_foo_short_size`:

```bash
git checkout -b xy_foo_short_size
```

Dodaj komentarz do pliku `foo.hpp`:

```cpp
/**
 * Prints "foo" with short size in bytes in your architecture.
 */
void foo();
```

Dodaj zmiany do repozytorium:

```bash
git status
git add .
git status
git commit
git status
git log
```

Wypisz w funkcji `foo()` rozmiar typu `short` modyfikując plik `foo.cpp`.

```cpp
#include "foo.hpp"
#include <iostream>

using namespace std;

void foo() {
    cout << "foo\n";
    cout << "Size of long in your architecture is " << sizeof(short) << "\n";
}
```

Dodaj zmiany do repozytorium:

```bash
git status
git add .
git status
git commit
git status
git log
```

Następnie wykonaj powyższe czynności dla "bar".

Przejdź na branch `main`

```bash
git checkout main
```

Utwórz branch `xy_bar_long_size`:

```bash
git checkout -b xy_bar_long_size
```

Dodaj komentarz do pliku `bar.hpp`:

```cpp
/**
 * Prints "bar" with long size in bytes in your architecture.
 */
void bar();
```

Dodaj zmiany do repozytorium:

```bash
git status
git add .
git status
git commit
git status
git log
```

Wypisz w funkcji `bar()` rozmiar typu `long` modyfikując plik `bar.cpp`.

```cpp
#include "bar.hpp"
#include <iostream>

using namespace std;

void bar() {
    cout << "bar\n";
    cout << "Size of long in your architecture is " << sizeof(long) << "\n";
}
```

Dodaj zmiany do repozytorium:

```bash
git status
git add .
git status
git commit
git status
git log
```

### Porównywanie zmian między branch-ami

Do porównania dwóch branch-y możemy skorzystać z polecenia `git diff`.
Aby zobaczyć jakie zmiany zostały wprowadzone do branch-a `xy_bar_long_size` względem branch-a `xy_foo_short_size`, wykonaj polecenie:

```bash
git diff xy_foo_short_size..xy_bar_long_size
```

Aby zobaczyć jakie zmiany zostały wprowadzone do branch-a `xy_bar_long_size` od momentu ostatniego wspólnego commit-a z branch-em `xy_foo_short_size` skorzystaj z trzech kropek `...`.

```bash
git diff xy_foo_short_size...xy_bar_long_size
```

Aby wylistować nowe commit-y wprowadzone do obu branch-y, wykonaj polecenie:

```bash
git log xy_foo_short_size...xy_bar_long_size
```

Aby wylistować nowe commit-y wprowadzone do branch-a `xy_bar_long_size` względem branch-a `xy_foo_short_size`, wykonaj polecenie (dwie kropki):

```bash
git log xy_foo_short_size..xy_bar_long_size
```

### Cherry-pick

