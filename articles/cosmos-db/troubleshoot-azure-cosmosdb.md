---
title: Hantera Azure Cosmos DB i Azure Storage Explorer
description: Lär dig hantera Azure Cosmos DB i Azure Storage Explorer.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Översikt av felsökningsguide i Microsoft Azure Cosmos DB i Storage Explorer

[Microsoft Azure Cosmos DB i Azure Storage Explorer](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) är en fristående app som gör det möjligt att ansluta till Azure Cosmos DB-konton som finns på Azure och i nationella moln från Windows, macOS eller Linux. Det gör det möjligt för dig att hantera Microsoft Azure Cosmos DB-entiteter, manipulera data och uppdatera lagrade procedurer och utlösare, och även andra Azure-entiteter som lagringsblobar och köer.

Den här guiden beskriver lösningar på vanliga problem för Microsoft Azure Cosmos DB i Storage Explorer.

- [Inloggningsproblem](#Sign-in-issues)
  - [Självsignerat certifikat i certifikatkedjan](#Self-signed-certificate-in-certificate-chain)
  - [Det gick inte att hämta prenumerationer](#Unable-to-retrieve-subscriptions)
  - [Det gick inte att visa autentiseringssidan](#Unable-to-see-the-authentication-page)
  - [Det går inte att ta bort kontot](#Cannot-remove-account)
- [HTTP-/HYYPS-proxyproblem](#Http/Https-proxy-issue)
- [Problem med ”Utvecklingsnod” under ”Lokala och kopplade” noder](#Development-node-under-Local-and-Attached-node-issue)
- [Nodfel vid koppling av Microsoft Azure Cosmos DB-konto i ”Lokala och kopplade”](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Nodfel vid expandera Microsoft Azure Cosmos DB](#Expand-Azure-Cosmos-DB-node-error)
- [Nästa steg](#Next-steps)

<h2 id="Sign-in-issues">Inloggningsproblem</h2>

Försök att starta om programmet innan du går vidare och se om problemen kan åtgärdas.

<h2 id="Self-signed-certificate-in-certificate-chain">Självsignerat certifikat i certifikatkedjan</h2>

Det finns några skäl till att det här felet visas, de två vanligaste är:

1. Du är bakom en ”transparent proxy”, vilket innebär att någon (till exempel din IT-avdelning) avlyssnar HTTPS-trafik, dekrypterar den och sedan krypterar den med hjälp av ett självsignerat certifikat.

2. Du kör programvara, till exempel antivirusprogram, som infogar ett självsignerat SSL-certifikat till de HTTPS-meddelanden du får.

När Storage Explorer påträffar ett sådant ”självsignerade certifikat” kan den inte längre veta om HTTPS-meddelandet den får har manipulerats eller inte. Om du har en kopia av det självsignerade certifikatet kan du berätta för Storage Explorer att lita på det. Om du är osäker på vem som infogat certifikatet kan du försöka att hitta det själv genom att göra följande:

1. Installera öppen SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (någon av de enklare versionerna är ok)
     - Mac och Linux: ska ingå i ditt operativsystem
2. Kör öppen SSL
    - Windows: Gå till installationskatalogen, sedan **/bin/**, dubbelklicka på **openssl.exe**.
    - Mac och Linux: kör **openssl** från en terminal
3. Kör `s_client -showcerts -connect microsoft.com:443`
4. Leta efter självsignerade certifikat. Om du är osäker på vilka som är självsignerade, leta då överallt efter om ämnet (”s:”) och utfärdaren (”i:”) är samma.
5.  När du har hittat självsignerade certifikat, kopiera och klistra in allt från och med **---BEGIN CERTIFICATE---** till **---END CERTIFICATE---** till en ny .cer-fil för varje certifikat.
6.  Öppna Storage Explorer och gå sedan till **Redigera** > **SSL-certifikat** > **Importera certifikat**. Med filväljaren, hitta, markera och öppna de CER-filerna som du skapade.

Om det inte går att hitta något självsignerat certifikat med hjälp av stegen ovan kan du skicka feedback för mer hjälp.

<h2 id="Unable-to-retrieve-subscriptions">Det gick inte att hämta prenumerationer</h2>

Om det inte går att hämta dina prenumerationer när du har loggat in:

- Verifiera att ditt konto har åtkomst till prenumerationerna genom att logga in på [Azure Portal](http://portal.azure.com/)
- Kontrollera att du har loggat in med rätt miljö ([Azure](http://portal.azure.com/), [Azure Kina](https://portal.azure.cn/), [Azure Tyskland](https://portal.microsoftazure.de/), [Azure som tillhör amerikanska myndigheter](http://portal.azure.us/), eller Anpassad miljö/Azure Stack)
- Om du är bakom en proxyserver, se till att du har konfigurerat Storage Explorer-proxyservern korrekt
- Försök att ta bort och lägga till kontot igen
- Försök att ta bort följande filer från arbetskatalogen (exempel: C:\Users\ContosoUser), och lägg sedan till kontot igen:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Titta på utvecklingsverktygskonsolen (f12) när du loggar in för eventuella felmeddelanden

![konsol](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">Det gick inte att visa autentiseringssidan</h2>  

Om du inte kan se autentiseringssidan:

- Beroende på anslutningen kan det ta ett tag för inloggningssidan att läsas in, vänta minst en minut innan du stänger dialogrutan för autentisering
- Om du är bakom en proxyserver, se till att du har konfigurerat Storage Explorer-proxyservern korrekt
- Visa utvecklingskonsolen genom att trycka på F12. Titta på svar från utvecklarkonsolen och se om du hittar en ledtråd till varför autentiseringen inte fungerar

<h2 id="Cannot-remove-account">Det går inte att ta bort kontot</h2>

Om du inte kan ta bort ett konto, eller återautentiseringslänken inte gör något

- Försök att ta bort följande filer från arbetskatalogen och lägg sedan till kontot igen:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Om du vill ta bort SAS-kopplade lagringsresurser, ta bort:
  - %AppData%/StorageExplorer-mapp för Windows
  - /Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer för Mac
  - ~/.config/StorageExplorer för Linux
  - **Du måste ange dina autentiseringsuppgifter igen** om du tar bort dessa filer


<h2 id="Http/Https-proxy-issue">HTTP-/HYYPS-proxyproblem</h2>

Du kan inte visa Microsoft Azure Cosmos DB-noder i det vänstra trädet när du konfigurerar HTTP-/HTTPS-proxy i ASE. Det är ett känt problem och korrigeras i nästa version. Du kan använda Microsoft Azure Cosmos DB-datautforskare i Azure Portal som en temporär lösning för tillfället. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">Problem med ”Utvecklingsnod” under ”Lokala och kopplade” noder</h2>

Du får inget svar när du klickar på noden ”utveckling” under ”lokala och kopplade” noder i vänster träd.  Det här beteendet är förväntat. Microsoft Azure Cosmos DB lokala emulatorn stöds i nästa version.

![Utvecklingsnod](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Nodfel vid koppling av Microsoft Azure Cosmos DB-konto i ”Lokala och kopplade”</h2>

Om du ser nedanstående fel när du har kopplat Microsoft Azure DB som Cosmos-kontot i ”Lokala och kopplade” noder, kontrollerar du om du använder rätt anslutningssträng.

![Fel vid fästning av Microsoft Azure Cosmos DB i Lokala och kopplade](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Nodfel vid expandera Microsoft Azure Cosmos DB</h2>

Du kan se nedanstående fel vid försök att expandera noderna i vänster träd. 

![Expanderingsfel](./media/troubleshoot-cosmosdb/expand-error.png)

Prova följande rekommendationer:

- Kontrollera om Microsoft Azure Cosmos DB-kontot håller på att skapas och försök igen när kontot har skapats.
- Om kontot är under noden ”Snabbåtkomst” eller ”Lokala och kopplade” noder måste du kontrollera om kontot har tagits bort. I så fall måste du manuellt ta bort noden.

<h2 id="Next-steps">Nästa steg</h2>

Om ingen av lösningarna fungerar, skicka ett e-postmeddelande till Microsoft Azure Cosmos DB Dev Tooling Team ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) med information om felet för att åtgärda problemen.





