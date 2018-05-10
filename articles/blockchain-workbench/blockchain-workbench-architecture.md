---
title: Azure Blockchain arbetsstationen-arkitektur
description: Översikt över Azure Blockchain arbetsstationen arkitektur och dess komponenter.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: a6c415123a8e1254e214af69711fc4143a929a58
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain arbetsstationen-arkitektur

Azure Blockchain arbetsstationen förenklar blockchain programutveckling genom att tillhandahålla en lösning som använder flera Azure-komponenter. Blockchain arbetsstationen kan distribueras med hjälp av en lösningsmall i Azure Marketplace. Mallen kan du välja moduler och komponenter för att distribuera med Blockchain Workbench, till exempel blockchain stacken, typ av klientprogram, samt stöd för IoT-integration. När har distribuerats, ger Blockchain arbetsstationen åtkomst till en webbapp, iOS-app och Android-app.

![Arkitektur för Blockchain arbetsstationen](media/blockchain-workbench-architecture/architecture.png)

## <a name="identity-and-authentication"></a>Enhetsidentitet samt autentisering

Använder Blockchain arbetsstationen, federera en consortium sina enterprise identiteter med Azure Active Directory (AD Azure). Arbetsstationen skapar nya användarkonton för-kedjan identiteter med företagsidentiteter som lagras i Azure AD. Identitetsmappning underlättar autentiserade inloggningen till klientens API: er och program och använder principer för autentisering av organisationer. Arbetsstationen ger också möjlighet att koppla företagsidentiteter till specifika roller inom ett visst smart kontrakt. Dessutom arbetsstationen innehåller också en mekanism för att identifiera åtgärderna som rollerna kan ta och vid vilken tidpunkt.

När Blockchain arbetsstationen har distribuerats kan samverkar användarna med Blockchain arbetsstationen antingen via klientprogram, REST-baserad klient-API eller Messaging API. I samtliga fall måste interaktioner autentiseras, antingen via Azure Active Directory (AD Azure) eller enhetsspecifika autentiseringsuppgifter.

Användare federera sina identiteter för consortium Azure AD genom att skicka ett e-postinbjudan till deltagare till sina e-postadress. När du loggar in autentiseras dessa användare med hjälp av namnet, lösenord och principer. Till exempel tvåfaktorsautentisering i organisationen.

Azure AD används för att hantera alla användare som har åtkomst till Blockchain arbetsstationen. Varje enhet som ansluter till ett smart kontrakt är även associerat med Azure AD.

Azure AD används också för att tilldela användare till en särskild administratörsgruppen. Användare som tillhör administratörsgruppen få rättigheter/administratörsåtgärder Blockchain arbetsstationen, till exempel distribuera kontrakt och ger behörighet till en användare åtkomst till ett kontrakt. Användare utanför den här gruppen har inte åtkomst till administratörsåtgärder.

## <a name="client-applications"></a>Klientprogram

Arbetsstationen innehåller automatiskt genererade klientprogram för webb- och mobile (iOS, Android) som kan användas för att validera, testa och visa blockchain program. Programgränssnittet genereras dynamiskt baserat på metadata för smart kontraktet och kan ta några användningsfall. Klientprogrammen leverera en användarinriktad klientdel till fullständig blockchain-appar som genereras av Blockchain arbetsstationen. Klientprogram autentisera användare via Azure Active Directory (Azure AD) och sedan presentera en användarupplevelse som är skräddarsydda för business kontext för smart kontraktet. Användarupplevelsen kan skapa nya smarta kontraktet instanser av behöriga personer och visar sedan möjligheten att utföra vissa typer av transaktioner på lämpliga platser i affärsprocess smart kontraktet representerar.

Auktoriserade användare kommer åt administratörskonsolen i webbprogrammet. Konsolen är tillgängliga för användare i gruppen Administratörer i Azure AD och ger åtkomst till följande funktioner:

* Distribuera Microsoft tillhandahåller smarta kontrakt för vanliga scenarier. Till exempel en tillgång överföring scenario.
* Överför och distribuera sina egna smarta kontrakt.
* Tilldela en användaråtkomst till smart kontrakt i kontexten för en viss roll.

## <a name="gateway-service-api"></a>Gateway service API

Blockchain arbetsstationen innehåller ett REST-baserad gateway service API. Vid skrivning till en blockchain, API: et genererar och skickar meddelanden till en händelse broker. När data begärs av API: n, skicka frågor till SQL-databasen av kedjan. SQL-databasen innehåller en replik på kedjan data och metadata som tillhandahåller information om kontexten och konfiguration för smart kontrakt som stöds. Frågor returnera nödvändiga data från repliken av kedjan i ett format som känner av metadata för kontraktet.

Utvecklare kan komma åt API för gateway-tjänsten för att bygga eller integrera blockchain lösningar utan Blockchain arbetsstationen klientprogram.

> [!NOTE]
> Om du vill aktivera autentiserad åtkomst till API är två klientprogram registrerade i Azure Active Directory. Azure Active Directory kräver olika program registreringar varje programtyp (interna och). 

## <a name="message-broker-for-incoming-messages"></a>Meddelandet broker för inkommande meddelanden

Utvecklare som vill skicka meddelanden direkt till Blockchain arbetsstationen kan skicka meddelanden direkt till Service Bus. Till exempel kan meddelanden API användas för system till integrering eller IoT-enheter.

## <a name="message-broker-for-downstream-consumers"></a>Meddelandet broker för underordnade konsumenter

Händelser inträffar under livscykeln för programmet. Händelser kan utlösas av Gateway-API: et eller på redovisningen. Händelsemeddelanden kan initiera underordnade kod baserat på händelsen.

Blockchain arbetsstationen distribuerar automatiskt två typer av händelsekonsumenter. En utlöses av blockchain händelser att fylla i SQL-Arkiv av kedjan. Den andra är att hämta metadata för händelser som genereras av API: n för överföring och lagring av dokument.

## <a name="message-consumers"></a>Meddelandet konsumenter

 Meddelandet konsumenter ta meddelanden från Service Bus. Den underliggande eventing modellen för meddelande konsumenterna kan tillägg av ytterligare tjänster och system. Exempelvis kan du lägga till stöd för att fylla i CosmosDB eller utvärdera meddelanden med Azure Streaming Analytics. I följande avsnitt beskrivs konsumenterna meddelandet ingår i Blockchain arbetsstationen.

### <a name="distributed-ledger-consumer"></a>Distribuerade redovisning konsumenten

Distribuerade ledger-teknik (Distributed Link Tracking) meddelanden innehåller metadata för transaktioner som ska skrivas till blockchain. Konsumenten hämtar meddelanden och skickar data till en transaktion builder, Signerare och routern.

### <a name="database-consumer"></a>Databasen konsumenten

Databasen konsumenten tar meddelanden från Service Bus och skickar data till en ansluten databas, till exempel SQL-databas.

### <a name="storage-consumer"></a>Storage-consumer

Lagring konsumenten tar meddelanden från Service Bus och skickar data till en direktansluten lagring. Till exempel lagra hashformaterats dokument i Azure Storage.

## <a name="transaction-builder-and-signer"></a>Transaktionen builder och undertecknare

Om ett meddelande som inkommande message service broker behöver skrivas till blockchain, kommer att behandlas av Distributed Link Tracking konsumenten. Distributed Link Tracking konsumenten är en tjänst som hämtar det meddelande som innehåller metadata för en önskad transaktion att köra och skickar information till den *transaktion builder och Signerare*. Den *transaktion builder och Signerare* monterar en blockchain transaktion baserat på data och önskade blockchain mål. När klar är transaktionen signerad. Privata nycklar lagras i Azure Key Vault.

 Blockchain arbetsstation hämtar rätt privat nyckel från Nyckelvalvet och loggar transaktionen utanför Key Vault. När du har loggat, skickas transaktionen till transaktion routrar och redovisning.

## <a name="transaction-routers-and-ledgers"></a>Transaktionen routrar och redovisning

Transaktionen routrar och Kundreskontra ta signerade transaktioner och vidarebefordra dem till lämpliga blockchain. För närvarande stöder Blockchain arbetsstationen Ethereum som blockchain dess mål.

## <a name="dlt-watcher"></a>Distributed Link Tracking watcher

En distribuerad ledger-teknik (Distributed Link Tracking) watcher övervakar händelser som inträffar i block kedjor bifogas Blockchain arbetsstationen.
Händelser visas information som är relevanta för enskilda användare och datorer. Till exempel skapa nya instanser av kontrakt, körning av transaktioner och ändringar av tillstånd. Händelser skapas och skickas till utgående message service broker, så att de kan användas av underordnade konsumenter.

Till exempel SQL konsumenten övervakar händelser, förbrukar dem och fyller SQL-databas med med värden. Kopian kan återskapning av en replik av-kedjan data i en av kedjan store.

## <a name="azure-sql-database"></a>Azure SQL-databas

Azure SQL-databas som är kopplade till Blockchain arbetsstationen lagrar kontraktet definitioner, konfigurationsmetadata och en SQL-tillgänglig replik av data som lagras i blockchain. Dessa data kan enkelt efterfrågas, visualiseras eller analyseras av direkt åtkomst till databasen. Utvecklare och andra användare kan använda databasen för rapportering, analytics eller andra datacentrerad integreringar. Användare kan till exempel visualisera transaktionsdata med Power BI.

Lagringen av kedjan gör möjligheten för företag att fråga efter data i SQL i stället för en blockchain redovisning. Genom att standardisera på en standard-schemat som är oberoende av blockchain teknik stackar, gör lagringen av kedjan också återanvändning av rapporter och andra artefakter över projekt, scenarier och organisationer.

## <a name="azure-storage"></a>Azure Storage

Azure Storage används för att lagra kontrakt och metadata som associeras med kontrakt.

Spela upp en roll i många scenarier för blockchain till Central från inköps- och fraktsedlar till bilder som används i Nyheter och medicinska bilder visar färgerna att videon har sitt ursprung i en serie inklusive polis brödtext kameror och större rörelse bilder dokument. Dokument är inte lämpligt att placera direkt på blockchain.

Blockchain arbetsstationen stöder möjligheten att lägga till dokument eller andra medieinnehåll med blockchain affärslogik. En hash av dokumentet eller media innehåll lagras i blockchain och faktiska dokumentet eller medieinnehåll lagras i Azure Storage. Den associerade transaktionsinformationen är levereras till inkommande message service broker, paketeras, signerade och dirigeras till blockchain. Den här processen startar händelser som delas via utgående message service broker. SQL DB använder den här informationen och skickar den till DB för senare frågor. Underordnade system kan också använda dessa händelser ska fungera efter behov.

## <a name="monitoring"></a>Övervakning

Arbetsstationen ger programloggning med hjälp av Application Insights och Azure-Monitor. Application Insights används för att lagra alla loggade information från Blockchain arbetsstationen och innehåller fel, varningar och lyckade åtgärder. Application Insights kan användas av utvecklare kan felsöka problem med Blockchain arbetsstationen. 

Azure-Monitor innehåller information om hälsotillståndet för blockchain nätverket. 

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure Blockchain arbetsstationen](blockchain-workbench-deploy.md)