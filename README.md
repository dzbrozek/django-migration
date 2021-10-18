### DANE DO ZADAŃ:

Dostępne są modele:

```python
from django.db import models


class Subscriber(models.Model):
    create_date = models.DateTimeField()
    email = models.EmailField(max_length=255, unique=True)
    gdpr_consent = models.BooleanField(default=False)

    def __str__(self):
        return f'Subscriber: {self.pk}'


class SubscriberSMS(models.Model):
    create_date = models.DateTimeField()
    phone = models.CharField(max_length=50, unique=True)
    gdpr_consent = models.BooleanField(default=False)

    def __str__(self):
        return f'Subscriber SMS: {self.pk}'


class Client(models.Model):
    create_date = models.DateTimeField()
    email = models.EmailField(max_length=255, unique=True)
    phone = models.CharField(max_length=50)

    def __str__(self):
        return f'Client: {self.pk}'


class User(models.Model):
    create_date = models.DateTimeField()
    email = models.EmailField(max_length=255)
    phone = models.CharField(max_length=50)
    gdpr_consent = models.BooleanField(default=False)

    def __str__(self):
        return f'User: {self.pk}'
 ```

Pola phone i email są znormalizowane, więc są porównywalne z wartościami z innych rekordów.
Skrypty utworzone w ramach poniższych zadań powinny być zgodne z Django i wykonane w formie migracji lub poleceń wykonywanych przy pomocy manage.py.
Oceniane będą:

* czytelność kodu
* optymalizacja pod kątem liczby zapytań do bazy danych


## Zadanie 1. 

Przygotuj skrypt migrujący rekordy z modeli Subscriber i SubscriberSMS do User przy założeniach:
* dla modelu Subscriber
  * jeśli istnieje User z polem email takim samym jak w Subscriber - pomiń subskrybenta i nie twórz nowego użytkownika
  * jeśli nie istnieje ww. User
    * jeśli istnieje Client z polem email takim jak Subscriber.email i nie istnieje User z polem phone takim jak Client.phone i polem email różnym od Client.email stwórz użytkownika na podstawie modelu Client
    * jeśli istnieje Client z polem email takim jak Subscriber.email i istnieje User z polem phone takim jak Client.phone i polem email różnym od Client.email zapisz id i email subskrybenta do pliku subscriber_conflicts.csv
    * jeśli nie istnieje Client z polem email takim jak Subscriber.email, stwórz użytkownika z pustym polem phone
* dla modelu SubscriberSMS
   * kroki analogiczne do Subscriber, tylko z odwróconą logiką pól email i phone, i osobnym plikiem csv na konflikty

Nie twórz użytkowników na podstawie klientów z nieunikalnymi numerami telefonów - informację o takich klientach również zapisz do pliku .csv.
Zadbaj o przeniesienie pola gdpr_consent do nowych użytkowników.

## Zadanie 2 (bonusowe)

Przygotuj skrypt migrujący, który dla użytkowników, którzy mają swoich odpowiedników w subskrybentach (subskrybenci pominięci w zadaniu 1) przenosi wartość pola Subscriber.gdpr_consent na odpowiednik w modelu User, ale tylko, jeśli data utworzenia subskrybenta jest nowsza niż data utworzenia użytkownika. Jeśli użytkownik powstał z połączenia Subscriber i SubscriberSMS (istniał Client o danym telefonie i adresie email) o wartości gdpr_consent decyduje obiekt o najnowszej dacie utworzenia.
