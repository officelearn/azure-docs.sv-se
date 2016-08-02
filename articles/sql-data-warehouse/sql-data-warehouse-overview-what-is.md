<properties
   pageTitle="Vad är Azure SQL Data Warehouse | Microsoft Azure"
   description="En distribuerad databas i företagsklass som kan bearbeta petabytevolymer med relationella och icke-relationella data. Den är branschens första informationslager i molnet som kan växa, krympa och pausa på sekunden."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# Vad är Azure SQL Data Warehouse?

Azure SQL Data Warehouse är en molnbaserad skalbar databas som kan bearbeta massiva mängder data, relationell såväl som icke-relationell. Det bygger på vår arkitektur med massivt parallell bearbetning (MPP). SQL Data Warehouse kan hantera ditt företags arbetsbelastningar. 

SQL Data Warehouse:

- Kombinerar vår beprövade relationella SQL Server-databas med våra Azure-skalbara molnfunktioner. Du kan öka, minska, pausa eller återuppta databearbetning sekundsnabbt.  På så sätt kan du spara kostnader genom att skala ut CPU vid behov och dra ner på användningen under tider med låg belastning.
- Utnyttjar vår Azure-plattform. Är enkel att distribuera och underhålls sömlöst. och fullständigt feltolerant tack vare automatisk säkerhetskopiering. 
- Kompletterar SQL Server-ekosystemet.  Du kan utveckla med välbekanta SQL Server T-SQL och verktyg.

Läs vidare för att få veta mer om nyckelfunktionerna i SQL Data Warehouse.

## Optimerad

### Arkitektur med Massivt Parallell Bearbetning (MPP)

SQL Data Warehouse använder sig av Microsofts arkitektur med massivt parallell bearbetning (MPP), som är utformad för att köra några av världens största lokala informationslager.

Vår MPP-arkitektur sprider för tillfället ut dina data över 60 shared-nothing-lagrings- och bearbetningsenheter. Data lagras i Azure Storage-Blobbar i Premium Storage och länkas till Compute-noder för frågekörning. Med den här arkitekturen, har vi en söndra och erövra metod för att köra komplexa T-SQL-frågor. Vid bearbetning så parsar Control-noden frågan och sedan ”erövrar” varje Compute-nod sin datadel parallellt. 

Genom att kombinera vår MPP-arkitektur och Azure-lagringsfunktioner, kan SQL Data Warehouse:

- Öka eller minska lagring oberoende av beräkning.
- Öka eller minska beräkning utan att flytta data. 
- Pausa beräkningskapacitet och bibehålla alla data.
- Återuppta beräkningskapaciteten på ett ögonblick.

Arkitekturen beskrivs i detalj nedan. 

![SQL Data Warehouse-arkitektur][1]


- **Control-node:** Control-noden är den som kontrollerar systemet. Det är den som är klientdelen som interagerar med alla program och anslutningar. Control-noden i SQL Data Warehouse drivs av SQL Database och det ser ut och känns på samma sätt att ansluta till den. Under ytan samordnar Control-noden all dataflytt och alla beräkningar som krävs för att köra parallella frågor på dina distribuerade data. När du skickar in en TSQL-fråga till SQL Data Warehouse, omvandlar Control-noden den till separata frågor som körs parallellt på varje Compute-nod.

- **Compute-noder:** Compute-noderna utgör kraften bakom SQL Data Warehouse. De är SQL-databaser som bearbetar dina frågesteg och hanterar dina data. När du lägger till data, distribuerar SQL Data Warehouse raderna med hjälp av dina Compute-noder. Compute-noderna är också ”arbetarna” som kör de parallella frågorna på dina data. Efter bearbetning, skickar de resultaten tillbaka till Control-noden. För att slutföra frågan, aggregerar Control-noden resultaten och returnerar slutresultatet.


- **Storage:** Dina data lagras i Azure Storage Blobbar. När Compute-noder interagerar med dina data, skriver de och läser direkt till och från blobblagring. Eftersom Azure-lagring expanderar helt transparent och utan begränsningar, kan SQL Data Warehouse också göra det. Eftersom beräkning och lagring sker oberoende av varandra, kan SQL Data Warehouse automatiskt skala lagring separat från beräkning och vice versa.  Azure Storage är också fullständigt feltolerant och effektiviserar säkerhetskopierings- och återställningsprocesserna.
   

- **Data Movement Service:** Data Movement Service (DMS) är vår teknologi för att flytta data mellan noderna. DMS ger Compute-noderna åtkomst till de data de behöver för kopplingar och aggregeringar. DMS är inte en Azure-tjänst. Det är en Windows-tjänst som körs samtidigt som SQL Database på alla noderna. Eftersom DMS körs i bakgrunden, kommer du inte direkt att interagera med det. När du tittar på frågeplaner, kommer du dock att se att de inkluderar vissa DMS-åtgärder, eftersom någon form av dataflytt behövs för att köra varje fråga parallellt.


### Optimerad frågeprestanda

Utöver söndra och erövra strategin, får MPP-metoden hjälp av ett antal prestandaoptimeringar som är specifika för informationslager, inklusive:

- En distribuerad frågeoptimerare och en uppsättning komplex statistik för alla data. Med information om datastorlek och distribution kan tjänsten optimera frågor genom att uppskatta kostnaden för specifika distribuerade frågeåtgärder.

- Avancerade algoritmer och tekniker som integrerats i processen med dataflytt för att effektivt flytta data mellan bearbetningsresurser efter behov för att genomföra frågan. De här dataflyttsåtgärderna är inbyggda och alla optimeringar av Data Movement Service sker automatiskt.

- Grupperad kolumnlagring indexeras som standard. Med hjälp av kolumnbaserad lagring får SQL Data Warehouse ut upp till 5 gånger så hög komprimering jämfört med traditionell, radbaserad lagring och upp till 10 gånger så hög frågeprestanda. Analytics-frågor som behöver skanna ett stort antal rader fungerar bra på kolumnlagringsindex. 

## Skalbar

Arkitekturen för SQL Data Warehouse introducerar åtskild lagring och beräkning, vilket gör att varje del kan skalas för sig. SQL Database snabba och enkla distributionsstruktur gör att ytterligare beräkningsresurser kan göras tillgängliga sekundsnabbt. Användningen av Azure Storage Blobs kompletterar det. Blobbar ger inte bara en stabil, replikerad lagring, utan också en infrastruktur för enkel expansion till låg kostnad.  Den här kombinationen av skalbar molnlagring och Azure-beräkning, låter SQL Data Warehouse dig betala för frågeprestanda-lagring efter behov och vid behov. Att ändra beräkningsmängden är så lätt som att dra ett skjutreglage i Azure-portalen till höger eller vänster, men det kan också schemaläggas med hjälp av T-SQL och PowerShell.

Förutom att fullständigt kontrollera beräkningskraften oberoende av lagringen, så låter SQL Data Warehouse dig pausa ditt informationslager. Utan att röra din lagring, så släpps all beräkningskraft till Azures huvudsakliga pool, vilket omedelbart sparar pengar åt dig. Vid behov, kan du återuppta beräkningen och ha dina data och din beräkningskraft tillgänglig för din arbetsbelastning.

Användning av beräkningskraft i SQL Data Warehouse mäts i SQL-informationslagerenheter (DWU:er). DWU:er är ett mått på den underliggande kraft som ditt informationslager har och är skapade för att se till att du har en standardiserad prestandakraft associerat med ditt informationslager vid en given tidpunkt.  Mer specifikt använder vi DWU:er för att tillse att:

- Du kan skala ditt informationslager effektivt, utan att behöva bry dig om underliggande maskinvara eller programvara.

- Du kan få ett grepp på vilken prestanda du kommer att se på DWU-nivå innan du ändrar storlek på ditt informationslager.

- Den underliggande maskin- och programvaran i din instans kan ändras eller flyttas utan påverkan på din arbetsbelastningsprestanda

- Vi kan göra justeringar i den underliggande arkitekturen för tjänsten utan att påverka prestandan för din arbetsbelastning.

- Allteftersom prestandan i SQL Data Warehouse snabbt förbättras, kan vi se till att det görs på ett sätt som är skalbart och har en jämn inverkan på systemet.

### Informationslagerenheter

Mer specifikt så är informationslagerenheter en sammanställning av tre specifika mått som vi anser har en stark korrelation med arbetsbelastningsprestanda i informationslager. För den allmänna tillgängligheten, är vårt mål att de här nyckeltalen för arbetsbelastning ska vara linjärt skalbara med de DWU:er som du har valt för ditt informationslager.

**Genomsökning/aggregering:** Det här arbetsbelastningsmåttet tar en standard informationslagerfråga som genomsöker ett stort antal rader och sedan genomför en komplex aggregering. Den här åtgärden är IO- och CPU-intensiv.

**Inläsning:** Det här mätvärdet mäter förmågan att mata in data i tjänsten. Inläsningar genomförs genom att PolyBase läser in en representativ datauppsättning från en Azure Storage Blob. Måttet är tänkt att vara påfrestande för nätverks- och CPU-aspekterna i tjänsten.

**CREATE TABLE AS SELECT (CTAS):** CTAS mäter förmågan att skapa en tabellkopia. Det innebär att läsa data från lagring, distribuera den över enhetens noder och skriva in den i lagring på nytt. Det är en CPU- och nätverksintensiv åtgärd.

### När ska du skala

Vår tanke är att DWU:er ska vara enkla. När du behöver snabbare resultat, ökar du dina DWU:er och betalar för bättre prestanda.  När du behöver mindre datorkraft, minskar du dina DWU:er och betalar bara för det du behöver. Några tillfällen när du bör överväga att ändra antal DWU:er är:

- När du inte behöver köra frågor, kanske på kvällar eller helger, kan du pausa alla beräkningsresurser för att avbryta alla frågor som körs och ta bort alla DWU:er som allokerats till ditt informationslager.

- När du utför en tung datainläsnings- eller omvandlingsåtgärd, kan du vilja skala upp så att dina data blir tillgängliga snabbare.

- För att förstå vad ditt perfekta DWU-värde är, kan du testa att skala upp och ner och köra några frågor efter att ha läst in dina data. Eftersom det går snabbt att skala, kan du testa flera olika prestandanivåer utan att lägga ner mer tid än en timme.

> [AZURE.NOTE] Observera att på grund av arkitekturen för SQL Data Warehouse, är det möjligt att du inte märker förväntade prestandakapaciteter vid lägre datavolymer.  Vi rekommenderar att du börjar med datavolymer som är minst 1 TB för att få en sann indikation av prestandafördelarna.

## Integrerat

SQL Data Warehouse är baserat på SQL Servers beprövade relationella databasmotor och innehåller flera av de funktioner som du förväntar dig från ett informationslager i företagsklass. Om du redan känner till Transact-SQL, är det lätt att överföra din kunskap till SQL Data Warehouse. Oavsett om du har avancerade kunskaper eller bara precis kommit igång, så kommer exemplen i dokumentationen att hjälpa dig på traven. Generellt sett så kan du tänka på sättet som vi byggt de språkliga elementen i SQL Data Warehouse på följande vis:

- SQL Data Warehouse använder sig av SQL Server Transact-SQL (TSQL) syntax för många åtgärder och stöder ett stort antal traditionella SQL-uppbyggnader som lagrade procedurer, användardefinierade funktioner, tabellpartitionering, index och sortering.

- SQL Data Warehouse innehåller även ett antal banbrytande SQL Server-funktioner som grupperade kolumnlager-index, PolyBase-integrering och Data Auditing (med hotbedöming).

- Eftersom SQL Data Warehouse fortfarande är under utveckling, är det vissa TSQL-språkelement som är mindre vanliga för arbetsbelastningar i informationslager, eller som är nyare för SQL Server, som inte är tillgängliga än. Se vår migreringsdokumentation för mer information om det här.

Med Transact-SQL och funktionslikheterna mellan SQL Server, SQL Data Warehouse, SQL Database och Analytics Platform System, kan du utveckla en lösning som passar dina databehov. Du kan själv välja var du vill ha dina data, baserat på prestanda-, säkerhets- och skalningskrav och kan sedan överföra dina data vid behov mellan olika system.

Förutom att använda sig av det TSQL-utsatta området för SQL Server så integrerar SQL Data Warehouse också med många av de verktyg som SQL Server-användare är bekanta med. Vi har särskilt fokuserat på att integrera ett antal kategorier verktyg med SQL Data Warehouse, inklusive:

**Traditionella SQL Server Tools:** SQL Data Warehouse har en fullständig integrering med SQL Server Analysis Services, Integration Services och Reporting services.

**Molnbaserade verktyg:** SQL Data Warehouse kan användas med ett antal nya verktyg i Azure och har djupgående integrering med Azure Data Factory, Stream Analytics, Machine Learning och Power BI.

**Verktyg från tredjepart:** Ett stort antal leverantörer av tredjeparts-verktyg har certifierad integrering av sina verktyg med SQL Data Warehouse. Se hela listan.

## Hybrid

Att använda SQL Data Warehouse med PolyBase ger användare en oöverträffad förmåga att flytta data över sina ekosystem, vilket öppnar upp för att ställa in avancerade hybridscenarier med icke-relationella såväl som lokala datakällor.

Polybase är lätt att använda och låter dig utnyttja dina data från olika källor med hjälp av samma bekanta T-SQL-kommandon. PolyBase låter dig fråga icke-relationella data som finns i Azure-blobblagring precis som det vore en vanlig tabell. Använd Polybase för att fråga icke-relationella data, eller importera dina icke-relationella data till SQL Data Warehouse.

- PolyBase använder sig av externa tabeller för att få åtkomst till icke-relationella data. Tabelldefinitionerna lagras i SQL Data Warehouse och kan kommas åt av SQL och verktyg som du skulle använda för normala, relationella data.

- Polybase är integreringsneutralt. Det visar samma funktioner och egenskaper för alla källor som det stöder. Data läses av Polybase kan vara i en rad olika format, inklusive avgränsade eller ORC-filer.

- PolyBase kan användas för att komma åt blobblagring som även används som lagring för ett HDInsight-kluster, vilket ger dig den senaste åtkomsten till samma data med relationella såväl som icke-relationella verktyg.

## Nästa steg

Nu när du vet lite om SQL Data Warehouse, läs mer om [arbetsbelastningen för informationslager], [hur man etablerar] ett SQL Data Warehouse och [hur man läser in exempeldata].  Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

- [Bloggar] 
- [Funktionsbegäranden]
- [Videoklipp]
- [CAT-teambloggar]
- [Skapa ett supportärende]
- [MSDN-forum]
- [Stack Overflow-forum]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[arbetsbelastningen för informationslager]: ./sql-data-warehouse-overview-workload.md
[så här läser du in exempeldata]: ./sql-data-warehouse-get-started-manually-load-samples.md
[så här etablerar du]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT-teambloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse



<!--HONumber=Jun16_HO2-->


