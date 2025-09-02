### Aktualny status projektu: `In Progress`
- Architektura projektowa, jak i model DB jest już określony i duży rectoring nie jest przewidziany
- Wygląd UI jest do przerobienia. Póki co jest jakikolwiek, by można było używać aplikacji w celu sprawdzenia logiki.   
   Jest nastawiony na płynny UX.

TODO:  
- Kodowanie haseł użytkowników
- Wdrożenie tłumaczeń
- Dokończenie rozpoczętych domenowych procesów
- Wydzielenie akcji poszczególnych endpointów do osobnych wątków CPU za pomocą Workerów  
   (odporność na zamrożenia przy obciążeniu)

# Filozofia architektury projektu

1. Projekt będzie funkcjonował jako SAAS (software as a service)
2. Projekt czerpie z metodyk CQRS oraz DDD
3. Projekt posiada Event Log w celu utrzymania chronologii zdarzeń w aplikacji
4. Komunikacja odbywa się za pomocą Eventów
5. Powtarzalne rzeczy w developmencie realizowane są za pomocą wybranych składni językowych, tzn. - wrapperów, kompozycji, funkcji aplikacyjnych - by zapewnić odporność na błędy, powtarzalność oraz optymalne działanie
6. Każde repozytorium napisane jest w języku TypeScript w formie restrykcyjnej.
7. Kod aplikacji ma być napisany w sposób optymalny, myśląc o wdrożeniu chmurowym, gdzie płaci się za runtime, transfer, deploy-time 
8. Projekt jest podzielony na następujące repozytoria:
- Repozytorium główne - wrapper dla projektu posiadający skrypty do zbudowania kontenerów Docker.
- Shared Kernel - Pojedyncze źródło prawdy, głównie domenowe, z którego korzystają pozostałe repozytoria.
- Frontend - Next.js.
- Backend - Next.js.
- Mobile - Warstwa napisana w React Native, hostująca aplikacje na platformy Android / iOS
8. Większość powtarzalnych czynności jest obsłużona w sposób ułatwiający development za pomocą szkieletu i twardego typowania.  
   Przykładowo:  
   Dodanie nowej funkcjonalności (np. upload zdjęcia w aplikacji) zawsze wymaga kilku powtarzalnych czynności.
   Czynności te zostały obsłużone aplikacyjnie i posiadają łatwe w użyciu zaczepy, z którch korzysta developer bez wchodzenia za każdym razem w zbyt niskopoziomowe szczegóły:  
- Dodanie nazwy nowego Eventu (np. CREATE_GALLERY_RECORD)
- Dodanie ról, które będą do niego uprawnione
- Określenie, czy event ma zapisywać się Event Logu w celu wyświetlenia użytkownikowi jego ostatniej aktywności
- Określenie jak ma wyglądać DTO pomiędzy Frontendem a Backendem (lub dostawcą zewnętrznym)
- Stworzenie endpointu, który out-of-the-box będzie walidować metodę (GET/POST), dane wysłane przez użytkownika (PAYLOAD), uprawnienia do danego eventu i inne krytyczne aspekty
- Określenie, czy po akcji użytkownikowi wyświetli się snackbar w UI
- Wystawienie funkcji-akcji w warsie Frontendowej, która będzie wykorzystana wtedy, gdy user ją wywoła np. klikając DODAJ ZDJĘCIE.

#### Wszystko to jest zawarte w odpowiednich, czytelnych miejscach, by przyspieszyć rozwój oraz ograniczyć ilość bugów do minimum.  
#### Nad większością rzeczy czuwa silnik TypeScript wskazując miejsca jako errory, które trzeba obsłużyć w następnej kolejności.
#### Gdy logika aplikacyjna/domenowa okaże się niemożliwa - TSC nie pozwoli zrobić builda, działając jako testy statyczne.


## Nagranie DEMO wstępnej wersji:

https://vimeo.com/1114964001


## Użyte technologie:

### Frontend:
Next.js + TypeScript + Mui 
  
### Backend:
Next.js + TypeScript

### DB:
Postgres + Prisma

### Platformy mobilne:
React Native



## Wybrane możliwości
1. Obsługa rejestracji
2. Obsługa logowania/wylogowania
3. Obsługa sesji i jej odświeżania - bezpiecznie dla Androida oraz iOS bez korzystania z ciaasteczek
4. Obsługa wielu sesji na wielu urządzeniach z możliwością wylogowywania poszczególnych urządzeń
5. Admin Panel do zarządzania aplikacją oraz użytkownikami
6. Jako Master Admin - możliwość wcielenia się w innego użytkownika (Become User) bez ingerencji w jego sesję i widoczność działań admina dla faktycznego użytkownika.
7. Obsługa zamrażania konta
8. Obsługa usunięcia konta do osobnej tabeli
9. Obsługa procesu płatności
10. Wylistowane ustalenia z polityk na żywo w celu testowania zachowania aplikacji
11. Obsługa ról oraz uprawnień
12. Obsługa krytycznych przekierowań według Roli i jej uprawnień do Routa lub Eventu
13. Obsługa back buttona do poruszania się wstecz
14. Aplikacja jest w pełni zdockeryzowana
15. Aplikacja jest periodycznie monitorowana pod kątem wydajności oraz optymalizowana 


## Instalacja

#### dev
`npm run install:dev` - Pobranie submodułów, instalacja zależności i odpalenie Dockera.

Po instalacji trzeba przejść do `cd ./web-1/` i wygenerować klienta bazy danych: `npm run development db:gen`  
W razie posiadania wolumena Docker z poprzednich instalacji, wystarczy tylko - `npm run development db:pull`  

Po tej czynności aplikacja dostępna jest pod adresem http://localhost:3000

Konto master admina można utworzyć za pomocą cheatu wpisywanego w pole adresu email podczas logowania: `masteradmininit`

Po wpisaniu kodu można już zalogować się na konto podane przy logowaniu.


#### prod
Uzupełnić .environment.prod

Postawić Dockera w trybie produkcyjnym - `npm run install:prod`

Po tej czynności aplikacja dostępna jest pod adresem http://localhost

## Skrypty
Wszystkie dostępne w plikach package.json

Kluczowe do pracy:  
`npm run push` - Tworzy timestampa, commituje i wypycha wszystkie pliki we wszystkich repozytoriach.   
`npm run pull` - Zaciąga wszystkie nowe pliki we wszystkich repozytoriach.  
`npm run kernel` - Jedynie po zmianiach w kernelu - redystrybucja po wszystkich aplikacjach.  

Backend:  
Skrypty muszą być poprzedzane `[development/production]` w celu załadowania odpowiedniego pliku `.environment`.  
Przykład: `npm run development db:pull`  

## shared-kernel
Jest to `Single Source Of Thruth` aplikacji na temat modeli, endpointów, DTO, uprawnień, typów i całej reszty konfiguracji.
#### 
#### 
## Proces dodawania nowego feature - korzystanie z frameworka
##### shared-kernel:
1. W `models` uzupełniamy DTO
2. Dodajemy event `cqrs.config.ts`
3. W `routing.config.ts` deniniujemy ścieżkę do obsługi feature oraz ewentualną stronę frontendową.
4. IDE powinno zgłosić błąd TS w `http.endpoints.ts` - musimy zdefiniować endpoint
5. W `permissions.config.ts` konfigurujemy uprawnienia do eventu i ewentualnego nowego route na frontendzie.
6. Jeśli wymagamy danych z formularza - piszemy walidator payloadu w `validation.policy.ts`
##### backend:
1. Tworzymy katalog zgodnie z wcześniej ustalonym routem w polityce.
2. Kopiujemy tam sąsiedni plik `index.ts`, by mieć dostęp do generycznego middleware `HTTPRequestHandlerMiddleware`
3. W pliku zawieramy jedynie kod domenowy danego feature - innymi rzeczami zajmuje się wcześniej ustalona polityka.
4. Definiujemy metodę POST lub GET.
5. Używamy gotowych typów pilnujących nasze DTO jako importy z kernela.
##### frontend:
1. Z wybranego pliku `IO` powielamy ostatnią funckję zmieniając jej nazwę na taką, jak nazwaliśmy nasz EVENT - ale camelCase.
2. Zmieniamy wszystkie eventy ze skopiowanej funkcji na nowy, przykładowo SET_MAIN_NOTE -> SAVE_NOTE
3. Używamy wystawionego przed chwilą endpoint-handlera w widoku, odpowiednio go obsługując już w React.
4. TypeScript wymusza wszystkie poprawne parametry, ewentualny payload oraz response z serwera po akcji.

##### (Opcjonalnie):  
- Modelujemy funkcjonalność w bazie w `schema.prisma` - backend
- Puszczamy `npm run development db:migrate:dev` - backend
- Przenosimy model z `schema.prisma` do `db_models.ts` - shared-kernel
