1) Selezionare i registi dei contenuti che non siano tra i 'Film' di genere 'Drammatico':

select regia
	from contenuti c
		where c.regia not in (
			select c2.regia
				from contenuti c2
					where selettore = 'F'
					and genere = 'Drammatico'
					)
order by regia desc;

2) Selezionare gli UTENTI che hanno visualizzato tutti i CONTENUTI visualizzati dall'UTENTE con nome utente ‘johncarano’:

select *
	from utente u
		where not exists(
			select *
				from visualizza v1
					where v1.nome_utente = 'johncarano'
					and not exists (
						select *
							from visualizza v2
								where u.nome_utente = v2.nome_utente
								and v1.codice = v2.codice
										)
)

3)Selezionare, per ciascun genere, il numero totale di visualizzazioni ed ordinarle in maniera crescente:

select genere, count(genere) as tot
	from visualizza, contenuti
		where visualizza.codice = contenuti.codice
			group by genere
			order by 2 asc;

4)Selezionare la matricola dei MANUTENTORI che non lavorano in 'Italia':

select matricola as "Matricola"
	from manutentore m, sede s
		where m.cod_sede = s.cod_sede and s.stato <> 'Italia';

5)Selezionare il nome dell'AZIENDA che ha acquistato CONTENUTI sia dalla CASA PRODUTTRICE 'Warner Bros', che da 'Walt Disney':

select a.nome as "Nome azienda
	from acquista q, azienda a, contenuti c, casa_produttrice p
		where q.p_iva = a.p_iva and q.codice = c.codice
		and c.p_iva = p.p_iva and p.nome = 'Warner Bros'
	intersect 
select a.nome
	from acquista q, azienda a, contenuti c, casa_produttrice p
		where q.p_iva = a.p_iva and q.codice = c.codice
		and c.p_iva = p.p_iva and p.nome = 'Walt Disney';

6) Impiegato con stipendio più alto per ogni azienda

SELECT W.NOME as "Nome azienda", X.MATRICOLA as "Matricola", X.STIPENDIO as "Stipendio"
FROM PERSONALE AS X, IMPIEGATO AS Y, PIATTAFORMA AS Z, AZIENDA AS W
WHERE X.MATRICOLA=Y.MATRICOLA AND Y.INDIRIZZO_WEB=Z.INDIRIZZO_WEB 
	AND Z.P_IVA=W.P_IVA AND X.STIPENDIO >= ALL (
		SELECT X2.STIPENDIO 
		FROM PERSONALE AS X2, IMPIEGATO AS Y2 
		WHERE X2.MATRICOLA=Y2.MATRICOLA AND Y2.INDIRIZZO_WEB=Z.INDIRIZZO_WEB 
			AND Z.P_IVA=W.P_IVA)
GROUP BY W.NOME,X.MATRICOLA;

7) Soldi spesi dalle aziende per comprare i contenuti

SELECT X.NOME as "Nome azienda",SUM(Y.PREZZO) as "Costo totale"
	FROM AZIENDA AS X,CONTENUTI AS Y,ACQUISTA AS Z	
		WHERE X.P_IVA=Z.P_IVA AND Z.CODICE=Y.CODICE
			GROUP BY X.NOME;

8) Numero film guardati da un utente di cui ha lasciato anche la recensione

SELECT X.NOME_UTENTE,COUNT(Y.NOME_UTENTE)
	FROM UTENTE AS X,RECENSIONE AS Y, VISUALIZZA AS Z
		WHERE X.NOME_UTENTE=Z.NOME_UTENTE AND EXISTS(
SELECT X.NOME_UTENTE
	WHERE Z.CODICE=Y.CODICE AND X.NOME_UTENTE=Y.NOME_UTENTE)
		GROUP BY X.NOME_UTENTE

9) Classifica in base al voto medio dei film di genere drammatico

SELECT X.NOME as "Nome contenuto",AVG(Y.VOTO) as "Media voto"
	FROM CONTENUTI AS X,RECENSIONE AS Y
		WHERE X.CODICE=Y.CODICE AND X.GENERE='Drammatico'
			GROUP BY X.NOME
			ORDER BY 2 DESC	

10) Numero partecipazioni dei dipendenti ai corsi di aggiornamenti nel mese di luglio

SELECT X.MATRICOLA,COUNT(Y.MATRICOLA)
	FROM PERSONALE AS X,PARTECIPA_A AS Y
		WHERE X.MATRICOLA=Y.MATRICOLA AND Y.DATA BETWEEN '2020-07-01' AND '2020-07-31'
			GROUP BY X.MATRICOLA

11) Utenti con un abbonamento attivo e relativa data di scadenza

select nome_utente as "Nome utente", data_scadenza as "Data scadenza"
	from piano_di_abbonamento
		where stato_piano = 'ON'
		order by 1 asc;
12) Tipologia di abbonamento e relativo numero di abbonati attivi

select tipologia as "Tipo piano", count(nome_utente) as "Numero abbonati"
	from piano_di_abbonamento
		where tipologia = 'Premium' or tipologia = 'Base' or tipologia = 'Family' and stato_piano = 'ON'
			group by tipologia;
13) Aumento degli stipendi per tutti i dipendenti sotto i 1000€

update personale
	set stipendio = stipendio + 500
		where stipendio < '1000';

14) Numero di vendite per ogni contenuto ordinato in ordine decrescente

select acquista.codice as "Codice film", contenuti.nome as "Nome contenuto", count(acquista.codice) as "Numero vendite"
	from acquista, contenuti
		where acquista.codice = contenuti.codice
		group by acquista.codice, contenuti.nome
		order by 3 desc;


