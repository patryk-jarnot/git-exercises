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

Przy wykonaniu polecenia `git log` zwróć uwagę na dodane etykiety (xy_print_foo, master, v1.0.0).

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
g++ -o hello main.cpp foo.cpp
./hello
```

Przejdź na główną gałąź projektu (w zależności od wersji może ona nazywać się również "master"):

```bash
git checkout main
```

Przyłącz gałąź `xy_print_foo` do głównej gałęzi projektu:

```bash
git merge xy_print_foo
```

Prześledź historię commit-ów:

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
g++ -o hello main.cpp bar.cpp
./hello
```

Przejdź na główną gałąź projektu:

```bash
git checkout main
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
 * Prints "foo" with size of short in bytes in your architecture.
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

Przejdź na branch `main` (albo `master` jeśli tak nazywa się u Ciebie główny branch):

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
git diff
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

Będąc na branch-u `xy_bar_long_size` dodaj do pliku `.gitignore` katalog `data/` otrzymując następującą zawartość:

```bash
hello
data/
```

Utwórz plik `input.json` w katalogu `data/`, a następnie zrób nowy commmit.

Załóżmy teraz, że na branch-u `xy_foo_short_size` również chcielibyśmy mieć taką zmianę.
Przechodzimy w tym celu ten branch:

```bash
git checkout xy_foo_short_size
```

Następnie kopiujemy na aktualny branch pierwszy commit z branch-a `xy_bar_long_size`:

```bash
git cherry-pick xy_bar_long_size
```

Pytanie: Jak pobrać dwa ostatnie commit-y z innego branch-a? Jak pobrać drugi i trzeci od końca commit z innego branch-a?

### Selekcja commit-ów

Wiele komend git-a przyjmuje jako parametr commit-y na których one operują, np: checkout, revert, rebase. Do wyboru commit-a możemy skorzystać z nazwy branch-a, wskaźnika na aktualny commit, jego SHA-1 itd. Dodatkowo można się odwoływać do commit-ów w sposób relatywny. Służą do tego modyfikatory `^` oraz `~`:

* ^
    Pozwala na wybranie kolejnego commit-a rodzica jeśli jest ich więcej.
* ~
    Modyfikator ten pozwala poruszać się w głąb historii.

### Show

Polecenie `git show` służy do wyświetlania szczegółowych informacji o commit-ach.
Możemy jako parametr podać dowolny commit z naszego repozytorium.

### Rebase

Alternatywą do merge-owania jest wykonanie rebase-ów.
Z jednej strony pozwala to na przechowywanie liniowej historii, która jest prostrza do czytania.
Z drugiej strony nie przechowuje informacji o tworzeniu branch-y i wymaga modyfikowania histoii, co może powodować problemy.

Aby wcielić branch-e `xy_foo_short_size` oraz `xy_bar_long_size` do branch-a `master`, należy wykonać następującą sekwencje poleceń:

```bash
git checkout master
git rebase xy_bar_long_size
git checkout xy_foo_short_size
git rebase master
git checkout master
git checkout xy_foo_short_size
```

Jest trochę więcej roboty, ale też historia jest trochę prostrza.

```bash
git log --oneline --graph --all
```

`rebase` jest w rzeczywistości bardzo złożoną komendą, która ma o wiele większe możliwości.
Możemy wykonać ją w trybie interaktywnym modyfikując na różny sposób trzy ostatnie commity.

```bash
git rebase -i HEAD~3
```

### Amend

```bash
git commit --amend
```

```bash
git add .
git commit --amend
```

### Reset

```bash
git log --graph --oneline --all
git reset --soft HEAD~
git log --graph --oneline --all
```

```bash
git reset --mixed HEAD~
git log --graph --oneline --all
```

```bash
git reset --hard HEAD~
git log --graph --oneline --all
```

### Revert

```bash
git revert HEAD~
```

### Stash

Dodaj pare debug printów do `main.cpp`.

```bash
git status
```

Widzimy, że mamy jakieś zmiany w pliku `main.cpp`.
Stwierdzamy, że to są bardzo przydatne printy, które możemy jeszcze w przyszłości wykorzystać.
Zachowajmy zatem te zmiany wykorzystując `stash`.

```bash
git stash push -m "Debug prints"
```

```bash
git stash list
```

```bash
git stash apply stash@{0}
```

### Blame

```bash
git blame main.cpp
```

### Bisect

Wklej następującą zawartość do pliku `script.sh`:

```bash
#!/bin/bash

grep "foo();" main.cpp && exit 1
exit 0
```

Aby znaleźć rewizję, w której dodano wywołanie funkcji `foo` w pliku `main.cpp`, należy wykonać następującą sekwencje poleceń:

```
git bisect start
git bisect bad
git bisect good 662fcbcf7806bb7bc340d45db2b50ce5bb62f586
git bisect run ./script.sh
```

### Przeszukiwanie repozytorium

Git daje możliwość przeszukiwania histoii zmian po łańcuchu znaków.

Aby znaleźć wszystkie commit-y zawierające daną zmianę, należy skorzystać z polecenia:

```
git log -S [string]
```

Można też wychorzystać wyrażenia regularne.
Wtedy ustawiamy przełącznik `-G`.

```
git log -G [pattern]
```

### Clean untracked files

Zdarza się, że budując projekt, albo generując inne pliki wybierzemy zły katalog.
Może to doprowadzić do sytuacji w której mamy sporo plików porozrzucanych po projekcie, które musimy usunąć.
Możemy tego dokonać ręcznie, albo wykorzystać git-a, aby zrobił to za nas.
Pomocne do tego będą następujące komendy:

* Sprawdź, które pliki zostaną usunięte:
    ```
    git clean -dn
    ```

* Usuń wskazane pliki:
    ```
    git clean -df
    ```

### Git-internals

Zrozum do czego służą poszczególne komponenty repozytorium.

```bash
ls .git
```

Rozpakowanie zawartości pliku obiektowego.

```bash
git cat-file -p [SHA-1]
```

Typ pliku można poznać poleceniem:

```bash
git cat-file -t [SHA-1]
```

