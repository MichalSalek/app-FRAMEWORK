## Starting app
`yarn install:dev` - All-in do zaciągnięcia submodułów, instalacji zależności i odpalenia Dockera. Czas ~180s. 

Po instalacji przejść do `cd ./web-1/` i wygenerować klienta bazy danych:  
Pierwsze uruchomienie -  `yarn development db:gen`  
Istniejący wolumen - `yarn development db:migrate:dev`  

Po tej czynności aplikacja dostępna jest pod adresem http://localhost:3000

Konto master admina tworzy kod wpisany gdziekolwiek (jak w grze): `masteradmininit`

Po wpisaniu kodu można już zalogować się na konto podane przy logowaniu.


## Runnig prod
Uzupełnić .environment.prod

Postawić Dockera w trybie produkcyjnym - `yarn install:prod`

Po tej czynności aplikacja dostępna jest pod adresem http://localhost
