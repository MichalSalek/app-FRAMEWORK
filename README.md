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

#### Backend
Skrypty muszą być poprzedzane `[development/production]`  
Przykład: `yarn development db:pull`  

