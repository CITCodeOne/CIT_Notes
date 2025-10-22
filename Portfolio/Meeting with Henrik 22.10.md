Dette dokument beskriver en tilgang til softwareudvikling med fokus på vertikal arbejdsdeling, modulære datatjenester og strategiske overvejelser om placering af funktionalitet (f.eks. i database vs. backend). Nøglebudskaber er at undgå unødvendig kompleksitet for frontend, optimere dataudlevering og fremme kritisk tænkning omkring designvalg, selvom der ikke altid findes én "korrekt" løsning.

Vertikal Arbejdsmetode
Opstart af Tre Endpoints
• Formål: Vise et givent user ID, en bookmark for et givent user ID, og at et movie ID skaber en bookmark. Mulighed for at validere den igen.
• Dette udgør tre endpoints.
Datatjeneste Struktur
• Proces: Opret et bookmark-objekt, map det til databasen, få datatjenesten til at virke, bind den til controlleren, verificer output.
• Vertikal arbejde: Opdel arbejdet i separate "slices" for forskellige domæneområder (f.eks. movies, actors, persons).
• Hver del får sin egen datatjeneste.
Fordeling af Arbejde
• Fordel: Mulighed for at dele arbejde ud, så teammedlemmer ikke arbejder på samme sted samtidigt.
• Hver vertikale "stang" (f.eks. bookmarks, movies) har en dedikeret datatjeneste.
• Mål: Hver datatjeneste udstiller data til web-servicelaget, som kan tage det, de har brug for.
Flaskehalse og Løsninger i DB Kontekst
• Flaskehals: DB-konteksten i Entity Framework.
• Løsning: Én person tager ansvar for opsætningen for at undgå merge-konflikter.
• Konfigurationsisolation: Brug funktionskald (f.eks. create-bookbox-config, create-movie-config) indenfor OnConfiguring-metoden for at strukturere og isolere konfigurationen af individuelle objekter.
	◦ Dette holder hovedkonfigurationsmetoden ren og delegerer specifikke konfigurationer til dedikerede metoder.

Design af Entiteter og Grænseflader (Interfaces)
Principper for Datatjenester
• Interface-drevet design: Opret en interface-klasse for hver datatjeneste (f.eks. IBookmarksService, IMovieService).
• Derefter implementeres grænsefladen (f.eks. BookmarksService, MovieService).
• Modulær injektion: I stedet for én stor datatjeneste, oprettes en for hver. Controlleren kan derefter injicere de specifikke grænseflader, den har brug for.
Fordele ved Mindre Moduler
• Isolerede moduler: Mindre, isolerede dele med specifikke ansvarsområder.
• Forbedringer:
	◦ Nemmere at forstå.
	◦ Nemmere at bruge.
	◦ Nemmere at udvikle.

Teamarbejde og Kvalitetssikring
Individuelt Fokus med Peer Review
• Anbefaling: Tildel specifikke opgaver (f.eks. "du laver bookmarks") til individuelle teammedlemmer.
• Peer Review: Når en del er færdig, præsenteres den for de andre for feedback ("Hvordan ser det ud?", "Forstår I det?", "Er det overkompliceret?").
• Fordel: Skaber ejerskab og muliggør parallel fremdrift i stedet for sekventiel "safe and check".
Fælles Opgavestruktur
• Grundlæggende opgaver er de samme for hver vertikal stang:
	◦ 1.	Konfigurer Entity Framework.
	◦ 2.	Opret objekter.
	◦ 3.	Skriv LINQ-udtryk til dataudtræk.
	◦ 4.	Transformér data og udlevér fra servicen.

Placering af Databasefunktionalitet
Frontend vs. Backend/Database
• Diskussion om hvor funktionalitet skal placeres.
• Generel regel: Man kan altid diskutere placering, men nogle ting ligger bedre ét sted end et andet.

Argumenter for Database-Løsninger (Avancerede Søgninger, Sideeffekter)
• Avancerede databasefunktioner: F.eks. søgninger med information-typel (full-text search), ranking, word-indexes.
	◦ Argument for database: Hurtigere adgang til data, ingen dataflytning, indkapsler kompleks logik. Klienten (backend) behøver kun at kalde en funktion uden at kende de interne detaljer om søgemetoder.
• Funktioner med sideeffekter: F.eks. en søgefunktion, der automatisk opdaterer søgehistorikken.
	◦ C# (backend): Kræver to separate statements (søgning, derefter indsættelse i historik). Risiko for at glemme den ene, hvilket bryder funktionaliteten.
	◦ Database: Kan bages ind i én databasefunktion, så en søgning altid opdaterer historikken, hvilket sikrer konsistens og forhindrer fejl.

Risici ved Klient-Side Implementering af Afhængigheder
• Hvis funktionalitet, der har en implicit afhængighed (f.eks. søgning og logning af søgehistorik), er opdelt på klient-siden (C#), opstår en dependency mellem kaldene.
• Problem: Folk skal vide, at de ikke må lave det ene uden det andet. Over tid kan dette glemmes eller kommenteres ud, hvilket fører til fejl, der er svære at diagnosticere.

Rapportering og Læring
Dokumentation af Ændringer
• Hvis der er ændringer i databasen (f.eks. forbedringer i backend), skal disse rapporteres.
• Den første del af rapporten skal omhandle opdateringer fra det foregående projekt.
• Dette inkluderer ændringer i datamodeller (ER-diagrammer) og fejlrettelser i databasefunktioner.
• Vigtigt: De tre rapporter skal afspejle den aktuelle tilstand og historikken for udviklingen.

Værdi i at Blive Klogere
• Det er acceptabelt at blive klogere og modificere tidligere arbejde.
• Vejledere giver ikke feedback på afleverede projekter, der indgår i eksamen, men besvarer gerne generelle spørgsmål undervejs.
• At opdage og rette egne fejl er en værdifuld del af læringsprocessen.

Pædagogisk Tilgang til Systemudvikling
• Den anvendte systemudviklingsproces er pedagogisk tilpasset og adskiller sig fra standardindustripraksis.
• Formål: At studerende gradvist lærer komponenterne (database, backend, frontend) separat, før de integreres, for at undgå overvældende kompleksitet tidligt i semesteret.

Datatjenestens Rolle i Forhold til Frontend
Adskillelse af Database- og Frontendbehov
• Vigtigt princip: Hvad databasen indeholder, og hvad hjemmesiden skal kunne vise, er to forskellige ting.
• Datatjenesten behøver ikke at afspejle databasen 1:1. Den kan sammenfatte flere databasetabeller til et enkelt objekt for at imødekomme frontends behov.

Balancering af Endpoints og Dataudlevering
• Problem: Mange små endpoints (f.eks. 7 forskellige for en enkelt visning) for frontend fører til for mange kald.
• Ønskværdigt: Backend bør levere en rigere repræsentation af data (f.eks. et "movie"-objekt med alle nødvendige detaljer) via et enkelt endpoint. Dette holder frontend så dum som muligt.

Principper for Dataudlevering
• Lever kun nødvendig data: Der er ingen grund til at levere mere data end frontend faktisk skal bruge (f.eks. alle skuespillere for en filmliste, hvis kun titlen vises).
• Fordele ved at begrænse data:
	◦ Undgår unødvendig sortering på klienten.
	◦ Reducerer datamængde over netværket, hvilket gør applikationen hurtigere.

Designvalg og Argumentation
• Mange designvalg er holdningsbaserede.
• Fokus på eksamen: Forstå hvorfor I har truffet bestemte valg, og kunne argumentere for fordele og ulemper (f.eks. med hensyn til scalability, testability, reusability).
• Der findes ikke ét "rigtigt" svar, men bedre løsninger afhængigt af konteksten.

Tekniske Overvejelser: HTTP/TCP Overhead
• Problem: Mange små endpoints kan medføre en HTTP/TCP connection overhead (især med HTTP/1).
• Paralleller: Ligner teknikker som "bundling" af JavaScript/CSS for at reducere kald og forbedre ydeevne.
• Dette er endnu et argument for at overveje færre, mere omfattende endpoints i backend.

Vigtigste Pointe
Det handler om at tænke sig om og argumentere for de designvalg, man træffer.