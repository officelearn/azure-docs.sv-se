---
title: Fel sökning av Körmiljö för Azure Functions kan inte kontaktas.
description: Lär dig hur du felsöker ett ogiltigt lagrings konto.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 212f10bd33479e5a9f7244d5b2090c0324f937c2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226766"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Så här felsöker du "Functions runtime går inte att komma åt"


## <a name="error-text"></a>Feltext
Det här dokumentet är avsett att felsöka följande fel när det visas i funktions portalen.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Sammanfattning
Det här problemet uppstår när Körmiljö för Azure Functions inte kan starta. Den vanligaste orsaken till att det här felet inträffar är att appen förlorar åtkomsten till lagrings kontot. [Läs mer om lagrings konto kraven här](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Felsökning
Vi går igenom de fyra vanligaste fel fallen, hur du identifierar och hur du löser varje ärende.

1. Lagrings kontot har tagits bort
1. Program inställningar för lagrings kontot har tagits bort
1. Autentiseringsuppgifterna för lagrings kontot är ogiltiga
1. Lagrings kontot är oåtkomligt
1. Daglig körnings kvot full

## <a name="storage-account-deleted"></a>Lagrings kontot har tagits bort

Alla Functions-appar kräver ett lagrings konto för att fungera. Om kontot tas bort fungerar inte-funktionen.

### <a name="how-to-find-your-storage-account"></a>Så här hittar du ditt lagrings konto

Börja med att leta upp ditt lagrings konto namn i dina program inställningar. Antingen `AzureWebJobsStorage` eller `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` kommer att innehålla namnet på ditt lagrings konto i en anslutnings sträng. Läs mer om [program inställnings referensen här](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Sök efter ditt lagrings konto i Azure Portal för att se om det fortfarande finns. Om den har tagits bort måste du återskapa ett lagrings konto och ersätta lagrings anslutnings strängarna. Funktions koden går förlorad och du måste distribuera den igen.

## <a name="storage-account-application-settings-deleted"></a>Program inställningar för lagrings kontot har tagits bort

I föregående steg, om du inte har någon anslutnings sträng för lagrings kontot, har de förmodligen tagits bort eller skrivits över. Att ta bort appinställningar utförs vanligt vis när du använder distributions fack eller Azure Resource Manager skript för att ange program inställningar.

### <a name="required-application-settings"></a>Nödvändiga program inställningar

* Krävs
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Krävs för förbruknings plan funktioner
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Läs om de här program inställningarna här](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Riktlinjer

* Kontrol lera inte "plats inställning" för någon av dessa inställningar. När du växlar distributions platser avbryts funktionen.
* Ändra inte inställningarna som en del av automatiserade distributioner.
* De här inställningarna måste tillhandahållas och vara giltiga vid skapande tillfället. En automatiserad distribution som inte innehåller de här inställningarna leder till en icke-funktionell app, även om inställningarna läggs till efter faktumet.

## <a name="storage-account-credentials-invalid"></a>Autentiseringsuppgifterna för lagrings kontot är ogiltiga

Ovanstående lagrings kontots anslutnings strängar måste uppdateras om du återskapar lagrings nycklar. [Läs mer om hantering av lagrings nycklar här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Lagrings kontot är oåtkomligt

Ditt Funktionsapp måste kunna komma åt lagrings kontot. Vanliga problem som blockerar en funktions åtkomst till ett lagrings konto är:

* Function-appar som distribueras till App Service miljöer utan rätt nätverks regler för att tillåta trafik till och från lagrings kontot
* Lagrings kontots brand vägg är aktive rad och inte konfigurerad för att tillåta trafik till och från funktioner. [Läs mer om konfiguration av brand vägg för lagrings konto här](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Daglig körnings kvot full

Om du har konfigurerat en daglig körnings kvot så inaktive ras Funktionsapp tillfälligt och många av Portal kontrollerna blir otillgängliga. 

* För att verifiera, se öppna plattforms funktioner > Funktionsapp inställningar i portalen. Följande meddelande visas om du använder kvoten
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Ta bort kvoten och starta om appen för att lösa problemet.

## <a name="next-steps"></a>Nästa steg

Nu när din Funktionsapp är tillbaka och fungerar tar du en titt på våra snabb starter och Developer-referenser för att komma igång igen!

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
