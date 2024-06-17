[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/J0Dv0VMM)
# Exam #1: "Gioco dei meme"
## Student: s323270 CAZZOLA MICHELE

## React Client Application Routes

- Route `/`: page content and purpose
- Route `/something/:param`: page content and purpose, param specification
- ...

## API Server
### Autenticazione

__Login__

URL: `/memegame/sessions`

HTTP method: POST

Descrizione: effettua il login

Request parameters: None

Request body content:
```
{
	"username": "user1",
	"password": "pwd1" 
}
```

Response code:
- `200 OK`
- `401 Unauthorized`: credenziali errate
- `500 Internal server error`: errore generico

Response body content: None

Vincoli di accesso: nessun vincolo

__Get user info__

URL: `/memegame/sessions/current`

HTTP method: GET

Descrizione: recupera informazioni sull'utente autenticato

Request parameters: None

Request body content: None

Response code:
- `200 OK`
- `401 Unauthorized` (utente non autenticato)
- `500 Internal server error` (errore generico)

Response body content:
```
{
	"username": "user1",
	"name": "Mario",
	"surname": "Rossi"
}
```

Vincoli di accesso: può essere chiamata solo da un utente autenticato

__Logout__

URL: `/memegame/sessions/current`

HTTP method: DELETE

Descrizione: effettua il logout

Request parameters: None

Request body content: None

Response code:
- `200 OK`
- `401 Unauthorized` (utente non autenticato)
- `500 Internal server error` (errore generico)

Response body content: None

Vincoli di accesso: può essere chiamata solo da un utente autenticato

### Meme

__Get collezione di tre meme differenti__

URL: `/memegame/memes/match`

HTTP method: GET

Descrizione: recupera tre meme differenti dal database, in modo casuale, per giocare una partita completa

Request parameters: None

Request body content: None

Response code:
- `200 OK`
- `401 Unauthorized` (utente non autenticato)
- `500 Internal server error` (errore generico)

Response body content:
```
[
	{
		"id": 2,
		"name": "meme2.png"
	},
	{
		"id": 4,
		"name": "meme4.png"
	},
	{
		"id": 7,
		"name": "meme7.png"
	}
]
```

Vincoli di accesso: può essere chiamata solo da un utente autenticato

__Get singolo meme__
URL: `/memegame/memes/single`

HTTP method: GET

Descrizione: recupera un meme dal database in modo casuale

Request parameters: None

Response parameters: None

Response code:
- `200 OK`
- `500 Internal server error` (errore generico)

Response body content:
```
{
	"id": 6,
	"name": "meme6.png"
}
```

Vincoli di accesso: nessun vincolo

### Captions

__Get due didascalie associate ad un meme__

URL `/memegame/memes/:id/captions`

HTTP method: GET

Descrizione: recupera dal database due tra le didascalie associate al meme con id specificato

Request parameters: `id` - id del meme di cui recuperare le due didascalie associate

Request body content: None

Response code:
- `200 OK`
- `404 Not found`: non ci sono almeno due didascalie per il meme specificato, oppure il meme non esiste
- `500 Internal server error`: errore generico

Response body content:
```
[
	{
		"id": 1,
		"text": "Lorem ipsum"
	},
	{
		"id": 3,
		"text": "Dolor sit amet"
	}
]
```

Vincoli di accesso: nessun vincolo

__Get cinque didascalie non associate ad un meme__

URL `/memegame/memes/not/:id/captions`

HTTP method: GET

Descrizione: recupera dal database cinque tra le didascalie non associate al meme con id specificato

Request parameters: `id` - id del meme specificato

Request body content: None

Response code:
- `200 OK`
- `404 Not found`: non ci sono almeno cinque didascalie non associate con il meme specificato
- `500 Internal server error`: errore generico

Response body content:
```
[
	{
		"id": 1,
		"text": "Lorem ipsum"
	},
	{
		"id": 3,
		"text": "Dolor sit amet"
	},
	{
		"id": 10,
		"text": "Lorem ipsum dolor"
	},
	{
		"id": 12,
		"text": "ipsum dolor sit amet"
	},
	{
		"id": 21,
		"text": "Lorem ipsum dolor sit amet"
	}
]
```

Vincoli di accesso: nessun vincolo

### Match

__Get di tutte le partite completate da un utente autenticato__

URL: `/memegame/matches/history`

HTTP method: GET

Descrizione: recupera tutte le partite dell'utente corrente dal database, insieme alle informazioni sui singoli round

Request parameters: None

Request body content: None

Response code:
- `200 OK`
- `401 Unauthorized`: utente non autenticato
- `500 Internal server error`: errore generico

Response body content:
```
[
	{	
		"points": 10,
		"rounds": [
			{
				"id": 1,
				"meme": "meme2.png",
				"points": 5,
				"caption": "caption3"
			},
			{
				"id": 2,
				"meme": "meme5.png",
				"points": 5,
				"caption": "caption12"
			}
		]
	},
	...
]
```

Vincoli di accesso: può essere chiamata solo da un utente autenticato

__Inserimento dati partita completata__

URL: `/memegame/matches`

HTTP method: POST

Descrizione: inserisce nel database i dati della partita appena completata dall'utente (autenticato) corrente

Request parameters: None

Request body content: None

Response code:
- `201 Created`
- `401 Unauthorized`: utente non autenticato
- `500 Internal server error`: errore generico

Vincoli di accesso: può essere chiamata solo da un utente autenticato


## Database Tables

- Tabella `user` - contiene:
  - `UserId`: id dell'utente, chiave primaria
  - `Username`: username dell'utente
  - `Name`: nome dell'utente
  - `Surname`: cognome dell'utente
  - `Password`: password (crittografata) dell'utente
  - `Salt`: sale associato alla password dell'utente
- Tabella `match` - contiene:
  - `MatchId`: chiave primaria (autoincrementale) della partita 
  - `UserId`: chiave esterna all'id dell'utente che ha giocato la partita
- Tabella `round` - contiene:
  - `MatchId`: chiave esterna all'id della partita e chiave primaria del round, insieme a _roundId_ (costituisce un indice unico con _memeId_)
  - `RoundId`: valore compreso tra 1 e 3, chiave primaria del round, insieme a _matchId_
  - `Guessed`: valore booleano (nel database, intero pari a 0 o 1), indica se l'utente ha indovinato il meme per il round
  - `MemeId`: chiave esterna all'id del meme mostrato per il round corrente (costituisce un indice unico con _matchId_)
  - `CaptionId`: chiave esterna (eventualmente _null_) alla didascalia selezionata dall'utente per il round corrente
- Tabella `caption`: - contiene:
  - `CaptionId`: chiave primaria (autoincrementale) della didascalia
  - `Text`: testo della didascalia
- Tabella `meme`: - contiene:
  - `MemeId`: chiave primaria (autoincrementale) del meme
  - `Name`: nome del meme (come immagine), con path relativo alla cartella `/meme/`
- Tabella `correct_caption` - rappresenta un'associazione molti-a-molti tra un meme e le sue didascalie corrette:
  - `CaptionId`: chiave esterna all'id della didascalia
  - `MemeId`: chiave esterna all'id del meme

## Main React Components

- `ListOfSomething` (in `List.js`): component purpose and main functionality
- `GreatButton` (in `GreatButton.js`): component purpose and main functionality
- ...

(only _main_ components, minor ones may be skipped)

## Screenshot

![Screenshot](./img/screenshot.jpg)

## Users Credentials

- username, password (plus any other requested info)
- username, password (plus any other requested info)
