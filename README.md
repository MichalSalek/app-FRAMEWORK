Spis treści:
- [Instalacja](#instalacja)
- [Proces dodawania nowego feature - korzystanie z frameworka](#proces-dodawania-nowego-feature---korzystanie-z-frameworka)
- [Wybrane możliwości](#wybrane-możliwości)



# Instalacja

## Starting app
`yarn install:dev` - All-in do zaciągnięcia submodułów, instalacji zależności i odpalenia Dockera. Czas ~180s. 

Po instalacji przejść do `cd ./web-1/` i wygenerować klienta bazy danych: `yarn development db:gen`
Masz istniejący wolumen z poprzednich instalacji - `yarn development db:pull`  

Po tej czynności aplikacja dostępna jest pod adresem http://localhost:3000

Konto master admina tworzy kod wpisany gdziekolwiek (jak w grze): `masteradmininit`

Po wpisaniu kodu można już zalogować się na konto podane przy logowaniu.


## Runnig prod
Uzupełnić .environment.prod

Postawić Dockera w trybie produkcyjnym - `yarn install:prod`

Po tej czynności aplikacja dostępna jest pod adresem http://localhost

## Scripts
Wszystkie dostępne w plikach package.json

Kluczowe do pracy:  
`yarn push` - Tworzy timestampa, commituje i wypycha wszystkie pliki we wszystkich repozytoriach.   
`yarn pull` - Zaciąga wszystkie nowe pliki we wszystkich repozytoriach.  
`yarn kernel` - Jedynie po zmianiach w kernelu - redystrybucja po wszystkich aplikacjach.  

Backend:  
Skrypty muszą być poprzedzane `[development/production]` w celu załadowania odpowiedniego pliku `.environment`.  
Przykład: `yarn development db:pull`  

## Repozytoria
main - frontend www  
web-1 - główny backend  
mobilka - nie używane  
web-2 - nie używane  

## shared-kernel
Jest to `Single Source Of Thruth` aplikacji na temat modeli, endpointów, DTO, uprawnień, typów i całej reszty konfiguracji.
### 
### 
# Proces dodawania nowego feature - korzystanie z frameworka
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
- Puszczamy `yarn development db:migrate:dev` - backend
- Przenosimy model z `schema.prisma` do `db_models.ts` - shared-kernel

# Wybrane możliwości
1. Obsługa rejestracji
2. Obsługa logowania/wylogowania/sesji i jej odświeżania
3. Obsługa wielu sesji na wielu urządzeniach z możliwością wylogowywania poszczególnych urządzeń
4. Admin Panel do zarządzania aplikacją oraz użytkownikami
5. Jako Master Admin - możliwość wcielenia się w innego użytkownika (Become User) bez ingerencji w jego sesję i widoczność działań admina.
6. Obsługa zamrażania konta
7. Obsługa usunięcia konta
8. Obsługa procesu płatności
9. Wylistowane ustalenia z polityk na żywo w celu testowania zachowania aplikacji
10. Kody:
    - `masteradmininit` - tworzenie konta testowego master admina
    - `connectioncheck` - wyświetla monit do pingowania wszystkich aplikacji ze sobą
    - `debugon` i `debugoff` - obsługa frontendowego trybu debugowania
    - `backenddebug` - obsługa backendowego trybu debugowania
    - `backenddbdebug` - obsługa debugowania zapytań z bazą danych
