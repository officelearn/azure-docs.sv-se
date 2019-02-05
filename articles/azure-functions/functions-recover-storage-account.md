---
title: Så här felsöker du Azure Functions-körning kan inte nås.
description: Lär dig hur du felsöker ett ogiltigt storage-konto.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 861a4d4d64f970a13aba68f831d2148f8a354ef4
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55732166"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Så här felsöker du ”functions-körning kan inte nås”


## <a name="error-text"></a>Feltext
Det här dokumentet är avsett att felsöka följande fel när det visas i Functions-portalen.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Sammanfattning
Det här problemet inträffar när Azure Functions-körning inte kan starta. Den vanligaste orsaken till felet är funktionsappen att förlora åtkomsten till lagringskontot. [Läs mer om kontot lagringskraven här](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Felsökning
Vi går via de fyra vanligaste fel fall, så här identifierar du och hur du löser varje fall.

1. Storage-kontot har tagits bort
1. Programinställningar för Storage-konto har tagits bort
1. Autentiseringsuppgifter för lagringskonto ogiltig
1. Storage-konto som är otillgänglig
1. Kvot för daglig körning fullständig

## <a name="storage-account-deleted"></a>Storage-kontot har tagits bort

Varje funktionsapp kräver ett lagringskonto för att fungera. Om kontot tas bort funktionen fungerar inte.

### <a name="how-to-find-your-storage-account"></a>Så här hittar du ditt lagringskonto

Starta genom att leta upp namnet på ditt lagringskonto i programinställningarna. Antingen `AzureWebJobsStorage` eller `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` innehåller namnet på ditt lagringskonto som har samlats i en anslutningssträng. Läs mer information på den [här inställningsreferens för program](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Sök efter ditt lagringskonto i Azure portal för att se om det fortfarande finns. Om det har tagits bort, behöver du återskapa ett lagringskonto och Ersätt anslutningssträngar för lagringsutrymme. Funktionskoden går förlorad och du måste distribuera den igen.

## <a name="storage-account-application-settings-deleted"></a>Programinställningar för Storage-konto har tagits bort

I föregående steg om du inte har en anslutningssträng för lagringskonto har de förmodligen tagits bort eller skrivs över. Tar bort appinställningar görs oftast när du använder distributionsplatser eller Azure Resource Manager-skript för att ange programinställningar.

### <a name="required-application-settings"></a>Nödvändiga programinställningar

* Krävs
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Krävs för förbrukning Plan funktioner
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#websitecontentazurefileconnectionstring)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#websitecontentshare)

[Läs mer om dessa programinställningar](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Riktlinjer

* Markera inte ”platsinställning” för någon av dessa inställningar. När du växla distributionsfack funktionen bryts.
* Ange inte dessa inställningar när du använder automatisk distribution.
* Dessa inställningar måste vara angivna och giltig vid tidpunkten för skapandet. En automatisk distribution som inte innehåller de här inställningarna resulterar i en icke-fungerande App, även om inställningarna har lagts till i efterhand.

## <a name="storage-account-credentials-invalid"></a>Autentiseringsuppgifter för lagringskonto ogiltig

Ovanstående anslutningssträngar för Storage-konto måste uppdateras om du återskapar lagringsnycklar. [Läs mer om storage nyckelhantering här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Storage-konto som är otillgänglig

Funktionsappen måste kunna komma åt lagringskontot. Vanliga problem som blockerar en Functions-åtkomst till ett lagringskonto är:

* Funktionsappar som distribueras till App Service-miljöer utan rätt Nätverksregler som tillåter trafik till och från storage-konto
* Storage-konto brandväggen är aktiverad och inte konfigurerad för att tillåta trafik till och från Functions. [Läs mer om storage-konto brandväggskonfiguration här](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Kvot för daglig körning fullständig

Om du har en kvot för daglig körning som konfigurerats Funktionsappen inaktiveras tillfälligt och många av portalkontroller blir otillgänglig. 

* Kontrollera, kontrollera att öppna plattformsfunktioner > Funktionsappinställningar i portalen. Du ser följande meddelande om du har överskridit kvoten
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Ta bort kvoten och starta om din app för att lösa problemet.

## <a name="next-steps"></a>Nästa steg

Nu när Funktionsappen är backend och fungerar kan du ta en titt på våra snabbstarter och utvecklare referenser till komma igång och körs igen!

* [Skapa din första Azure-funktion](functions-create-first-azure-function.md)  
  Kom igång snabbt och skapa din första funktion med hjälp av Azure Functions-snabbstart. 
* [Azure Functions, info för utvecklare](functions-reference.md)  
  Ger mer teknisk information om Azure Functions-runtime och en referens för kodning av funktioner och definiering av utlösare och bindningar.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive värdplanen för förbrukning, och hur du väljer rätt plan. 
* [Läs mer om Azure App Service](../app-service/overview.md)  
  Azure Functions använder Azure App Service för grundläggande funktioner som distributioner, miljövariabler och diagnostik. 
