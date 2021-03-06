The documentation in html and rst still doesn't working. The authors of this module puts all efforts to complete this one.

**INTRODUCTION: (koniecznie to przeczytaj !!!!)**

we have few representations ways for nodes localisation

(lat, lon) for UM 'grid nodes' - actually, temporally in c_grid/5_pgrid

(row, col) for whole area (globalrowcol) - size (616, 448)

(row, col) only for Polands see below - size (165, 175) the most frequent presentation in these modules (namedtuplerowcol).

More details in *coords_manager.py* under Poland - bound variable namedtuple type


**DEMO EXAMPLE (koniecznie wypróbuj dla komfortu !!!!)**
W pliku demo.py prezentuję przykłady użycia funkcji. Jeżeli pobierzesz projekt i to Jeżeli chciesz użyć funkcji w innym skrypcie python
to musisz funkcje importować w taki sam sposób jak w demo.py i katalog również umiejscowić w odpowiednim miejscu w celu uniknięcia błędów importowania.


najczęstrza forma przedstawienia daty to YEAR, MONTH, DAY, HOUR tzn 4 zmienne typu integer

parametry pogodowe w formie "constant integer" reprezentują parametr pogodowy. Wyróżnione są najczęściej używane:
*code_imgw_air_temp = 29*

*code_imgw_ground_temp_5 = 77*

*code_imgw_ground_temp_10 = 79*

*code_imgw_rel_hum = 37*

Konkretna wartość dla parametru zależy od numeru kolumny w plikach, których dokładniejszy opis znajduje się tutaj:
https://dane.imgw.pl/data/dane_pomiarowo_obserwacyjne/dane_meteorologiczne/terminowe/synop/s_t_format.txt

**zawartość katalogów**

*dane imgw/2019*

pomiary ze stacji synoptycznych (jeżeli brakuje danych to można je dociągnąć z https://dane.imgw.pl/data/dane_pomiarowo_obserwacyjne/dane_meteorologiczne/terminowe/synop/)

*dane_imgw/'nazwapliku'.csv*

lokalizacje stacji synoptycznych 

*nuts*

pliki służące do wycinania maski polski i nie tylko! Jeżeli brakuje plików należy je ściągnąć stąd:

https://ec.europa.eu/eurostat/web/gisco/geodata/reference-data/administrative-units-statistical-units/nuts 

*c_grid, p_grid*

katalogi z plikami zawierającymi danymi dla różnych siatek dla różnych parametrów z różnych wersji UM. Pamiętaj sprawdzeniu czy prawidłowo ustawiłaś zmienne *grid_type = "c"* oraz
*comp_type = "pgrid"* w momencie jak zmieniasz parametr pogodowy lub pobierasz z dawniejszych danych niż 2019 r. W razie problemów pytaj Mateusza o pakiet UM_utils/coords bo od niego to przekopiowałem. Informacja powinna się chyba znaleźć u niego w UM_utils/fields.json. UM_utils albo MM_utils nie pamiętam...


**OPIS FUNKCJI: użytych w demo.py**

**---load and transform---**

*get_imgw.py:*
**load_imgw_single(int:YEAR, int:MONTH, int:DAY, int:HOUR, int:weather_feature)**

Załaduj 2-wymiarową mapę parametru pogodowego w reprezentacji (row, col) dla Polski.
Zostało to otrzymane na podstawie interpolacji danych ze stacji synoptycznych za pomocą interpolatora scipy.interpolate.Rbf (radial basis function)
Wartość zwracana to tablica dwuwymiarowa


*coords_manager.py*
**globalrowcol2namedtuplerowcol(tuple:(row, col), namedtuple:Poland)**

funkcja konwertuje reprezentację global (row, col) na reprezentację (row, col) dla Polski

*get_imgw.py:*
**load_sequence_map(datetime:start, param=code_imgw_air_temp, forecast_hour_len)**

funkcja ładująca sekwencję danych dla każdego węzła siatki w reprezentacji Poland (row, col)
start - *type datetime* - początek sekwencji
param - *type const int* - parametr pogodowy
forecast_hour_len - *type int* - długość sekwencji
funkcja zwraca tablicę 3-wymiarową.


*get_imgw.py*
**get_one_series(spacetime, node)**

funkcja wyciągająca z funkcji  *load_sequence_map* serię danych dla konkretnego węzła siatki w reprezentacji Poland (row, col)
*spacetime* - tablica 3-wymiarowa wygenerowana przez *load_sequence_map*
*node* - tuple reprezentujące węzeł siatki według numerowania Poland nametuple
funkcja zwraca tablicę danych




**---visualisation---**

*coords_manager.py*
**make_mask(my_bounds)**

funkcja tworząca maskę danych 
my_bounds - *type namedtuple bounds* - jakie dokładnie wycięcie potrzebuję zrobić - njczęściej będzie to Polska
ta funkcja używa danych z eurostatu plików tzw. "nuts" i używam biblioteki geopandas 


*plots.py*
**pickle2mask()**

Załaduj maskę z Poland_mask.pkl


*plots.py*
**visualise_rowcol_map(map, title, str:y_label_name, cmap:cm.brg, min, max)**

*map* - tablica 2-wymiarowa, którą wizualizujemy właśnie

*y_label_name* - etykieta i ops legendy

*cmap* - wybrana kolormapa

*min, max* - wartość minimalna i maksymalna parametru dla colormapy w wizualizacji - steruj tym parametrem głównie wtedy gdy zmieniasz parametr pogodowy

Funkcja wizualizująca zinterpolowane dane IMGW dla całej powierzchni Polski.




