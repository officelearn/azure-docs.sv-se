---
title: Kontraktet integration mönster i Azure Blockchain arbetsstationen för smartkort
description: Översikt över smart kontraktet integration mönster i Azure Blockchain arbetsstationen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: a6a44e30fe58617b43c5491a72fc882015bc9591
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886071"
---
# <a name="smart-contract-integration-patterns"></a>Smart kontraktet integration mönster

Smart kontrakt representerar ofta ett företagsarbetsflöde som behöver integreras med externa system och enheter.

Krav för de här arbetsflödena innehåller behöver initiera transaktioner på en distribuerad redovisning som innehåller data från ett externt system, en tjänst eller en enhet. De måste också ha externa system som reagerar på händelser från smart kontrakt i en distribuerad redovisning.

Ger möjlighet att både skicka transaktioner från externa system till smart kontrakt som ingår i ett program för Azure Blockchain arbetsstationen, samt skicka händelsemeddelanden till externa system baserat på ändringar som ta REST-API och meddelanden integrering Placera i ett program.

För data integrationsscenarier innehåller Azure Blockchain arbetsstationen en uppsättning databasvyer som koppla en kombination av transaktionsdata från blockchain och metadata om program och smarta kontrakt.

Dessutom kan vissa scenarier, till exempel de som rör ange kedja eller media, också kräver integreringen av dokument. Medan Azure Blockchain arbetsstationen inte innehåller API-anrop för att hantera dokument direkt, kan dokument införlivas i ett Azure Blockchain program. Det här avsnittet innehåller också mönstret.

Det här avsnittet innehåller de mönster som identifierats för de olika typerna av integreringar inom slutpunkt till slutpunkt-lösningar.

## <a name="rest-api-based-integration"></a>REST API-baserade integrering

Funktioner i Azure Blockchain arbetsstationen genereras webbprogrammet exponeras via REST API. Detta inkluderar Azure Blockchain arbetsstationen överför, konfiguration och administration av program, skicka transaktioner till en distribuerad redovisning och hämtning av metadata och redovisning programdata.

REST-API används främst för interaktiva klienter, till exempel webb, mobil-, och bot program.

Det här avsnittet tittar på mönster som fokuserar på de aspekter av REST-API som skicka transaktioner till en distribuerad redovisning och de som frågar efter data om transaktioner från Azure Blockchain arbetsstationen *av kedjan* SQL-databas.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Skicka transaktioner till en distribuerad redovisning från ett externt system

Azure Blockchain arbetsstationen REST API ger dig möjlighet att skicka autentiserade begäranden kan köra transaktioner på en distribuerad redovisning.

![Skicka transaktioner till en distribuerad redovisning](media/blockchain-workbench-integration-patterns/send-transactions-ledger.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   Det externa programmet autentiserar till Azure Active Directory etablerats som en del av distributionen av Azure Blockchain arbetsstationen.
-   Auktoriserade användare få en ägartoken som kan skickas med begäranden till API: et.
-   Externa program gör anrop till REST-API med ägartoken.
-   REST API-paket på begäran som ett meddelande och skickar det till Service Bus. Härifrån kommer den att hämtas, signerade och skickas till lämplig distribuerade redovisning.
-   REST-API: gör en begäran till Azure Blockchain arbetsstationen SQL-databas att registrera begäran och fastställa den aktuella Etableringsstatus.
-   SQL-databas returnerar Etableringsstatus och API-anrop returnerar ID för det externa programmet som kallas den.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Fråga efter Blockchain arbetsstationen metadata och distribuerade redovisningstransaktioner

Azure Blockchain arbetsstationen REST API ger dig möjlighet att skicka autentiserade begäranden till frågan information som rör smart kontraktet körning i en distribuerad redovisning.

![Frågar metadata](media/blockchain-workbench-integration-patterns/querying-metadata.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

1. Det externa programmet autentiserar till Azure Active Directory etablerats som en del av distributionen av Azure Blockchain arbetsstationen.
2. Auktoriserade användare få en ägartoken som kan skickas med begäranden till API: et.
3. Externa program gör anrop till REST-API med ägartoken.
4. REST-API frågar efter data för begäran från SQL-databas och returneras till klienten.

## <a name="messaging-integration"></a>Integrering med meddelanden

Messaging integration underlättar interaktion med system, tjänster och enheter där interaktiv inloggning inte är möjligt eller önskvärt. Meddelanden integration fokuserar på två typer av meddelanden som begär att transaktioner köras på en distribuerad redovisning och händelser som visas av att när transaktioner har ägt rum.

Meddelanden integration fokuserar på körningen och övervakning av transaktioner för användare skapas, kontrakt och körningen av transaktioner på kontrakt och används främst av *fjärradministrerade* serversystem.

Det här avsnittet tittar på mönster som fokuserar på de aspekter av meddelande-baserad API som skickar transaktioner till en distribuerad redovisning och de som representerar händelsemeddelanden som exponeras av den underliggande distribuerade redovisningen.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Enkelriktade händelse leverans från ett smart kontrakt till en händelsekonsument 

I det här scenariot kan inträffar en händelse inom ett smart kontrakt, till exempel en tillståndsändring eller körningen av en viss typ av transaktionen. Den här händelsen skickas via en händelse rutnätet underordnade konsumenter och konsumenterna och vidta lämpliga åtgärder.

Ett exempel på det här scenariot är att en konsument skulle bli aviserad och kan vidta åtgärder, till exempel spela in informationen i en SQL-databas eller tjänsten gemensamma Data när en transaktion inträffar. Det här är samma mönster som arbetsstationen följer för att fylla sin *av kedjan* SQL-databas.

En annan skulle vara om ett smart kontrakt övergår till ett visst tillstånd, till exempel när ett kontrakt försätts i ett *OutOfCompliance*. När den här tillståndsändring sker utlösa den en avisering ska skickas till en administratör mobiltelefon.

![Enkelriktade händelse leverans](media/blockchain-workbench-integration-patterns/one-way-event-delivery.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   Smart kontraktet övergår till ett nytt tillstånd och skickar en händelse till redovisningen.
-   Redovisningen tar emot och levererar händelsen till Azure Blockchain arbetsstationen.
-   Azure Blockchain arbetsstationen som prenumererar på händelser från redovisningen och tar emot händelsen.
-   Azure Blockchain arbetsstationen publicerar händelsen till prenumeranter i rutnätet händelse.
-   Externa system prenumererar på händelsen rutnätet, använda meddelandet och vidta lämpliga åtgärder.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Enkelriktade händelse leverans av ett meddelande från ett externt system till ett smart kontrakt

Det finns också ett scenario som flödar från motsatt riktning. I det här fallet en händelse genereras av en sensor eller ett externt system och data från att händelsen ska skickas till ett smart kontrakt.

Ett vanligt exempel är överföringen av data från finansiella marknader, till exempel priser varor, börs eller obligationer, till ett smart kontrakt.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkt leverans av en Azure Blockchain arbetsstationen i det förväntade formatet

Vissa program har byggts för att integrera med Azure Blockchain arbetsstationen och direkt genererar och skicka meddelanden i förväntat format.

![Direkt leverans](media/blockchain-workbench-integration-patterns/direct-delivery.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   En händelse inträffar i ett externt system som utlöser skapandet av ett meddelande för Azure Blockchain arbetsstationen.
-   Det externa systemet har kod som skrivs för att skapa det här meddelandet i ett format som är kända och skickar det direkt till Service Bus.
-   Azure Blockchain arbetsstationen som prenumererar på händelser från Service Bus och hämtar meddelandet.
-   Azure Blockchain arbetsstationen initierar ett anrop till redovisning, skicka data från det externa systemet till ett visst kontrakt.
-   Kontraktet övergår till ett nytt tillstånd vid mottagning av meddelandet.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett okänt format till Azure Blockchain arbetsstationen

Vissa datorer kan inte ändras för att leverera meddelanden i standardformat som används av Azure Blockchain arbetsstationen. I dessa fall måste befintliga mekanismer meddelande användas ofta format från dessa system. Mer specifikt internt meddelandetyper av dessa system kan du omvandla använder Logic Apps, Azure Functions eller andra anpassade kod för att mappa till en standard messaging format förväntades.

![Okänt meddelandeformat](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

Detta sker med hjälp av den process som beskrivs ovan, där:

-   En händelse inträffar i ett externt system som utlöser skapandet av ett meddelande.
-   En Logikapp eller anpassad kod används för att ta emot meddelandet och omvandla dem till ett standardmeddelande för Azure Blockchain arbetsstationen formaterad.
-   Logikappen skickar omvandlade meddelandet direkt till Service Bus.
-   Azure Blockchain arbetsstationen som prenumererar på händelser från Service Bus och hämtar meddelandet.
-   Azure Blockchain arbetsstationen initierar ett anrop till redovisning, skicka data från det externa systemet till en specifik funktion i kontraktet.
-   Funktionen Kör och vanligtvis ändrar tillståndet. Ändring av tillstånd flyttas framåt företagsarbetsflöde återspeglas i smart kontraktet, aktivera andra funktioner som nu ska utföras efter behov.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Övergång kontroll till en extern bearbeta och await slutförande

Det finns scenarier där ett smart kontrakt måste stoppa intern körning och leverera till en extern process. Att slutförs sedan extern process, fortsätter skicka ett meddelande till smart kontraktet och körningen sedan i smart kontraktet.

#### <a name="transition-to-the-external-process"></a>Övergång till extern process

Det här mönstret är vanligtvis implementeras med hjälp av följande metod:

-   Smart kontraktet övergår till ett visst tillstånd. I det här tillståndet antingen Nej eller ett begränsat antal funktioner kan köras förrän ett externt system tar en önskad åtgärd.
-   Ändring av tillstånd har angetts som en händelse till en underordnad konsumenten.
-   Underordnade konsumenten tar emot händelsen och utlöser extern kod.

![Kontroll av övergången till extern process](media/blockchain-workbench-integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Returnera kontroll från smart kontraktet

Beroende på möjlighet att anpassa det externa systemet kan eller eventuellt inte att leverera meddelanden i ett standardformat som Azure Blockchain arbetsstationen förväntas. Baserat på externa system möjligheten avgör att skapa något av dessa meddelanden vilka av följande två returnerade sökvägar som ska vidtas.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Direkt leverans av en Azure Blockchain arbetsstationen i det förväntade formatet

![](media/blockchain-workbench-integration-patterns/direct-delivery.png)

I den här modellen kommunikation till kontraktet och efterföljande tillståndsändring sker följande ovanstående bearbeta where -

-   Vid nå slutförs eller en viss milstolpe extern kod körs, skickas en händelse till Service Bus ansluten till Azure Blockchain arbetsstationen.

-   För system som kan anpassas direkt för att skriva ett meddelande som överensstämmer med till API: et förväntningar omvandlas den.

-   Innehållet i meddelandet är paketerat och skickas till en specifik funktion på smart kontraktet. Detta görs för användarens räkning som är kopplad till det externa systemet.

-   Funktionen Kör och normalt kommer att ändra tillståndet. Ändring av tillstånd flyttas framåt företagsarbetsflöde återspeglas i smart kontraktet, aktivera andra funktioner som nu ska utföras efter behov.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Leverans av ett meddelande i ett okänt format till Azure Blockchain arbetsstationen

![Okänt meddelandeformat](media/blockchain-workbench-integration-patterns/unknown-message-format.png)

I den här modellen där i ett standardformat går inte att skicka ett meddelande direkt, kommunikation till kontraktet och efterföljande tillståndsändring sker följande ovanstående bearbeta var:

1.  Vid nå slutförs eller en viss milstolpe extern kod körs, skickas en händelse till Service Bus ansluten till Azure Blockchain arbetsstationen.
2.  En Logikapp eller anpassad kod används för att ta emot meddelandet och omvandla dem till ett standardmeddelande för Azure Blockchain arbetsstationen formaterad.
3.  Logikappen skickar omvandlade meddelandet direkt till Service Bus.
4.  Azure Blockchain arbetsstationen som prenumererar på händelser från Service Bus och hämtar meddelandet.
5.  Azure Blockchain arbetsstationen initierar ett anrop till redovisning, skicka data från det externa systemet till ett visst kontrakt.
6. Innehållet i meddelandet är paketerat och skickas till en specifik funktion på smart kontraktet. Detta görs för användarens räkning som är kopplad till det externa systemet.
7.  Funktionen Kör och normalt kommer att ändra tillståndet. Ändring av tillstånd flyttas framåt företagsarbetsflöde återspeglas i smart kontraktet, aktivera andra funktioner som nu ska utföras efter behov.

## <a name="iot-integration"></a>IoT-integrering

Ett vanligt scenario för integrering är inkludering av telemetridata som hämtats från sensorer i ett smart kontrakt. Baserat på data som levereras av sensorer kunde smart kontrakt välgrundade åtgärda och ändra tillståndet för kontraktet.

Till exempel om en lastbil leverera medicinska hade temperaturen Sväva till 110 grader, det kan påverka effektiviteten hos medicinska och kan orsaka ett problem med allmän säkerhet om inte identifierat och tas bort från leveranskedjan. Om en drivrutin snabbare sin bil till 100 miles per timme, utlösa resulterande sensorinformation insurance av sin insurance providern avbryts. Om bilen var en bil, gick GPS-data anger när drivrutinen gick utanför en geografisk plats som omfattas av sin uthyrning avtal och debiterar ett påföljder.

Utmaningen är att dessa sensorer som kan leverera data på en konstant basis och det är inte lämpligt att skicka alla dessa data till ett smart kontrakt. En vanlig metod är att begränsa antalet meddelanden som skickas till blockchain leverera alla meddelanden till en sekundär butik. Till exempel leverera meddelanden som tagits emot efter att endast fast intervall, till exempel en gång i timmen och en innesluten värdet ligger utanför en överenskommen på intervallet för ett smart kontrakt. Kontrollerar värden som ligger utanför toleranser garanterar att data som är relevanta för affärslogik kontrakt tas emot och körs. Kontrollera värdet vid intervallet som bekräftar att fortfarande rapporterar. Alla data skickas till en sekundär reporting store för att aktivera bredare rapportering, analyser och maskininlärning. Vid hämtning av sensoravläsningar för GPS inte kanske är nödvändiga varje minut för ett smart kontrakt, kan de ger intressanta data som ska användas i rapporter eller mappning vägar.

På Azure-plattformen görs integrering med enheter vanligtvis med IoT-hubben. IoT-hubb ger dig möjlighet att skicka meddelanden baserat på innehåll och gör typ av funktion som beskrivs ovan.

![IoT-meddelanden](media/blockchain-workbench-integration-patterns/iot.png)

Processen ovan visar ett mönster för detta implementeras:

-   En enhet kommunicerar direkt eller via en gateway för fältet för IoT-hubb.
-   IoT-hubb som tar emot meddelanden och utvärderar meddelanden mot vägar upprätta som till exempel kontrollera innehållet i meddelandet. *Rapporterar sensorn en temperatur som är större än 50 grader?*
-   IoT-hubben skickar meddelanden som uppfyller villkoren för att en definierad Service Bus för vägen.
-   En Logikapp eller annan kod lyssnar på Service Bus som har upprättat IoT-hubb för vägen.
-   Logikappen eller annan kod hämtar och transformera meddelandet till ett känt format.
-   Transformerade meddelandet nu skickas i ett standardformat till Service Bus för Azure Blockchain arbetsstationen.
-   Azure Blockchain arbetsstationen som prenumererar på händelser från Service Bus och hämtar meddelandet.
-   Azure Blockchain arbetsstationen initierar ett anrop till redovisning, skicka data från det externa systemet till ett visst kontrakt.
-   Vid mottagning av meddelandet kontraktet utvärderar data och kan ändra status baserat på resultatet av den bedömningen, till exempel för hög temperatur, ändrar du läget till *efterlevnad av*.

## <a name="data-integration"></a>Dataintegrering

Förutom REST och message-baserad API tillhandahåller Azure Blockchain arbetsstationen också åtkomst till en SQL-databas med programmet och kontrakt metadata samt transaktionsdata från distribuerade redovisning.

![Dataintegrering](media/blockchain-workbench-integration-patterns/data-integration.png)

Dataintegrering är känt:

-   Azure Blockchain arbetsstationen lagrar metadata om program, arbetsflöden, kontrakt och transaktioner som en del av normal drift.
-   Ange en eller flera dialogrutor för att underlätta insamling av information om databasen, till exempel Databasservernamnet, databasens namn, typ av autentisering, inloggningsuppgifter och vilken databas som visar för att använda externa system eller verktyg.
-   Frågor skrivs mot SQL-databasvyer för att underlätta underordnade förbrukning av externa system, tjänster, reporting, utvecklingsverktyg och enterprise produktivitetsverktyg.

## <a name="storage-integration"></a>Lagringsintegrering

Många scenarier kan kräva behovet av att tillämpa attestable filer. Av flera skäl blir det olämpligt att skicka filer till en blockchain. I stället är det vanligaste sättet att utföra en kryptografisk hash (till exempel SHA-256) mot en fil och dela den hashvärdet för en distribuerad redovisning. Utför hash när som helst framtida ska returnera samma resultat. Om filen ändras även om bara en bildpunkt har ändrats i en bild, returnerar hash-algoritmen ett annat värde.

![Lagringsintegrering](media/blockchain-workbench-integration-patterns/storage-integration.png)

Mönstret kan implementeras där:

-   Ett externt system kvarstår en fil i en mekanism för lagring, till exempel Azure Storage.
-   Ett hash-värde som genereras med filen eller filen och associerade metadata, till exempel en identifierare för ägaren URL där filen finns, osv.
-   Hash-algoritmen och eventuella metadata skickas till en funktion på ett smart kontrakt som *FileAdded*
-   I framtiden, fil- och metadata hashas igen och jämförs med de värden som lagras på redovisningen.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Förutsättningar för att implementera integration mönster med hjälp av REST och meddelandet API: er

För att underlätta möjligheten för ett externt system eller en enhet kan interagera med smart kontrakt med hjälp av REST- eller meddelandet API, måste följande utföras-

1. I Azure Active Directory för consortium skapas ett konto som representerar det externa systemet eller enheten.
2. Lämplig smart kontraktet för tillämpningsprogrammet Azure Blockchain arbetsstationen har funktioner som är definierade för att acceptera händelser från det externa systemet eller enheten.
3. Programmets konfigurationsfil för smart kontraktet innehåller rollen som systemet eller enheten kommer att tilldelas.
4. Programmets konfigurationsfil för smart kontraktet identifierar i vilka tillstånd som den här funktionen kan anropas av den angivna rollen.
5. Programmets konfigurationsfil och dess smart kontrakt överförs till Azure Blockchain arbetsstationen.

När programmet har överförts har Azure Active Directory-konto för det externa systemet tilldelats kontraktet och tillhörande rolltjänster.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testa externt System Integration flöden innan du skriva kod för integrering 

Möjligheten att integrera med externa system är ett krav för många scenarier. Det är klokt att kunna verifiera smart kontraktet design tidigare eller parallellt med utvecklingen av kod för att integrera med externa system.

Användning av Azure Active Directory (Azure AD) kan avsevärt accelerera utvecklarproduktivitet och tid för värden. Mer specifikt kan kod integrering med ett externt system ta en icke-trivial tidsperiod. Med hjälp av Azure AD och den automatisk genereringen av UX genom Azure Blockchain arbetsstationen, så kan utvecklare att logga in på arbetsstationen som det externa systemet och fylla i förväntades från det externa systemet via UX. Detta ger snabbt utveckling och validering av idéer i ett bevis på koncept miljö antingen före eller parallellt med integration kod som skrivs för externa system.
