# Raport sql - wybory do sejmu 2023
## Przygotowanie danych
Dane zostały pobrane ze strony [wybory.gov.pl](https://wybory.gov.pl) z zakładki "Arkusze danych". Zostały pobrane 20 października 2023.
Analiza została przeprowadzona w SQL Management Studio 19.1.
## Dane ogólne
Wszystkich kandydatów: **6333**
```sql
SELECT COUNT([Nazwisko i imiona]) AS 'Liczba wszystkich kandydatów'  FROM [analiza_wybory].[dbo].[kandydaci_sejm_2023]
```
Najmniejsza liczba głosów uzyskanych przez pojedynczego kandydata: **9**.
```sql
SELECT MIN([Liczba głosów]) AS 'najmniejsza liczba głosów' FROM [analiza_wybory].[dbo].[kandydaci_sejm_2023]
```
Największa liczba głosów uzyskanych przez pojedynczego kandydata: **538634**.
```sql
SELECT MAX([Liczba głosów]) AS 'największa liczba głosów' FROM [analiza_wybory].[dbo].[kandydaci_sejm_2023]
```
## Oszukani przez metodę D’Hondta
Wybory wydają się proste - kandydaci z największą liczbą głosów przechodzą do sejmu... Jednak o tym, kto przechodzi dalej decyduje tzw. metoda D’Hondta, belgijskiego matematyka. Metoda liczenia głosów oparta jest o proporcjonalną reprezentację list partyjnych.
Poszukiwania takich "oszukanych" kandydatów, należy podzielić na dwa etapy:
1. Znaleźć kandydata z najmniejszą liczbą głosów, któremu przyznano mandat poselski (3482).
```sql
SELECT COUNT([Liczba głosów]) AS 'liczba oszukanych' FROM [analiza_wybory].[dbo].[kandydaci_sejm_2023] where [Czy przyznano mandat] = 'NIE' and [Liczba głosów]>=3482 ORDER BY [liczba oszukanych] DESC
```
2. Znaleźć i policzyć kandydatów, którzy mieli tyle samo lub więcej głosów co kandydat z punktu 1., ale nie otrzymali mandatu
```sql
SELECT COUNT([Liczba głosów]) AS 'liczba oszukanych' FROM [analiza_wybory].[dbo].[kandydaci_sejm_2023] where [Czy przyznano mandat] = 'NIE' ORDER BY [liczba oszukanych] DESC
```
O dziwo, takich "oszukanych" jest dość dużo, bo aż 589.
## Zawody
Wszystkich zawodów wskazanych jest **1317**.
Oczywiście trzeba brać pod uwagę, że niektóre podane zawody są sprecyzowane, niektóre bardzo ogólnie. Przypuszczalnie kandydaci oprócz wyboru konkretnych zawodów mogli je wpisywać ręcznie.  
Top 10 zawodów kandydatów pod względem popularności:

| Nazwa zawodu| Liczba przedstawicieli|
| --- | --- |
| przedsiębiorca | 442 |
| nauczyciel | 420 |
| ekonomista | 348 |
| prawnik | 168 |
| rolnik | 158 |
| politolog | 141 |
| pracownik biurowy | 117 |
| parlamentarzysta | 93 |
| emeryt | 86 |
| nauczyciel akademicki | 84 |

Top 10 zawodów kandydatów wybranych do sejmu:

| Nazwa zawodu| Liczba przedstawicieli|
| --- | --- |
| parlamentarzysta | 71 |
| ekonomista | 37 |
| prawnik | 37 |
| politolog | 27 |
| poseł na Sejm RP | 18 |
| nauczyciel akademicki | 16 |
| przedsiębiorca | 16 |
| nauczyciel | 14 |
| adwokat | 13 |
| historyk | 13 
## Aspekty techniczne
Analiza została przeprowadzona w SQL Management Studio 19.1.
Użycie opcji Tasks -> "Import flat file..." kończyło się błędem, dlatego użyto opcji Tasks -> "Import data..." z odpowiednimi typami danych i wyróżnikiem danych tekstowych (więcej szczegółów poniżej).  
Wszystkie czynności:
1. Zamiana kodowania pliku	 na ANSI -> w programie Notepad++ w menu Format / "convert to ANSI format"
2. Ustawienie kwalifikatora danych tekstowych (**Text qualifier**) -> plik csv zawierał dane tekstowe ujęte w cudzysłów, co  wymagało wskazania podczas importu
3. W celu zwiększenia czytelności, zmieniono nazwę tabeli na **kandydaci_sejm_2023**
