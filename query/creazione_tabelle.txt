CREATE TABLE AZIENDA (
    P_Iva           CHAR(11) PRIMARY KEY,
    Nome            VARCHAR(25) NOT NULL,
    Mail            VARCHAR(40),
    Tel             VARCHAR(15)
);

CREATE TABLE SEDE (
    Cod_sede        CHAR(3),
    Indirizzo       VARCHAR(30),
    Citta           VARCHAR(20),
    Stato           VARCHAR(20),
    P_Iva           CHAR(11) NOT NULL UNIQUE,
    N_Piani_totali  INTEGER,
    PRIMARY KEY     (Cod_sede),
    FOREIGN KEY     (P_Iva)
        REFERENCES  AZIENDA
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE UFFICIO ( 
    N_Ufficio       INTEGER,
    N_Piano         INTEGER,
    Cod_sede        CHAR(3) NOT NULL,
    PRIMARY KEY     (N_Ufficio, N_Piano,Cod_sede),
    FOREIGN KEY     (Cod_sede)
        REFERENCES  SEDE
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE LOCALE_SERVER (
    N_Piano         INTEGER,
    N_Locale        INTEGER,
    Cod_sede        CHAR(3) NOT NULL,
    PRIMARY KEY     (N_Piano, N_Locale,Cod_sede),
    FOREIGN KEY     (Cod_sede)
        REFERENCES  SEDE
        ON DELETE CASCADE ON UPDATE CASCADE
);


CREATE TABLE PERSONA ( 
    CF              CHAR(16) PRIMARY KEY,
    Nome            VARCHAR(15) NOT NULL,
    Cognome         VARCHAR(15) NOT NULL,
    Tel             VARCHAR(15),
    Mail            VARCHAR(40)
);
     
CREATE TABLE PERSONALE (
    Matricola       CHAR(6) PRIMARY KEY,
    Stipendio       INTEGER,
    CF              CHAR(16) NOT NULL UNIQUE,
    FOREIGN KEY     (CF)
        REFERENCES  PERSONA
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE MANUTENTORE (
    Matricola       CHAR(6) PRIMARY KEY,
    N_Server        INTEGER,
    N_Piano         INTEGER NOT NULL,
    N_Locale        INTEGER NOT NULL,
    Cod_sede        CHAR(3) NOT NULL,
    CF              CHAR(16) NOT NULL UNIQUE,
    FOREIGN KEY     (Matricola)
        REFERENCES  PERSONALE
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (CF)
        REFERENCES  PERSONA
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (N_Piano, N_Locale,Cod_sede)
        REFERENCES  LOCALE_SERVER
        ON DELETE CASCADE ON UPDATE CASCADE
);


CREATE TABLE TITOLO_MANUTENTORE (
    Matricola       CHAR(6),
    Brevetto        VARCHAR(40),
    PRIMARY KEY     (Matricola, Brevetto),
    FOREIGN KEY     (Matricola)
        REFERENCES  MANUTENTORE
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE PIATTAFORMA (
    Indirizzo_Web   VARCHAR(40),
    P_Iva           CHAR(11),
    PRIMARY KEY     (Indirizzo_Web),
    FOREIGN KEY     (P_Iva)
        REFERENCES  AZIENDA (P_Iva)
        ON DELETE CASCADE ON UPDATE CASCADE    
);

CREATE TABLE IMPIEGATO (
    Matricola       CHAR(6) PRIMARY KEY,
    N_Scrivania     INTEGER,
    N_Piano         INTEGER NOT NULL,
    N_Ufficio       INTEGER NOT NULL,
    Cod_sede        CHAR(3) NOT NULL,
    Matr_capo       CHAR(6),
    Indirizzo_web   VARCHAR(40) NOT NULL,
    CF              CHAR(16) NOT NULL UNIQUE,
    FOREIGN KEY     (Matricola)
        REFERENCES  PERSONALE
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (CF)
        REFERENCES  PERSONA
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (N_Piano, N_Ufficio,Cod_sede)
        REFERENCES  UFFICIO
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Indirizzo_web)
        REFERENCES  PIATTAFORMA
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE TITOLO_IMPIEGATO (
    Matricola       CHAR(6),
    Titolo_studio   VARCHAR(50),
    PRIMARY KEY     (Matricola, Titolo_studio),
    FOREIGN KEY     (Matricola)
        REFERENCES  IMPIEGATO
        ON DELETE CASCADE ON UPDATE CASCADE
);


CREATE TABLE CASA_PRODUTTRICE (
    P_Iva           CHAR(11),
    Nome            VARCHAR(20),
    Stato           VARCHAR(20),
    PRIMARY KEY     (P_Iva)
);

CREATE TABLE CONTENUTI (
    Codice          CHAR(5),
    Nome            VARCHAR(30),
    Trama           VARCHAR(500),
    Regia           VARCHAR(50),
    Casting         VARCHAR(300),
    Genere          VARCHAR(10),
    Durata          TIME (2),
    Anno            CHAR(4),
    Selettore       CHAR(1),
    Prezzo          INTEGER,
    N_Stagione      INTEGER,
    N_Episodio      INTEGER,
    P_Iva           CHAR(11) NOT NULL,
    PRIMARY KEY     (Codice),
    FOREIGN KEY     (P_Iva)
        REFERENCES  CASA_PRODUTTRICE (P_Iva)
        ON DELETE CASCADE ON UPDATE CASCADE,
	CHECK (Selettore = 'F' OR Selettore = 'S')
);

CREATE TABLE ACQUISTA (
    P_Iva           CHAR(11),
    Codice          CHAR(5),
    PRIMARY KEY     (P_Iva, Codice),
    FOREIGN KEY     (P_Iva)
        REFERENCES  AZIENDA (P_Iva)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Codice)
        REFERENCES  CONTENUTI (Codice)
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE CONTIENE (
    Codice          CHAR(5),
    Indirizzo_Web   VARCHAR(40),
    PRIMARY KEY     (Codice, Indirizzo_Web),
    FOREIGN KEY     (Codice)
        REFERENCES  CONTENUTI (Codice)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Indirizzo_Web)
        REFERENCES  PIATTAFORMA (Indirizzo_Web)
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE UTENTE (
    Nome_Utente     VARCHAR(20),
    Password        VARCHAR(30),
    CF              CHAR(16) NOT NULL UNIQUE,
    Indirizzo_Web   VARCHAR(40) NOT NULL,
    PRIMARY KEY     (Nome_Utente),
    FOREIGN KEY     (CF)
        REFERENCES  PERSONA (CF)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Indirizzo_Web)
        REFERENCES  PIATTAFORMA (Indirizzo_Web)
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE PIANO_DI_ABBONAMENTO (
    Nome_Utente     VARCHAR(20) NOT NULL,
    Stato_Piano     VARCHAR(3),
    Data_Inizio     DATE,
    Data_Scadenza   DATE,
    Tipologia       VARCHAR(10),
    Metodo_Di_Pagamento     VARCHAR(20),
    PRIMARY KEY     (Nome_Utente, Stato_Piano),
    FOREIGN KEY     (Nome_Utente)
        REFERENCES  UTENTE (Nome_Utente)
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE PLAYLIST (
    Nome_Utente     VARCHAR(20) NOT NULL,
    Cod_Playlist    CHAR(6),
    Nome_Playlist   VARCHAR(20) NOT NULL,
    PRIMARY KEY     (Nome_Utente, Cod_Playlist),
    FOREIGN KEY     (Nome_Utente)
        REFERENCES  UTENTE (Nome_Utente)
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE AGGIUNTI_A (
    Codice          CHAR(5),
    Nome_Utente     VARCHAR(20),
    Cod_Playlist    CHAR(6),
    PRIMARY KEY     (Codice, Nome_Utente, Cod_Playlist),
    FOREIGN KEY     (Codice)
        REFERENCES  CONTENUTI (Codice)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Nome_Utente, Cod_Playlist)
        REFERENCES  PLAYLIST (Nome_Utente, Cod_Playlist)
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE RECENSIONE (
    Codice          CHAR(5) NOT NULL,
    Voto            INTEGER,
    Commento        VARCHAR(300),
    Nome_Utente     VARCHAR(20) NOT NULL,
    PRIMARY KEY     (Codice, Voto, Commento),
    FOREIGN KEY     (Codice)
        REFERENCES  CONTENUTI (Codice)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Nome_Utente)
        REFERENCES  UTENTE (Nome_Utente)
        ON DELETE CASCADE ON UPDATE CASCADE,
    CHECK (Voto >= 0 AND Voto <= 10)
);


CREATE TABLE VISUALIZZA (
    Nome_Utente     VARCHAR(20),
    Codice          CHAR(5),
    PRIMARY KEY     (Nome_Utente, Codice),
    FOREIGN KEY     (Nome_Utente)
        REFERENCES  UTENTE (Nome_Utente)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Codice)
        REFERENCES  CONTENUTI (Codice)
        ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE PUBBLICITA (
    Codice          CHAR(5) NOT NULL,
    Sito_ospitante  VARCHAR(40),
    P_Iva           CHAR(11) NOT NULL,
    PRIMARY KEY     (Codice, Sito_ospitante),
    FOREIGN KEY     (Codice)
        REFERENCES  CONTENUTI (Codice)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (P_Iva)
        REFERENCES  AZIENDA (P_Iva)
        ON DELETE CASCADE ON UPDATE CASCADE
);


CREATE TABLE CORSO_DI_AGGIORNAMENTO (
    Data            DATE,
    Argomento       VARCHAR(40),
    Durata          TIME (2),
    PRIMARY KEY     (Data, Argomento)
);


CREATE TABLE PARTECIPA_A (
    Data            DATE,
    Argomento       VARCHAR(40),
    Matricola       CHAR(6),
    PRIMARY KEY     (Data, Argomento, Matricola),
    FOREIGN KEY     (Matricola)
        REFERENCES  PERSONALE (Matricola)
        ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY     (Data, Argomento)
        REFERENCES  CORSO_DI_AGGIORNAMENTO
        ON DELETE CASCADE ON UPDATE CASCADE
);


