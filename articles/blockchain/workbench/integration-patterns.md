---
title: Smarta mönster för kontraktsintegrering - Azure Blockchain Workbench
description: Översikt över smarta kontraktsintegreringsmönster i förhandsversionen av Azure Blockchain Workbench.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: f9626edd5bd655e3de5d0f9648041faf832e3b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325973"
---
# <a name="smart-contract-integration-patterns"></a>Integreringsmönster för smart kontrakt

Smarta kontrakt representerar ofta ett affärsarbetsflöde som måste integreras med externa system och enheter.

Kraven för dessa arbetsflöden inkluderar ett behov av att initiera transaktioner i en distribuerad redovisning som innehåller data från ett externt system, en tjänst eller en enhet. De måste också ha externa system reagera på händelser som kommer från smarta kontrakt i en distribuerad liggare.

REST API- och meddelandeintegrationen skickar transaktioner från externa system till smarta kontrakt som ingår i ett Azure Blockchain Workbench-program. Det skickar också händelsemeddelanden till externa system baserat på ändringar som sker i ett program.

För scenarier för dataintegration innehåller Azure Blockchain Workbench en uppsättning databasvyer som sammanfogar en kombination av transaktionsdata från blockkedjan och metadata om program och smarta kontrakt.

Dessutom kan vissa scenarier, till exempel de som rör leveranskedjan eller media, också kräva att dokument integreras. Azure Blockchain Workbench tillhandahåller inte API-anrop för hantering av dokument direkt, men dokument kan införlivas i ett blockchain-program. Det här avsnittet innehåller också det mönstret.

Det här avsnittet innehåller de mönster som identifierats för att implementera var och en av dessa typer av integrationer i dina helhetslösningar.

## <a name="rest-api-based-integration"></a>REST API-baserad integrering

Funktioner inom Azure Blockchain Workbench genererade webbprogram exponeras via REST API. Funktionerna inkluderar Azure Blockchain Workbench-uppladdning, konfiguration och administration av program, överföring av transaktioner till en distribuerad redovisning och frågor om programmetadata och redovisningsdata.

REST API används främst för interaktiva klienter som webb-, mobil- och botapplikationer.

I det här avsnittet undersöks mönster som fokuserar på de aspekter av REST API:et som skickar transaktioner till en distribuerad redovisning och mönster som frågar data om transaktioner från Azure Blockchain Workbenchs *SQL-databas utanför kedjan.*

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Skicka transaktioner till en distribuerad redovisning från ett externt system

AZURE Blockchain Workbench REST API skickar autentiserade begäranden för att köra transaktioner i en distribuerad redovisning.

![Skicka transaktioner till en distribuerad redovisning](./media/integration-patterns/send-transactions-ledger.png)

Utför transaktioner sker med hjälp av den process som tidigare avbildats, där:

-   Det externa programmet autentiserar till Azure Active Directory-etableringen som en del av Azure Blockchain Workbench-distributionen.
-   Behöriga användare får en innehavartoken som kan skickas med begäranden till API:et.
-   Externa program anropar REST API med hjälp av innehavartoken.
-   REST API paketerar begäran som ett meddelande och skickar den till servicebussen. Härifrån hämtas, signeras och skickas den till lämplig distribuerad redovisning.
-   REST API gör en begäran till Azure Blockchain Workbench SQL DB att registrera begäran och fastställa den aktuella etablering status.
-   SQL DB returnerar etableringsstatusen och API-anropet returnerar ID:t till det externa programmet som anropade det.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Fråga Blockchain Workbench-metadata och distribuerade redovisningstransaktioner

AZURE Blockchain Workbench REST API skickar autentiserade begäranden till frågeinformation som är relaterad till smart kontraktskörning i en distribuerad redovisning.

![Fråga metadata](./media/integration-patterns/querying-metadata.png)

Fråga sker med hjälp av den process som tidigare avbildats, där:

1. Det externa programmet autentiserar till Azure Active Directory-etableringen som en del av Azure Blockchain Workbench-distributionen.
2. Behöriga användare får en innehavartoken som kan skickas med begäranden till API:et.
3. Externa program anropar REST API med hjälp av innehavartoken.
4. REST API frågar data för begäran från SQL DB och returnerar den till klienten.

## <a name="messaging-integration"></a>Integrering av meddelanden

Meddelandeintegrering underlättar interaktion med system, tjänster och enheter där en interaktiv inloggning inte är möjlig eller önskvärd. Meddelandeintegrering fokuserar på två typer av meddelanden: meddelanden som begär transaktioner ska utföras i en distribuerad redovisning och händelser som exponeras av den redovisningen när transaktioner har ägt rum.

Meddelandeintegration fokuserar på utförande och övervakning av transaktioner relaterade till skapande av användare, skapande av kontrakt och genomförande av transaktioner på kontrakt och används främst av *huvudlösa* backend-system.

I det här avsnittet undersöks mönster som fokuserar på de aspekter av det meddelandebaserade API:et som skickar transaktioner till en distribuerad redovisning och mönster som representerar händelsemeddelanden som exponeras av den underliggande distribuerade redovisningen.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Enkelriktad händelseleverans från ett smart avtal till en evenemangskonsument 

I det här fallet inträffar en händelse i ett smart kontrakt, till exempel en tillståndsändring eller körning av en viss typ av transaktion. Den här händelsen sänds via ett evenemangsnät till konsumenter i senare led och dessa konsumenter vidtar sedan lämpliga åtgärder.

Ett exempel på det här scenariot är att när en transaktion inträffar, en konsument skulle varnas och kan vidta åtgärder, till exempel spela in informationen i en SQL DB eller Common Data Service. Det här scenariot är samma mönster som Workbench följer för att fylla sin *utanför kedjan* SQL DB.

En annan skulle vara om ett smart kontrakt övergår till en viss stat, till exempel när ett kontrakt går in i en *OutOfCompliance*. När den här tillståndsändringen inträffar kan det utlösa en avisering som ska skickas till en administratörs mobiltelefon.

![Enkelriktad händelseleverans](./media/integration-patterns/one-way-event-delivery.png)

Det här scenariot inträffar med hjälp av den process som tidigare avbildats, där:

-   Det smarta kontraktet övergår till ett nytt tillstånd och skickar en händelse till redovisningen.
-   Liggaren tar emot och levererar händelsen till Azure Blockchain Workbench.
-   Azure Blockchain Workbench prenumererar på händelser från redovisningen och tar emot händelsen.
-   Azure Blockchain Workbench publicerar händelsen till prenumeranter på event grid.
-   Externa system prenumererar på händelserutnätet, förbrukar meddelandet och vidtar lämpliga åtgärder.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Enkelriktad händelseleverans av ett meddelande från ett externt system till ett smart kontrakt

Det finns också ett scenario som rinner från motsatt riktning. I det här fallet genereras en händelse av en sensor eller ett externt system och data från den händelsen ska skickas till ett smart kontrakt.

Ett vanligt exempel är leverans av data från finansmarknaderna, till exempel priser på råvaror, aktier eller obligationer, till ett smart kontrakt.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direktleverans av en Azure Blockchain Workbench i förväntat format

Vissa program är byggda för att integreras med Azure Blockchain Workbench och genererar och skickar meddelanden direkt i förväntade format.

![Direktleverans](./media/integration-patterns/direct-delivery.png)

Denna leverans sker med hjälp av den process som tidigare avbildats, där:

-   En händelse inträffar i ett externt system som utlöser skapandet av ett meddelande för Azure Blockchain Workbench.
-   Det externa systemet har kod skriven för att skapa det här meddelandet i ett känt format och skickar det direkt till servicebussen.
-   Azure Blockchain Workbench prenumererar på händelser från servicebussen och hämtar meddelandet.
-   Azure Blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till ett visst kontrakt.
-   När meddelandet har mottagits övergår kontraktet till ett nytt tillstånd.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett format som är okänt för Azure Blockchain Workbench

Vissa system kan inte ändras för att leverera meddelanden i standardformat som används av Azure Blockchain Workbench. I dessa fall kan befintliga mekanismer och meddelandeformat från dessa system ofta användas. De inbyggda meddelandetyperna för dessa system kan omvandlas med hjälp av Logic Apps, Azure Functions eller annan anpassad kod för att mappa till ett av de standardmeddelandeformat som förväntas.

![Okänt meddelandeformat](./media/integration-patterns/unknown-message-format.png)

Detta sker med hjälp av den process som tidigare avbildats, där:

-   En händelse inträffar i ett externt system som utlöser skapandet av ett meddelande.
-   En Logik App eller anpassad kod används för att ta emot det meddelandet och omvandla det till en standard Azure Blockchain Workbench formaterat meddelande.
-   Logikappen skickar det transformerade meddelandet direkt till servicebussen.
-   Azure Blockchain Workbench prenumererar på händelser från servicebussen och hämtar meddelandet.
-   Azure Blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till en specifik funktion i kontraktet.
-   Funktionen körs och ändrar vanligtvis tillståndet. Ändringen av tillståndet flyttar framåt affärsarbetsflödet som återspeglas i det smarta kontraktet, vilket gör att andra funktioner nu kan köras efter behov.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Övergångskontroll till en extern process och invänta slutförande

Det finns scenarier där ett smart kontrakt måste stoppa intern körning och lämna över till en extern process. Den externa processen skulle sedan slutföras, skicka ett meddelande till det smarta kontraktet och körningen skulle sedan fortsätta inom det smarta kontraktet.

#### <a name="transition-to-the-external-process"></a>Övergång till den externa processen

Det här mönstret implementeras vanligtvis med följande metod:

-   Det smarta kontraktet övergår till ett visst tillstånd. I det här läget kan antingen inga eller ett begränsat antal funktioner köras tills ett externt system vidtar en önskad åtgärd.
-   Tillståndsändringen visas som en händelse för en nedströmskonsument.
-   Den nedströmskonsumenten tar emot händelsen och utlöser extern kodkörning.

![Övergångskontroll till extern process](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Återlämnande av kontroll från det smarta kontraktet

Beroende på möjligheten att anpassa det externa systemet kan det eller kanske inte kan leverera meddelanden i något av de standardformat som Azure Blockchain Workbench förväntar sig. Baserat på de externa systemens förmåga att generera ett av dessa meddelanden avgör vilket av följande två returvägar som tas.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direktleverans av en Azure Blockchain Workbench i förväntat format

![](./media/integration-patterns/direct-delivery.png)

I den här modellen sker kommunikationen till kontraktet och efterföljande tillståndsändringar efter den tidigare processen där -

-   När du har slutfört eller en specifik milstolpe i körningen av extern kod skickas en händelse till servicebussen som är ansluten till Azure Blockchain Workbench.

-   För system som inte kan anpassas direkt för att skriva ett meddelande som överensstämmer med förväntningarna på API: et omvandlas det.

-   Innehållet i meddelandet paketerars och skickas till en specifik funktion på det smarta kontraktet. Den här leveransen görs på uppdrag av den användare som är associerad med det externa systemet.

-   Funktionen körs och ändrar vanligtvis tillståndet. Ändringen av tillståndet flyttar framåt affärsarbetsflödet som återspeglas i det smarta kontraktet, vilket gör att andra funktioner nu kan köras efter behov.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett format som är okänt för Azure Blockchain Workbench

![Okänt meddelandeformat](./media/integration-patterns/unknown-message-format.png)

I den här modellen där ett meddelande i ett standardformat inte kan skickas direkt sker kommunikationen till kontraktet och efterföljande tillståndsändring efter den föregående processen där:

1.  När du har slutfört eller en specifik milstolpe i körningen av extern kod skickas en händelse till servicebussen som är ansluten till Azure Blockchain Workbench.
2.  En Logik App eller anpassad kod används för att ta emot det meddelandet och omvandla det till en standard Azure Blockchain Workbench formaterat meddelande.
3.  Logikappen skickar det transformerade meddelandet direkt till servicebussen.
4.  Azure Blockchain Workbench prenumererar på händelser från servicebussen och hämtar meddelandet.
5.  Azure Blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till ett visst kontrakt.
6. Innehållet i meddelandet paketerars och skickas till en specifik funktion på det smarta kontraktet. Den här leveransen görs på uppdrag av den användare som är associerad med det externa systemet.
7.  Funktionen körs och ändrar vanligtvis tillståndet. Ändringen av tillståndet flyttar framåt affärsarbetsflödet som återspeglas i det smarta kontraktet, vilket gör att andra funktioner nu kan köras efter behov.

## <a name="iot-integration"></a>IoT-integrering

Ett vanligt integrationsscenario är införandet av telemetridata som hämtas från sensorer i ett smart kontrakt. Baserat på data som levereras av sensorer kan smarta kontrakt vidta välgrundade åtgärder och ändra kontraktets tillstånd.

Till exempel, om en lastbil som levererar läkemedel hade sin temperatur skjuta i höjden till 110 grader, det kan påverka effektiviteten av läkemedlet och kan orsaka en allmän säkerhet fråga om inte upptäcks och tas bort från leveranskedjan. Om en förare accelererade sin bil till 100 miles per timme, kan den resulterande sensorinformation utlösa en annullering av försäkring av sin försäkringsgivare. Om bilen var en hyrbil, GPS-data kan tyda på när föraren gick utanför en geografi som omfattas av deras hyresavtal och ta ut en straffavgift.

Utmaningen är att dessa sensorer kan leverera data på en konstant basis och det är inte lämpligt att skicka alla dessa data till ett smart kontrakt. En typisk metod är att begränsa antalet meddelanden som skickas till blockchain samtidigt leverera alla meddelanden till en sekundär butik. Leverera till exempel meddelanden som tas emot med endast ett fast intervall, till exempel en gång per timme, och när ett inneslutet värde faller utanför ett överenskommet intervall för ett smart kontrakt. Kontrollera värden som faller utanför toleranser, säkerställer att data som är relevanta för kontrakt affärslogiken tas emot och körs. Kontrollera värdet med intervallet bekräftar att sensorn fortfarande rapporterar. Alla data skickas till ett sekundärt rapporteringslager för att möjliggöra bredare rapportering, analys och maskininlärning. Till exempel, medan få sensor avläsningar för GPS kanske inte krävs varje minut för ett smart kontrakt, kan de ge intressanta data som ska användas i rapporter eller kartläggning rutter.

På Azure-plattformen sker integrering med enheter vanligtvis med IoT Hub. IoT Hub tillhandahåller routning av meddelanden baserat på innehåll och aktiverar den typ av funktioner som beskrivits tidigare.

![IoT-meddelanden](./media/integration-patterns/iot.png)

Processen visar ett mönster:

-   En enhet kommunicerar direkt eller via en fältgateway till IoT Hub.
-   IoT Hub tar emot meddelandena och utvärderar meddelandena mot vägar som upprättats som kontrollerar innehållet i meddelandet, till exempel. *Rapporterar sensorn en temperatur som är högre än 50 grader?*
-   IoT Hub skickar meddelanden som uppfyller kriterierna till en definierad servicebuss för rutten.
-   En Logikapp eller annan kod lyssnar på servicebussen som IoT Hub har upprättat för rutten.
-   Logikappen eller annan kod hämtar och omvandlar meddelandet till ett känt format.
-   Det transformerade meddelandet, som nu är i standardformat, skickas till Service Bus för Azure Blockchain Workbench.
-   Azure Blockchain Workbench prenumererar på händelser från servicebussen och hämtar meddelandet.
-   Azure Blockchain Workbench initierar ett anrop till redovisningen och skickar data från det externa systemet till ett visst kontrakt.
-   När meddelandet har mottagits utvärderar kontraktet data och kan ändra tillståndet baserat på resultatet av utvärderingen, till exempel för en hög temperatur, ändra tillståndet till *Out of Compliance*.

## <a name="data-integration"></a>Dataintegrering

Förutom REST och meddelandebaserat API ger Azure Blockchain Workbench också åtkomst till en SQL DB som fylls med program- och kontraktsmetadata samt transaktionsdata från distribuerade liggare.

![Dataintegrering](./media/integration-patterns/data-integration.png)

Dataintegrationen är välkänd:

-   Azure Blockchain Workbench lagrar metadata om program, arbetsflöden, kontrakt och transaktioner som en del av sitt normala driftbeteende.
-   Externa system eller verktyg tillhandahåller en eller flera dialogrutor för att underlätta insamlingen av information om databasen, till exempel databasservernamn, databasnamn, typ av autentisering, inloggningsuppgifter och vilka databasvyer som ska utnyttjas.
-   Frågor skrivs mot SQL-databasvyer för att underlätta nedströmsförbrukning av externa system, tjänster, rapportering, utvecklarverktyg och företagets produktivitetsverktyg.

## <a name="storage-integration"></a>Integrering av lagring

Många scenarier kan kräva behov av att införliva intygbara filer. Av flera skäl är det olämpligt att lägga filer på en blockchain. I stället är en vanlig metod att utföra en kryptografisk hash (till exempel SHA-256) mot en fil och dela hash som hash på en distribuerad liggare. Utföra hash igen när som helst i framtiden bör returnera samma resultat. Om filen ändras, även om bara en pixel ändras i en bild, returnerar hash-värdet ett annat värde.

![Integrering av lagring](./media/integration-patterns/storage-integration.png)

Mönstret kan implementeras där:

-   Ett externt system beständiga en fil i en lagringsmekanism, till exempel Azure Storage.
-   En hash genereras med filen eller filen och tillhörande metadata, till exempel en identifierare för ägaren, url:en där filen finns osv.
-   Hash och eventuella metadata skickas till en funktion på ett smart kontrakt, till exempel *FileAdded*
-   I framtiden kan filen och metadata hasheras igen och jämföras med de värden som lagras i redovisningen.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Förutsättningar för att implementera integrationsmönster med hjälp av REST- och meddelande-API:er

För att underlätta möjligheten för ett externt system eller en annan enhet att interagera med det smarta kontraktet med hjälp av antingen REST- eller meddelande-API:et måste följande inträffa -

1. I Azure Active Directory för konsortiet skapas ett konto som representerar det externa systemet eller enheten.
2. Ett eller flera lämpliga smarta kontrakt för ditt Azure Blockchain Workbench-program har funktioner definierade för att acceptera händelserna från ditt externa system eller din externa enhet.
3. Programkonfigurationsfilen för ditt smarta kontrakt innehåller rollen, som systemet eller enheten har tilldelats.
4. Programkonfigurationsfilen för ditt smarta kontrakt identifierar i vilka tillstånd den här funktionen anropas av den definierade rollen.
5. Programkonfigurationsfilen och dess smarta kontrakt överförs till Azure Blockchain Workbench.

När programmet har överförts tilldelas Azure Active Directory-kontot för det externa systemet kontraktet och den associerade rollen.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testa externa systemintegrationsflöden innan du skriver integrationskod 

Att integrera med externa system är ett viktigt krav i många scenarier. Det är önskvärt att kunna validera smart kontraktsdesign före eller parallellt med utvecklingen av kod för att integrera med externa system.

Användningen av Azure Active Directory (Azure AD) kan avsevärt påskynda utvecklare produktivitet och tid till värde. Specifikt kan kodintegrationen med ett externt system ta en icke-trivial tid. Genom att använda Azure AD och automatisk generering av UX av Azure Blockchain Workbench kan du tillåta utvecklare att logga in på Blockchain Workbench som det externa systemet och fylla värden från det externa systemet via UX. Du kan snabbt utveckla och validera idéer i en proof of concept-miljö innan integrationskoden skrivs för de externa systemen.
