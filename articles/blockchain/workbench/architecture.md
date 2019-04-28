---
title: Azure Blockchain Workbench-arkitektur
description: Översikt över Azure Blockchain Workbench arkitektur och dess komponenter.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 83c5e1405c402a1c6c98f9dbcaaf74891eb75e6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437773"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench-arkitektur

Azure Blockchain Workbench förenklar utveckling av blockkedjor genom att tillhandahålla en lösning med flera Azure-komponenter. Blockchain Workbench kan distribueras med hjälp av en mall i Azure Marketplace. Mallen kan du välja moduler och komponenter för att distribuera inklusive blockchain stack, typ av klientprogrammet, och stöd för IoT-integrering. När distribuerats ger Blockchain Workbench åtkomst till en webbapp, iOS-app och Android-app.

![Blockchain Workbench-arkitektur](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Enhetsidentitet samt autentisering

Med Blockchain Workbench kan federera ett konsortium enterprise identiteter med Azure Active Directory (AD Azure). Workbench genererar nya användarkonton för identiteter i kedjan med företagsidentiteter som lagras i Azure AD. Identitetsmappning underlättar autentiserad inloggning till klientens API: er och program och använder principer för autentisering av organisationer. Workbench ger också möjlighet att koppla företagsidentiteter för specifika roller inom ett visst smarta kontrakt. Dessutom Workbench innehåller också en mekanism för att identifiera åtgärderna som de här rollerna kan ta och vilken tid.

När Blockchain Workbench har distribuerats kan samverkar användarna med Blockchain Workbench antingen via klientprogram, REST-baserad klient-API eller API för enhetsmeddelanden. I samtliga fall måste interaktioner autentiseras via Azure Active Directory (AD Azure) eller specifika autentiseringsuppgifter.

Användare federera deras identiteter ett konsortium Azure AD genom att skicka en e-postinbjudan till deltagare till sina e-postadress. När du loggar in autentiseras dessa användare med hjälp av namn, lösenord och principer. Till exempel tvåfaktorsautentisering i sina organisationer.

Azure AD används för att hantera alla användare som har åtkomst till Blockchain Workbench. Varje enhet som ansluter till ett smarta kontrakt är även associerat med Azure AD.

Azure AD används också för att tilldela användare till en särskild administratörsgruppen. Användare som tillhör administratörsgruppen beviljas åtkomst till rättigheter och åtgärder i Blockchain Workbench, inklusive distribution av kontrakt och ge behörigheter till en användare för att få åtkomst till ett avtal. Användare utanför den här gruppen har inte åtkomst till administrativa åtgärder.

## <a name="client-applications"></a>Klientprogram

Workbench ger automatiskt genererade klientprogram för webb och mobil (iOS och Android), som kan användas för att verifiera, testa och visa blockchain-program. Programgränssnittet genereras dynamiskt baserat på smarta kontrakt metadata och kan hantera alla användningsfall. Klientprogrammen leverera en användarinriktad klientdel till fullständig blockchain-program som genereras av Blockchain Workbench. Klientprogram autentisera användare via Azure Active Directory (Azure AD) och sedan presentera en användarupplevelse som anpassats efter affärskontexten av smarta kontrakt. Användarupplevelsen gör det möjligt att skapa nya smarta kontrakt instanser av behöriga personer och sedan visar möjligheten att utföra vissa typer av transaktioner på lämpliga platser i affärsprocessen smarta kontrakt representerar.

I webbprogrammet, behöriga användare kan komma åt administratörskonsolen. Konsolen är tillgänglig för användare i gruppen Administratörer i Azure AD och ger åtkomst till följande funktioner:

* Distribuera Microsoft tillhandahållna smarta kontrakt för populära scenarier. Till exempel en tillgång överföring scenario.
* Ladda upp och distribuera sina egna smarta kontrakt.
* Tilldela en användaråtkomst till det smarta kontrakten i kontexten för en viss roll.

Mer information finns i den [Azure Blockchain Workbench klienten exempelprogram på GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-development-kit/connect/mobile/blockchain-workbench/workbench-client).

## <a name="gateway-service-api"></a>API för gateway-tjänsten

Blockchain Workbench innehåller ett REST-baserad gateway-API. Vid skrivning till en blockchain, genererar API: et och levererar meddelanden till en asynkron meddelandekö för händelser. När data har begärts av API: et, skickas frågor till SQL-databas av kedjan. SQL-databasen innehåller en replik av data i kedjan och metadata som tillhandahåller information om kontext och konfiguration för stöds smarta kontrakt. Frågor returnera nödvändiga data från annan repliken i ett format som underrättat genom att metadata för kontraktet.

Utvecklare kan komma åt API för gateway-tjänsten för att skapa eller integrera blockchain-lösningar utan att behöva Blockchain Workbench klientappar.

> [!NOTE]
> Om du vill aktivera autentiserad åtkomst till API: et är två program registrerade i Azure Active Directory. Azure Active Directory kräver distinkta programregistreringar varje programtyp (interna och web). 

## <a name="message-broker-for-incoming-messages"></a>Asynkron meddelandekö för inkommande meddelanden

Utvecklare som vill skicka meddelanden direkt till Blockchain Workbench kan skicka meddelanden direkt till Service Bus. Till exempel kan meddelanden API användas för system till integration eller IoT-enheter.

## <a name="message-broker-for-downstream-consumers"></a>Asynkron meddelandekö för underordnade konsumenter

Under livscykeln för programmet inträffar händelser. Händelserna kan utlösas av Gateway-API: et eller på huvudboken. Händelsemeddelanden kan initiera underordnade kod baserat på händelsen.

Blockchain Workbench distribuerar automatiskt två typer av händelsekonsumenter. En consumer utlöses av blockchain händelser att fylla i en annan SQL-Arkiv. För andra konsumenter är att samla in metadata för händelser som genererats av API: et som rör överföring och lagring av dokument.

## <a name="message-consumers"></a>Meddelandekonsumenter

 Meddelandekonsumenter ta meddelanden från Service Bus. Den underliggande eventing modellen meddelandekonsumenter möjliggör tillägg av ytterligare tjänster och system. Du kan till exempel lägga till stöd för att fylla i CosmosDB eller utvärdera meddelanden med Azure Streaming Analytics. I följande avsnitt beskrivs meddelandekonsumenter som ingår i Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Distribuerat transaktionsregister konsument

Distribuerat transaktionsregister teknik (Distributed Link Tracking) meddelanden innehåller metadata för transaktioner som ska skrivas till blockchain. Konsumenten hämtar meddelanden och skickar data till en transaktion builder och signerad av routern.

### <a name="database-consumer"></a>Databas-konsument

Databasen konsumenten tar meddelanden från Service Bus och skickar data till en anslutna databasen, t.ex SQL-databas.

### <a name="storage-consumer"></a>Storage-konsument

Storage-konsument tar meddelanden från Service Bus och skickar data till en direktansluten lagring. Till exempel lagra hashade dokument i Azure Storage.

## <a name="transaction-builder-and-signer"></a>Transaktionen builder och signerad av

Om ett meddelande på inkommande message service broker behöver skrivas till blockchain, kommer att behandlas av DLT konsument. DLT konsumenten är en tjänst som hämtar det meddelande som innehåller metadata för en önskad transaktion att köra och sedan skickar information till den *transaktion builder och signerad av*. Den *transaktion builder och signerad av* monterar en blockchain-transaktion baserat på data och önskad blockchain-mål. När klar signeras transaktionen. Privata nycklar lagras i Azure Key Vault.

 Blockchain Workbench hämtar rätt privat nyckel från Key Vault och loggar transaktionen utanför Key Vault. När du har loggat, skickas transaktionen till transaktion routrar och huvudböcker.

## <a name="transaction-routers-and-ledgers"></a>Transaktionen routrar och huvudböcker

Transaktionen routrar och huvudböcker ta signerade transaktioner och vidarebefordrar dem till lämplig blockchain. För närvarande stöder Blockchain Workbench Ethereum som dess mål blockchain.

## <a name="dlt-watcher"></a>DLT watcher

Ett distribuerat transaktionsregister teknik (Distributed Link Tracking) watcher övervakar händelser som inträffar i block kedjor som är kopplade till Blockchain Workbench.
Händelser visas information som är relevanta för enskilda användare och system. Till exempel att skapa nya instanser av kontrakt, körning av transaktioner och tillståndsändringar. Händelserna inhämtas och skickas till den utgående meddelandekön så att de kan användas av underordnade konsumenter.

Till exempel SQL-konsument övervakar händelser, använder dem och fyller på SQL-databas med de inkluderade värdena. Kopian kan återskapning av en replik av data i kedjan i en annan store.

## <a name="azure-sql-database"></a>Azure SQL-databas

Azure SQL-databas som är kopplade till Blockchain Workbench lagras kontrakt definitioner, konfigurationsmetadata och en SQL-tillgänglig replik av data som lagras i blockchain. Dessa data kan enkelt efterfrågas, visualiseras eller analyseras av direkt åtkomst till databasen. Utvecklare och andra användare kan använda databasen för rapportering, analys eller andra datacentrerade integrationer. Användare kan till exempel visualisera transaktionsdata med Power BI.

Den här annan lagring gör möjligheten för företag att fråga data i SQL i stället för en blockchain-redovisning. Genom att standardisera på en standard-schema som är oberoende av blockchain teknik-stackar, möjliggör den annan lagringen dessutom återanvändning av rapporter och andra artefakter i projekt, scenarier och organisationer.

## <a name="azure-storage"></a>Azure Storage

Azure Storage används för att lagra kontrakt och metadata som associeras med kontrakt.

Spela upp en roll i många scenarier för blockchain-centric från inköpsorder och fraktsedlar till bilder som används i Nyheter och medicinska bilder till video kommer från en jämför inklusive polis brödtext kameror och större rörelse bilder, dokument. Dokument är inte lämpligt att placera direkt på blockchain.

Blockchain Workbench stöder möjligheten att lägga till dokument eller andra medieinnehåll med blockchain affärslogik. En hash av dokument eller media innehållet lagras i blockchain och faktiska dokument eller medieinnehåll lagras i Azure Storage. Associerade transaktionsinformationen är levereras till inkommande message service broker, paketeras, registrerat och dirigeras till blockchain. Den här processen utlöser händelser, som delas via den utgående meddelandekön. SQL DB använder den här informationen och skickar det till DB för att fråga senare. Underordnade system kan också använda dessa händelser så att den fungerar efter behov.

## <a name="monitoring"></a>Övervakning

Workbench ger programloggning med hjälp av Application Insights och Azure Monitor. Application Insights används för att lagra alla loggade informationen från Blockchain Workbench och innehåller fel, varningar och lyckade åtgärder. Application Insights kan användas av utvecklare kan felsöka problem med Blockchain Workbench. 

Azure Monitor innehåller information om hälsotillståndet för det blockchain-nätverket. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)