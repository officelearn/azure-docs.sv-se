---
title: Azure Blockchain Workbench-arkitektur
description: Översikt över Azure Blockchain Workbench Preview-arkitektur och dess komponenter.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: aa972e8ae486d181f0c48df72ec89c925c940451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324899"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench-arkitektur

Azure Blockchain Workbench Preview förenklar blockchain-programutveckling genom att tillhandahålla en lösning med flera Azure-komponenter. Blockchain Workbench kan distribueras med hjälp av en lösningsmall på Azure Marketplace. Mallen kan du välja moduler och komponenter att distribuera inklusive blockchain stack, typ av klientprogram och stöd för IoT-integration. När Blockchain Workbench har distribuerats får du åtkomst till en webbapp, iOS-app och Android-app.

![Blockchain Workbench arkitektur](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identitet och autentisering

Med Blockchain Workbench kan ett konsortium federera sina företagsidentiteter med Azure Active Directory (Azure AD). Workbench genererar nya användarkonton för butiksidentiteter med företagsidentiteterna som lagras i Azure AD. Identitetsmappningen underlättar autentiserade inloggning till klient-API:er och program och använder autentiseringsprinciperna för organisationer. Workbench ger också möjlighet att associera företagsidentiteter till specifika roller inom ett givet smart kontrakt. Dessutom tillhandahåller Workbench också en mekanism för att identifiera de åtgärder som dessa roller kan vidta och vid vilken tidpunkt.

När Blockchain Workbench har distribuerats interagerar användarna med Blockchain Workbench antingen via klientprogrammen, REST-baserade klient-API:et eller meddelande-API:et. I samtliga fall måste interaktioner autentiseras, antingen via Azure Active Directory (Azure AD) eller enhetsspecifika autentiseringsuppgifter.

Användare matar med sina identiteter till ett konsortium Azure AD genom att skicka en e-postbjudan till deltagare på sin e-postadress. När du loggar in autentiseras dessa användare med namn, lösenord och principer. Till exempel tvåfaktorsautentisering av deras organisation.

Azure AD används för att hantera alla användare som har åtkomst till Blockchain Workbench. Varje enhet som ansluter till ett smart kontrakt är också associerad med Azure AD.

Azure AD används också för att tilldela användare till en särskild administratörsgrupp. Användare som är associerade med administratörsgruppen får åtkomst till rättigheter och åtgärder inom Blockchain Workbench, inklusive distribution av kontrakt och ge en användare behörighet att komma åt ett kontrakt. Användare utanför den här gruppen har inte åtkomst till administratörsåtgärder.

## <a name="client-applications"></a>Klientprogram

Workbench tillhandahåller automatiskt genererade klientprogram för webb och mobil (iOS, Android), som kan användas för att validera, testa och visa blockchain-program. Programgränssnittet genereras dynamiskt baserat på metadata för smarta kontrakt och kan hantera alla användningsfall. Klientprogrammen levererar en användarinriktad frontsida till de kompletta blockchain-programmen som genereras av Blockchain Workbench. Klientprogram autentiserar användare via Azure Active Directory (Azure AD) och presenterar sedan en användarupplevelse som är skräddarsydd för affärskontexten för det smarta kontraktet. Användarupplevelsen gör det möjligt att skapa nya smarta kontraktsinstanser av behöriga personer och presenterar sedan möjligheten att utföra vissa typer av transaktioner vid lämpliga tidpunkter i affärsprocessen som det smarta kontraktet representerar.

I webbprogrammet kan behöriga användare komma åt administratörskonsolen. Konsolen är tillgänglig för användare i gruppen Administratör i Azure AD och ger åtkomst till följande funktioner:

* Distribuera Microsoft som smarta kontrakt för populära scenarier. Till exempel ett scenario för överföring av tillgångar.
* Ladda upp och distribuera sina egna smarta kontrakt.
* Tilldela en användare åtkomst till det smarta kontraktet i samband med en viss roll.

Mer information finns i [exempelklientprogrammen för Azure Blockchain Workbench på GitHub](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>API för gateway-tjänst

Blockchain Workbench innehåller ett REST-baserat gateway service API. När du skriver till en blockchain genererar och levererar API:et meddelanden till en händelsemäklare. När data begärs av API:et skickas frågor till sql-databasen utanför kedjan. SQL-databasen innehåller en replik av data och metadata i kedjan som tillhandahåller kontext- och konfigurationsinformation för smarta kontrakt som stöds. Frågor returnerar nödvändiga data från repliken utanför kedjan i ett format som informeras av metadata för kontraktet.

Utvecklare kan komma åt gateway-tjänst-API:et för att skapa eller integrera blockchain-lösningar utan att förlita sig på Blockchain Workbench-klientappar.

> [!NOTE]
> För att aktivera autentiserat åtkomst till API:et registreras två klientprogram i Azure Active Directory. Azure Active Directory kräver distinkta programregistreringar varje programtyp (inbyggt och webb). 

## <a name="message-broker-for-incoming-messages"></a>Meddelandemäklare för inkommande meddelanden

Utvecklare som vill skicka meddelanden direkt till Blockchain Workbench kan skicka meddelanden direkt till Service Bus. Till exempel kan API för meddelanden användas för system-till-system-integrering eller IoT-enheter.

## <a name="message-broker-for-downstream-consumers"></a>Meddelandemäklare för nedströmskonsumenter

Under programmets livscykel inträffar händelser. Händelser kan utlösas av gateway-API:et eller i redovisningen. Händelsemeddelanden kan initiera nedströmskod baserat på händelsen.

Blockchain Workbench distribuerar automatiskt två typer av händelsekonsumenter. En konsument utlöses av blockchain-händelser för att fylla i SQL-butiken utanför kedjan. Den andra konsumenten är att samla in metadata för händelser som genereras av API:et som är relaterat till överföring och lagring av dokument.

## <a name="message-consumers"></a>Meddelande konsumenter

 Meddelandekonsumenter tar meddelanden från Service Bus. Den underliggande händelsemodellen för meddelandekonsumenter möjliggör tillägg av ytterligare tjänster och system. Du kan till exempel lägga till stöd för att fylla i CosmosDB eller utvärdera meddelanden med Azure Streaming Analytics. I följande avsnitt beskrivs meddelandet som konsumenter som ingår i Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Konsument med distribuerad redovisning

DLT-meddelanden (Distributed Ledger Technology) innehåller metadata för transaktioner som ska skrivas till blockkedjan. Konsumenten hämtar meddelandena och skickar data till en transaktionsbyggare, undertecknare och router.

### <a name="database-consumer"></a>Databas konsument

Databaskonsumenten tar meddelanden från Service Bus och skickar data till en bifogad databas, till exempel SQL-databas.

### <a name="storage-consumer"></a>Lagring konsument

Lagringskonsumenten tar meddelanden från Service Bus och skickar data till en bifogad lagring. Till exempel lagra hash-dokument i Azure Storage.

## <a name="transaction-builder-and-signer"></a>Transaktionsbyggare och undertecknare

Om ett meddelande på den inkommande meddelandemäklaren måste skrivas till blockchain, kommer det att behandlas av DLT-konsumenten. DLT-konsumenten är en tjänst som hämtar meddelandet som innehåller metadata för en önskad transaktion som ska köras och sedan skickar informationen till *transaktionsverktyget och undertecknaren*. *Transaktionsbyggaren och undertecknaren* monterar en blockchain-transaktion baserat på data och önskad blockchain-destination. När transaktionen har monterats signeras den. Privata nycklar lagras i Azure Key Vault.

 Blockchain Workbench hämtar lämplig privat nyckel från Key Vault och signerar transaktionen utanför Key Vault. När transaktionen har signerats skickas den till transaktionsroutrar och redovisningar.

## <a name="transaction-routers-and-ledgers"></a>Transaktionsroutrar och transaktioner

Transaktionsroutrar och liggare tar signerade transaktioner och dirigerar dem till lämplig blockkedja. För närvarande stöder Blockchain Workbench Ethereum som mål blockchain.

## <a name="dlt-watcher"></a>DLT-watcher

En DLT-watcher (Distributed Ledger Technology) övervakar händelser som inträffar på blockkedjor som är kopplade till Blockchain Workbench.
Händelser återspeglar information som är relevant för individer och system. Till exempel skapandet av nya kontraktsinstanser, körning av transaktioner och ändringar av tillstånd. Händelserna fångas in och skickas till den utgående meddelandemäklaren, så att de kan konsumeras av nedströmskonsumenter.

SQL-konsumenten övervakar till exempel händelser, använder dem och fyller i SQL-databasen med de inkluderade värdena. Kopian möjliggör återskapande av en kopia av butiksdata i en butik utanför kedjan.

## <a name="azure-sql-database"></a>Azure SQL-databas

Azure SQL-databasen som är kopplad till Blockchain Workbench lagrar kontraktsdefinitioner, konfigurationsmetadata och en SQL-tillgänglig replik av data som lagras i blockkedjan. Dessa data kan enkelt efterfrågas, visualiseras eller analyseras genom direkt åtkomst till databasen. Utvecklare och andra användare kan använda databasen för rapportering, analys eller andra datacentrerade integreringar. Användare kan till exempel visualisera transaktionsdata med Power BI.

Den här butikslagringen ger företagorganisationerna möjlighet att fråga data i SQL i stället för i en blockchain-redovisning. Genom att standardisera på ett standardschema som är agnostiker för blockkedjeteknikstackar möjliggör flyttkåpa lagringen återanvändning av rapporter och andra artefakter i projekt, scenarier och organisationer.

## <a name="azure-storage"></a>Azure Storage

Azure Storage används för att lagra kontrakt och metadata som är associerade med kontrakt.

Från inköpsorder och konossement, till bilder som används i nyheterna och medicinska bilder, till video som kommer från ett kontinuum inklusive kameror poliskropp och stora filmer, dokument spelar en roll i många blockchain-centrerad scenarier. Dokument är inte lämpliga att placera direkt på blockchain.

Blockchain Workbench stöder möjligheten att lägga till dokument eller annat medieinnehåll med blockchain-affärslogik. Ett hash-dokument eller medieinnehåll lagras i blockkedjan och det faktiska dokumentet eller medieinnehållet lagras i Azure Storage. Den associerade transaktionsinformationen levereras till den inkommande meddelandemäklaren, paketeras, signeras och dirigeras till blockkedjan. Den här processen utlöser händelser som delas via den utgående meddelandemäklaren. SQL DB använder den här informationen och skickar den till DB för senare frågor. Nedströmssystem kan också använda dessa händelser för att fungera på lämpligt sätt.

## <a name="monitoring"></a>Övervakning

Workbench tillhandahåller programloggning med Application Insights och Azure Monitor. Application Insights används för att lagra all loggad information från Blockchain Workbench och innehåller fel, varningar och lyckade åtgärder. Application Insights kan användas av utvecklare för att felsöka problem med Blockchain Workbench. 

Azure Monitor innehåller information om hälsotillståndet för blockchain-nätverket. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)