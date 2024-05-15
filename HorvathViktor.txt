#Ez a program egy egyszerű konzolos felületet biztosít a felhasználó számára a foglalások kezelésére.
#A Szálloda, Szoba és Foglalas osztályok segítségével megvalósítja a szállodai foglalási rendszert, és az adatokat megfelelően validálja.
#A program kéri a felhasználótól a foglalás vagy lemondás dátumát és a szoba számát, majd végrehajtja a megfelelő műveletet.


from abc import ABC, abstractmethod
from datetime import datetime

class Szoba(ABC):
    def __init__(self, szobaszam, ar):
        self.szobaszam = szobaszam
        self.ar = ar

    @abstractmethod
    def tipus(self):
        pass

class EgyagyasSzoba(Szoba):
    def tipus(self):
        return "Egyágyas"

class KetagyasSzoba(Szoba):
    def tipus(self):
        return "Kétágyas"

class Szalloda:
    def __init__(self, nev):
        self.nev = nev
        self.szobak = []

    def add_szoba(self, szoba):
        self.szobak.append(szoba)

    def list_szobak(self):
        for szoba in self.szobak:
            print(f"Szoba: {szoba.szobaszam}, Ár: {szoba.ar}, Típus: {szoba.tipus()}")

class Foglalas:
    def __init__(self, szoba, datum):
        self.szoba = szoba
        self.datum = datum

def main():
    # Szálloda, szobák és foglalások inicializálása
    szalloda = Szalloda("Példa Szálloda")
    szalloda.add_szoba(EgyagyasSzoba("101", 5000))
    szalloda.add_szoba(EgyagyasSzoba("102", 6000))
    szalloda.add_szoba(KetagyasSzoba("201", 8000))
    foglalasok = [
        Foglalas(szalloda.szobak[0], datetime(2024, 5, 15)),
        Foglalas(szalloda.szobak[1], datetime(2024, 5, 17)),
        Foglalas(szalloda.szobak[2], datetime(2024, 5, 20)),
        Foglalas(szalloda.szobak[0], datetime(2024, 5, 25)),
        Foglalas(szalloda.szobak[2], datetime(2024, 5, 30))
    ]

    # Felhasználói interfész
    while True:
        print("\nVálassz műveletet:")
        print("1. Foglalás")
        print("2. Lemondás")
        print("3. Foglalások listázása")
        print("4. Kilépés")
        valasz = input("Művelet kiválasztása: ")

        if valasz == "1":
            datum = input("Add meg a foglalás dátumát (éééé-hh-nn): ")
            szobaszam = input("Add meg a szoba számát: ")

            # Adatok ellenőrzése és foglalás végrehajtása
            try:
                datum_obj = datetime.strptime(datum, "%Y-%m-%d")
                szoba = next((szoba for szoba in szalloda.szobak if szoba.szobaszam == szobaszam), None)
                if not szoba:
                    print("Hiba: A megadott szobaszám nem létezik!")
                    continue
                foglalva = False
                for foglalas in foglalasok:
                    if foglalas.szoba == szoba and foglalas.datum == datum_obj:
                        foglalva = True
                        break
                if foglalva:
                    print("Hiba: A szoba már foglalt ezen a dátumon!")
                    continue
                foglalasok.append(Foglalas(szoba, datum_obj))
                print("Sikeres foglalás!")
            except ValueError:
                print("Hiba: Hibás dátum formátum!")
        elif valasz == "2":
            datum = input("Add meg a lemondás dátumát (éééé-hh-nn): ")
            szobaszam = input("Add meg a szoba számát: ")

            # Lemondás végrehajtása
            try:
                datum_obj = datetime.strptime(datum, "%Y-%m-%d")
                szoba = next((szoba for szoba in szalloda.szobak if szoba.szobaszam == szobaszam), None)
                if not szoba:
                    print("Hiba: A megadott szobaszám nem létezik!")
                    continue
                foglalas = next((foglalas for foglalas in foglalasok if foglalas.szoba == szoba and foglalas.datum == datum_obj), None)
                if not foglalas:
                    print("Hiba: A megadott foglalás nem létezik!")
                    continue
                foglalasok.remove(foglalas)
                print("Sikeres lemondás!")
            except ValueError:
                print("Hiba: Hibás dátum formátum!")
        elif valasz == "3":
            print("\nFoglalások:")
            for foglalas in foglalasok:
                print(f"Szoba: {foglalas.szoba.szobaszam}, Dátum: {foglalas.datum}")
        elif valasz == "4":
            break
        else:
            print("Hibás válasz! Kérem, válasszon a megadott lehetőségek közül.")

if __name__ == "__main__":
    main()