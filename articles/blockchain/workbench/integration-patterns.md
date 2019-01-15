---
title: Smarta kontrakt integration mönster i Azure Blockchain Workbench
description: Översikt över smarta kontrakt integration mönster i Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 1e1bc16d32167d62d5f66f64bb383fcceeb79eb5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267155"
---
# <a name="smart-contract-integration-patterns"></a>Smarta kontrakt integration mönster

Smarta kontrakt representerar ofta ett företagsarbetsflöde som krävs för att integrera med externa system och enheter.

Kraven för de här arbetsflödena omfattar ett behov av att initiera transaktioner på ett distribuerat transaktionsregister som innehåller data från ett externt system, en tjänst eller en enhet. De måste också vara externa system som reagerar på händelser från smarta kontrakt på en distribuerad redovisning.

REST-API och meddelanden integrering ger möjlighet att både skicka transaktioner från externa system till smarta kontrakt som ingår i ett Azure Blockchain Workbench-program, samt skicka händelsemeddelanden till externa system baserat på ändringar som ta Placera i ett program.

För dataintegrering innehåller Azure Blockchain Workbench en uppsättning databasvyer som slå sammanfogar en kombination av transaktionsdata från blockkedje- och metadata om program och smarta kontrakt.

Dessutom kan vissa scenarier, till exempel de som är relaterade till att ange kedja eller media, även kräva integreringen av dokument. Medan Azure Blockchain Workbench inte ger API-anrop för att hantera dokument direkt, kan dokument införlivas i ett Azure Blockchain-program. Det här avsnittet innehåller även mönstret.

Det här avsnittet innehåller de mönster som identifierats för att implementera de olika typerna av integreringar i dina lösningar från slutpunkt till slutpunkt.

## <a name="rest-api-based-integration"></a>REST API-baserad integrering

Funktioner i Azure Blockchain Workbench genereras webbprogrammet exponeras via REST API. Bland funktionerna finns Azure Blockchain Workbench överföringen, konfiguration och administration av program som skickar transaktioner till en distribuerad redovisning och frågor efter programdata för metadata och redovisning.

REST-API används främst för interaktiva klienter, till exempel webb, mobil, och bot-program.

Det här avsnittet tittar vi på mönster som fokuserar på de aspekter av REST-API som skicka transaktioner till en distribuerad redovisning och de som fråga efter data om transaktioner från Azure Blockchain Workbench *av kedjan* SQL-databas.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Skicka transaktioner till en distribuerad redovisning från ett externt system

Azure Blockchain Workbench REST-API ger möjlighet att skicka autentiserade begäranden att köra transaktioner på en distribuerad redovisning.

![Skicka transaktioner till en distribuerad redovisning](./media/integration-patterns/send-transactions-ledger.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   Det externa programmet autentiseras mot Azure Active Directory som tillhandahålls som en del av Azure Blockchain Workbench-distribution.
-   Auktoriserade användare få en ägartoken som kan skickas med begäranden till API: et.
-   Externa program göra anrop till REST-API med hjälp av ägartoken.
-   REST API-paket på begäran som ett meddelande och skickar den till en Service Bus. Härifrån kommer det att hämtas, registrerat och skickas till lämplig distribuerat transaktionsregister.
-   REST-API gör en begäran till Azure Blockchain Workbench SQL DB att registrera begäran och upprätta aktuella Etableringsstatus.
-   SQL-databas returnerar etableringsstatusen och API-anrop returnerar det ID: T för det externa programmet som kallas den.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Frågor till Blockchain Workbench metadata och distribuerat transaktionsregister transaktioner

Azure Blockchain Workbench REST-API ger möjlighet att skicka autentiserade begäranden till Frågedetaljer som rör smarta kontrakt körning på en distribuerad redovisning.

![Fråga metadata](./media/integration-patterns/querying-metadata.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

1. Det externa programmet autentiseras mot Azure Active Directory som tillhandahålls som en del av Azure Blockchain Workbench-distribution.
2. Auktoriserade användare få en ägartoken som kan skickas med begäranden till API: et.
3. Externa program göra anrop till REST-API med hjälp av ägartoken.
4. REST-API frågar data för begäran från SQL DB och returneras till klienten.

## <a name="messaging-integration"></a>Integrering av meddelanden

Meddelanden integration underlättar interaktion med system, tjänster och enheter där en interaktiv inloggning inte är möjligt eller önskvärt. Meddelanden integration fokuserar på två typer av meddelanden, de som begär att transaktioner köras på en distribuerad redovisning och händelser som exponeras av den transaktionsregister när transaktioner har ägt rum.

Integrering av meddelanden som fokuserar på körning och övervakning av transaktioner rör skapa användare, kontrakt och körning av transaktioner på kontrakt och används främst av *fjärradministrerad* backend-system.

Det här avsnittet tittar vi på mönster som fokuserar på de aspekter av meddelandebaserat API: et som skickar transaktioner till en distribuerad redovisning och de som representerar händelsemeddelanden som exponeras av den underliggande distribuerat transaktionsregister.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Enkelriktad händelseleverans från ett smarta kontrakt till en händelsekonsument 

I det här scenariot kan inträffar en händelse inom ett smarta kontrakt, till exempel en tillståndsändring eller körning av en viss typ av transaktionen. Den här händelsen sänds via en Event Grid för underordnade konsumenterna och konsumenterna och vidta lämpliga åtgärder.

Ett exempel på det här scenariot är att när en transaktion utförs en konsument skulle bli aviserad om och kan vidta åtgärder, till exempel spela in informationen i en SQL-databas eller Common Data Service. Det här är samma mönster som arbetsstationen följer för att fylla i dess *av kedjan* SQL DB.

En annan skulle vara om ett smarta kontrakt övergår till ett visst tillstånd, till exempel när ett kontrakt hamnar i ett *OutOfCompliance*. När den här tillståndsändringen sker kan det utlösa en avisering ska skickas till en administratörs mobiltelefon.

![Enkelriktad händelseleverans](./media/integration-patterns/one-way-event-delivery.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   Smarta kontrakt övergår till ett nytt tillstånd och skickar en händelse till huvudboken.
-   Redovisningen tar emot och levererar händelsen till Azure Blockchain Workbench.
-   Azure Blockchain Workbench prenumererar händelser från redovisningen och tar emot händelsen.
-   Azure Blockchain Workbench publicerar händelsen till prenumeranter i Event Grid.
-   Externa system som prenumererar på Event Grid, förbrukar meddelandet och vidta lämpliga åtgärder.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Enkelriktad händelseleverans i ett meddelande från ett externt system till ett smarta kontrakt

Det finns också ett scenario som flödar från motsatt riktning. I det här fallet en händelse genereras av en sensor eller ett externt system och data från händelsen ska skickas till ett smarta kontrakt.

Ett vanligt exempel är leverans av data från finansiella marknader, till exempel priserna för produkterna, lager eller värdepapper, till ett smarta kontrakt.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkt leverans av en Azure Blockchain Workbench det förväntade formatet

Vissa program har skapats för att integrera med Azure Blockchain Workbench och direkt genererar och skicka meddelanden i de förväntade formaten.

![Direkt leverans](./media/integration-patterns/direct-delivery.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   En händelse inträffar på ett externt system som utlöser skapandet av ett meddelande för Azure Blockchain Workbench.
-   Det externa systemet har kod som skrivs till att skapa det här meddelandet i ett känt format och skickar det direkt till en Service Bus.
-   Azure Blockchain Workbench prenumererar händelser från Service Bus och hämtar meddelandet.
-   Azure Blockchain Workbench initierar ett anrop till redovisningen, skicka data från det externa systemet till ett specifikt kontrakt.
-   Kontraktet övergår till ett nytt tillstånd vid mottagning av meddelandet.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett okänt format till Azure Blockchain Workbench

Vissa system kan inte ändras för att skicka meddelanden i standardformat som används av Azure Blockchain Workbench. I dessa fall, befintliga mekanismer och meddelandet användas ofta format från dessa system. Mer specifikt kan internt meddelande om typer av dessa system omvandlas använder Logic Apps, Azure Functions eller andra anpassad kod för att mappa till en standard messaging format som förväntat.

![Okänd meddelandeformat](./media/integration-patterns/unknown-message-format.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   En händelse inträffar på ett externt system som utlöser skapandet av ett meddelande.
-   En Logikapp eller anpassad kod används för att ta emot meddelandet och transformera det till ett standardmeddelande för Azure Blockchain Workbench formaterade.
-   Logikappen skickar transformerade meddelandet direkt till en Service Bus.
-   Azure Blockchain Workbench prenumererar händelser från Service Bus och hämtar meddelandet.
-   Azure Blockchain Workbench initierar ett anrop till redovisningen, skicka data från det externa systemet till en viss funktion i kontraktet.
-   Funktionen Kör och vanligtvis ändrar tillståndet. Ändring av tillstånd flyttar framåt affärsarbetsflödet återspeglas i smart kontraktet, aktivering av andra funktioner som nu ska köras efter behov.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Övergång kontroll till en extern bearbeta och await slutförande

Det finns scenarier där ett smarta kontrakt måste stoppa intern körning och leverera till en extern process. Att extern process skulle Slutför, fortsätter skicka ett meddelande till smarta kontrakt och körning sedan i smarta kontrakt.

#### <a name="transition-to-the-external-process"></a>Övergång till extern process

Det här mönstret implementeras vanligtvis med följande metod:

-   Smarta kontrakt övergår till ett visst tillstånd. I det här tillståndet antingen Nej eller ett begränsat antal funktioner kan köras förrän ett externt system tar en önskad åtgärd.
-   Ändring av tillstånd har angetts som en händelse till en underordnad konsument.
-   Underordnade konsumenten får händelsen och utlöser externa kodkörning.

![Kontroll av övergången till extern process](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Returnera kontroll från smarta kontrakt

Beroende på möjligheten att anpassa det externa systemet kanske eller kanske inte kan leverera meddelanden i ett standardformat som förväntar sig att Azure Blockchain Workbench. Baserat på externa system möjligheten avgör att generera något av dessa meddelanden vilka av följande två returnerade sökvägar som ska vidtas.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkt leverans av en Azure Blockchain Workbench det förväntade formatet

![](./media/integration-patterns/direct-delivery.png)

I den här modellen kommunikationen till kontraktet och efterföljande tillståndsändring sker följande ovanstående bearbeta var -

-   När du når slutförs eller en viss milstolpe i den externa kodkörningen, skickas en händelse till en Service Bus som är anslutna till Azure Blockchain Workbench.

-   För system som inte kan anpassas direkt för att skriva ett meddelande som överensstämmer med till API: et förväntningar, omvandlas.

-   Innehållet i meddelandet paketeras och skickas till en viss funktion i smarta kontrakt. Detta görs för användaren som är associerade med det externa systemet.

-   Funktionen Kör och vanligtvis ändrar tillståndet. Ändring av tillstånd flyttar framåt affärsarbetsflödet återspeglas i smart kontraktet, aktivering av andra funktioner som nu ska köras efter behov.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett okänt format till Azure Blockchain Workbench

![Okänd meddelandeformat](./media/integration-patterns/unknown-message-format.png)

I den här modellen där ett meddelande i ett standardformat inte kan skickas direkt, kommunikation till kontraktet och efterföljande tillståndsändring sker följande ovanstående bearbeta var:

1.  När du når slutförs eller en viss milstolpe i den externa kodkörningen, skickas en händelse till en Service Bus som är anslutna till Azure Blockchain Workbench.
2.  En Logikapp eller anpassad kod används för att ta emot meddelandet och transformera det till ett standardmeddelande för Azure Blockchain Workbench formaterade.
3.  Logikappen skickar transformerade meddelandet direkt till en Service Bus.
4.  Azure Blockchain Workbench prenumererar händelser från Service Bus och hämtar meddelandet.
5.  Azure Blockchain Workbench initierar ett anrop till redovisningen, skicka data från det externa systemet till ett specifikt kontrakt.
6. Innehållet i meddelandet paketeras och skickas till en viss funktion i smarta kontrakt. Detta görs för användaren som är associerade med det externa systemet.
7.  Funktionen Kör och vanligtvis ändrar tillståndet. Ändring av tillstånd flyttar framåt affärsarbetsflödet återspeglas i smart kontraktet, aktivering av andra funktioner som nu ska köras efter behov.

## <a name="iot-integration"></a>IoT-integrering

Ett vanligt scenario för integration är inkludering av telemetridata som hämtats från sensorer i ett smarta kontrakt. Baserat på data från sensorer, kunde smarta kontrakt ta välgrundade åtgärder och ändra tillståndet för kontraktet.

Till exempel om en lastbil leverera medicin hade temperaturen Sväva till 110 grader, det kan påverka effektiviteten i medicinen och kan orsaka problem för en allmän säkerhet om inte har identifierats och tas bort från leveranskedjan. Om en drivrutin accelerated sin bil till 100 miles per timme, kan resulterande sensorinformation utlösa en uppsägning av insurance av hans försäkring providern. Om bilen var en bil, kan GPS-data indikera när drivrutinen gick utanför ett geografiskt område som omfattas av hans hyresavtal och debiterar en särskilda avgifter.

Utmaningen är att dessa sensorer som kan leverera data på basis av konstant och det är inte lämpligt att skicka alla dessa data till ett smarta kontrakt. En vanlig metod är att begränsa antalet meddelanden som skickas till blockkedja som du leverera alla meddelanden till en sekundär butik. Exempelvis kan leverera meddelanden som tas emot med endast fast intervall, till exempel en gång i timmen och när en innesluten värdet ligger utanför en överenskommen på intervallet för ett smarta kontrakt. Kontroll av värden som faller utanför toleranser, säkerställer att data som är relevanta för affärslogik kontrakt tas emot och körs. Kontrollera värdet vid tider som bekräftar att fortfarande rapporterar. Alla data skickas till en sekundär reporting butik för att aktivera bredare rapportering, analys och maskininlärning. Vid komma sensoravläsningar för GPS inte kanske är nödvändiga varje minut för ett smarta kontrakt, kan de till exempel ange intressanta data som ska användas i rapporter eller mappning vägar.

På Azure-plattformen görs integrering med enheter normalt med IoT Hub. IoT Hub ger dig möjlighet att skicka meddelanden baserat på innehåll och gör typ av funktion som beskrivs ovan.

![IoT-meddelanden](./media/integration-patterns/iot.png)

Processen ovan visar ett mönster för detta är implementerat:

-   En enhet kommunicerar direkt eller via en fält-gateway till IoT Hub.
-   IoT-hubb tar emot meddelanden och utvärderar meddelanden mot vägar som upprättats som till exempel kontrollera innehållet i meddelandet. *Rapporterar sensorn en temperatur som är större än 50 grader?*
-   IoT-hubben skickar meddelanden som uppfyller villkoren för att en definierad Service Bus för vägen.
-   En Logikapp eller annan kod lyssnar till en Service Bus som IoT Hub har upprättats för vägen.
-   Logikappen eller annan kod hämtar och omvandlar meddelandet till ett känt format.
-   Transformerade meddelandet nu skickas i ett standardformat till Service Bus för Azure Blockchain Workbench.
-   Azure Blockchain Workbench prenumererar händelser från Service Bus och hämtar meddelandet.
-   Azure Blockchain Workbench initierar ett anrop till redovisningen, skicka data från det externa systemet till ett specifikt kontrakt.
-   Vid mottagning av meddelandet kontraktet utvärderar data och kan ändra tillståndet baserat på resultatet av den bedömningen, till exempel för en hög temperatur, ändrar du läget till *efterlevnad av*.

## <a name="data-integration"></a>Dataintegrering

Förutom REST och meddelandebaserat API tillhandahåller Azure Blockchain Workbench även åtkomst till en SQL-databas som fyllts med program- och metadata, samt transaktionsdata från distribuerad redovisning.

![Dataintegrering](./media/integration-patterns/data-integration.png)

Dataintegrering är välkänd:

-   Azure Blockchain Workbench lagras metadata om program, arbetsflöden, kontrakt och transaktioner som en del av normal drift aktivitet.
-   Ange en eller flera dialogrutor för att underlätta insamling av information om databasen, t.ex namn på databasserver, databasens namn, typ av autentisering, inloggningsuppgifter och vilken databas som visar för att använda externa system eller verktyg.
-   Frågor skrivs mot SQL-databasvyer för att underlätta underordnade förbrukning av externa system, tjänster, rapportering, utvecklingsverktyg och enterprise produktivitetsverktyg.

## <a name="storage-integration"></a>Storage-integrering

Många scenarier kan kräva behovet av att införliva attestable filer. Flera skäl blir det olämpligt att skicka filer till en blockkedja. I stället är en vanlig metod att utföra en kryptografisk hash (till exempel SHA-256) mot en fil och dela den hashvärdet för en distribuerad redovisning. Utför hashen igen när som helst framtida ska returnera samma resultat. Om filen ändras även om bara en bildpunkt ändras i en bild, returneras hash-värdet ett annat värde.

![Storage-integrering](./media/integration-patterns/storage-integration.png)

Mönstret kan implementeras där:

-   Ett externt system kvarstår en fil i en mekanism för lagring, till exempel Azure Storage.
-   Ett hash-värde som genereras med filen eller filen och associerade metadata, till exempel en identifierare för ägare, URL: en där filen finns, osv.
-   Hash-värdet och alla metadata som skickas till en funktion på ett smart kontrakt, till exempel *FileAdded*
-   I framtiden fil- och metadata hashas igen och jämförs mot de värden som lagras på huvudboken.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Förutsättningar för att implementera mönster för integrering med hjälp av REST och meddelandet API: er

För att underlätta möjlighet för ett externt system eller en enhet för att interagera med smarta kontrakt med REST eller meddelande API, måste följande utföras-

1. I Azure Active Directory för consortium skapas ett konto som representerar den externa system eller enhet.
2. Smart eller rätt avtal för Azure Blockchain Workbench-programmet har funktioner som definierats för att acceptera händelser från din externa system eller enhet.
3. Programmets konfigurationsfil för dina smarta kontrakt innehåller rollen som systemet eller enheten kommer att tilldelas.
4. Programmets konfigurationsfil för dina smarta kontrakt identifierar i vilka tillstånd som den här funktionen kan anropas av rollen definierad.
5. Programmets konfigurationsfil och dess smarta kontrakt överförs till Azure Blockchain Workbench.

När programmet har överförts, har Azure Active Directory-konto för det externa systemet tilldelats kontraktet och tillhörande rolltjänster.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testa externt System Integrationsflöden innan du skriva kod för integrering 

Ger möjligheten att integrera med externa system är ett krav för många scenarier. Det är önskvärt att kunna verifiera smarta kontrakt design tidigare eller parallellt till utvecklingen av kod för att integrera med externa system.

Användning av Azure Active Directory (Azure AD) kan avsevärt snabbare produktivitet för utvecklare och tid till värde. Mer specifikt kan code-integrering med ett externt system ta en icke-trivialt tidsperiod. Med hjälp av Azure AD och den automatisk genereringen av UX genom Azure Blockchain Workbench, detta att utvecklare kan logga in på Workbench som det externa systemet och fylla i statusvärden förväntades från det externa systemet via UX. På så sätt kan för att snabbt utveckla och validera idéer i en proof of concept-miljö före att eller parallellt till integrering kod skrivs för externa system.
