
Hovedkonklusion: Teamet diskuterer og beslutter en datamodel og API-design, der prioriterer enkelhed, separation af entiteter og DTO'er, samt effektiv datahåndtering for frontend. Nøglebeslutninger omfatter en samlet UserAction entitet, en Contribution entitet for mange-til-mange relationer, og strategier for håndtering af Page objekter og lange tekstfelter som Plot.
​
Datamodellering Generelle Principper
• Enkelhed (Simplicity): Forsøg at gøre modellen så simpel som mulig for at lette samarbejde og undgå unødig kompleksitet.
• Adskillelse af Entiteter og DTO'er (Separation of Entities and DTOs):
	◦ Entiteter: Kernen i datamodellen, aftalt tidligt i processen.
	◦ DTO'er: Supplerende data, der kan tilføjes løbende af webservice-teamet efter behov for specifikke scenarier. De skal ligge i en separat mappe/namespace.
	◦ Formål: undgå at sende unødvendig data (f.eks. plot i listevisninger).
• Effektivitet: Overvej, hvordan data hentes og præsenteres for frontend-gruppen, især for store datafelter eller mange-til-mange relationer.
• Undgå Redundans: Eliminer gentagende information, hvor muligt (f.eks. isSeries, isEpisode i MediaTek).
• Validering: Inkorporer valideringslogik i entiteter for at forhindre ugyldig data i at nå databasen (f.eks. IA4 klasse for årstal).


Nøgleentiteter og Deres Relationer

Page
• Definition: Ikke en traditionel entitet fra starten, men en abstraktion der repræsenterer enten en Title eller en Individual.
• ID-type: Skal være en serial int (ikke char 10 som oprindeligt antaget).
• Forhold til Title og Individual:
	◦ Page-objektet vil indeholde en reference til enten en Title eller en Individual (mutually exclusive).
	◦ Formål: At kunne hente sider baseret på et page-id og få enten en Title eller Individual tilbage.
	◦ Fordel: Gør det nemmere for frontend at håndtere, da de får et generisk Page-objekt.
	◦ Bookmark og Visit vil referere til et Page ID.
• Implikationer: Page skal behandles som en entitet i EF-konteksten for at understøtte relationer (f.eks. til Bookmark, Visit).

User Actions
• Samlet UserAction Entitet:
	◦ Beslutning om at samle alle brugerhandlinger (Page Visit, Search, Rating, Bookmark) under én fælles entitet kaldet UserAction.
	◦ Fælles Attributter: U-const (User ID), Time (timestamp). Dette danner en composite key for UserAction.
	◦ Diskussion om ActionKind Enum: Foreslået at tilføje en public enum ActionKind { PageVisit, Search, Rating, Bookmark } for at skelne mellem handlingstyperne. Dette gør det nemmere at arbejde med polymorfe modeller og flade DTO'er.
	◦ Underklasser af UserAction:
		‣ Page Visit: Indeholder Page (det besøgte side-objekt).
		‣ Search: Indeholder Search String. Search History skal være en entitet med CRUD (create, read, delete) muligheder.
		‣ Rating: Indeholder en Title (kan kun rate titler ifølge nuværende model). Refererer til User og Title. Skal være en entitet med CRUD.
		‣ Bookmark: Indeholder en Page. Sammenlignet med Visit – princippielt ens, men beskriver forskellige handlinger. Skal være en entitet med CRUD.
	◦ Visited Pages: Skal være en entitet med CRUD, ligner en browserhistorik, der sporer, hvilke sider en bruger har besøgt.
	◦ Argument for fælles UserAction: Muliggør en samlet læsbar "action historic" for en bruger (f.eks. på en profilside).

Contribution
• Forhold: Mange-til-mange relation mellem Individual og Title.
• Implementering: Skal være en separat entitet (Contribution) for at håndtere mange-til-mange relationen korrekt i EF Core (som en bridge table).
	◦ Analogi: Order Details i Northwind databasen.
	◦ Attributter: Udover Title ID og Individual ID (der udgør en composite key), vil den indeholde Contribution (rolle, f.eks. "actor") og Detail (specifik detalje, f.eks. "karakteren de spiller"), samt Priority.
• Datahåndtering:
	◦ Når man beder om en Title, hentes relevante Contributors.
	◦ Når man beder om en Individual, hentes relevante Titles de har bidraget til.
	◦ GetContributionDetails vil være en DTO/service-metode for at få specifikke bidragsdetaljer.

Title
• Episodes / Parent Series:
	◦ En Title kan have en liste af Episodes (som også er Titles).
	◦ En Episode Title vil have en Parent Series (nullable Title).
	◦ Disse relationer skal være gensidigt udelukkende.
• Plot Preview: En kort streng (f.eks. de første 20 tegn af plottet) som en attribut i Title-entiteten, med "..." hvis længere.
• Genres: En liste af Genres (som en enum eller list af strings) som en attribut i Title-entiteten. Ikke en separat genre-entitet, men kan være en entity hvis den kun peger i én retning.

Individual
• Har en liste af Titles, de har bidraget til (via Contribution entiteten).

User
• Attributter: User ID, Registration Time (timestamp).
• Billeder: Mulighed for fremtidig implementering af profilbilleder (gemmes som bytes).

Genre
• Håndtering: En liste af strings eller en enum (enum Genre { ... }).
• Ingen separat entitet: Besluttet at ikke have en Genre entitet for at undgå unødvendig kompleksitet.

Plot
• Håndtering af lang tekst: Plottet (den fulde lange streng) skal hentes separat som en DTO (TitleWithPlot) eller via en getPlot service-metode.
• Argument for separation: Undgå at sende store datamængder unødigt, da det fulde plot kun er relevant i specifikke visninger (f.eks. på en filmside).

Tekniske Overvejelser
• Page ID: Skal være af typen int (serial) i databasen for effektivitet, selvom string bruges internt i nogle systemer.
• Composite Keys: Anerkendelse af behovet for composite keys i entiteter som Contribution (Title ID + Individual ID) og UserAction (User ID + Time).
• Nullable Fields: Behov for nullable felter i entiteter for at håndtere specielle tilfælde eller valgfri data.
• DTO Naming Conventions: Bør beskrive hvad DTO'en er (ProductWithCategory), snarere end hvorfra den kommer (f.eks. metode-specifikke navne).
• Validering (IA4 klasse): Brug af en specifik klasse for at sikre validitet af årstal (f.eks. at det er et rimeligt årstal) for at undgå databasefejl.
• API Design: Fokus på at give frontend de nødvendige nøgler til at hente yderligere detaljer on-demand, frem for at inkludere al potentielt irrelevant data i et enkelt kald.
• EF Core: Viden om EF Cores muligheder for at håndtere relationer og DTO'er er afgørende.



