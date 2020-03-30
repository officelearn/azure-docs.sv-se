---
title: 'Felsöka fel: Azure Functions Runtime kan inte nås'
description: Läs om hur du felsöker ett ogiltigt lagringskonto.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063789"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Felsöka fel: "Azure Functions Runtime is unreachable"

Den här artikeln hjälper dig att felsöka följande felsträng som visas i Azure-portalen:

> "Fel: Azure Functions Runtime kan inte nås. Klicka här för mer information om lagringskonfiguration."

Det här problemet uppstår när Azure Functions Runtime inte kan starta. Den vanligaste orsaken till problemet är att funktionsappen har förlorat åtkomsten till sitt lagringskonto. Mer information finns i [Krav på lagringskonto](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

Resten av den här artikeln hjälper dig att felsöka följande orsaker till det här felet, inklusive hur du identifierar och löser varje ärende.

## <a name="storage-account-was-deleted"></a>Lagringskonto har tagits bort

Varje funktionsapp kräver ett lagringskonto för att fungera. Om kontot tas bort fungerar inte funktionen.

Börja med att söka efter ditt lagringskontonamn i programinställningarna. Antingen `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` eller innehåller namnet på ditt lagringskonto som är inlindat i en anslutningssträng. Mer information finns i [Appinställningar referens för Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Sök efter ditt lagringskonto i Azure-portalen för att se om det fortfarande finns. Om det har tagits bort återskapar du lagringskontot och ersätter dina lagringsanslutningssträngar. Din funktionskod går förlorad och du måste distribuera om den.

## <a name="storage-account-application-settings-were-deleted"></a>Programinställningar för lagringskonto har tagits bort

I föregående steg, om du inte kan hitta en anslutningssträng för lagringskonto, togs den troligen bort eller skrevs över. Ta bort programinställningar oftast händer när du använder distributionsplatser eller Azure Resource Manager-skript för att ange programinställningar.

### <a name="required-application-settings"></a>Obligatoriska programinställningar

* Krävs:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Krävs för funktioner i konsumtionsplanen:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Mer information finns i [Appinställningar referens för Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Riktlinjer

* Kontrollera inte "platsinställning" för någon av dessa inställningar. Om du byter distributionsplatser bryts funktionsappen.
* Ändra inte dessa inställningar som en del av automatiska distributioner.
* Dessa inställningar måste anges och vara giltiga vid skapande. En automatiserad distribution som inte innehåller dessa inställningar resulterar i en funktionsapp som inte körs, även om inställningarna läggs till senare.

## <a name="storage-account-credentials-are-invalid"></a>Autentiseringsuppgifter för lagringskonto är ogiltiga

De tidigare diskuterade lagringskontoanslutningssträngarna måste uppdateras om du återskapar lagringsnycklar. Mer information om hantering av lagringsnyckel finns i [Skapa ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>Lagringskontot är inte tillgängligt

Din funktionsapp måste kunna komma åt lagringskontot. Vanliga problem som blockerar en funktionsapps åtkomst till ett lagringskonto är:

* Funktionsappen distribueras till apptjänstmiljön utan rätt nätverksregler för att tillåta trafik till och från lagringskontot.

* Brandväggen för lagringskonto är aktiverad och inte konfigurerad för att tillåta trafik till och från funktioner. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>Den dagliga genomförandekvoten är full

Om du har konfigurerat en daglig körningskvot inaktiveras funktionsappen tillfälligt, vilket gör att många av portalkontrollerna blir otillgängliga. 

Om du vill verifiera kvoten i [Azure-portalen](https://portal.azure.com)väljer du**Funktionsinställningar för** **plattformsfunktioner** > i funktionsappen. Om du har överskridit den **dagliga användningskvot** som du har angett visas följande meddelande:

  > "Funktionsappen har nått daglig användningskvot och har stoppats till nästa tidsram på 24 timmar."

Lös problemet genom att ta bort eller öka den dagliga kvoten och sedan starta om appen. Annars blockeras körningen av appen till nästa dag.

## <a name="app-is-behind-a-firewall"></a>Appen ligger bakom en brandvägg

Din funktionskörning kan inte nås av något av följande skäl:

* Din funktionsapp finns i en [internt belastningsbalanserad App Service Environment](../app-service/environment/create-ilb-ase.md) och den är konfigurerad för att blockera inkommande internettrafik.

* Din funktionsapp har [inkommande IP-begränsningar](functions-networking-options.md#inbound-ip-restrictions) som är konfigurerade för att blockera internetåtkomst. 

Azure-portalen ringer direkt till den app som körs för att hämta listan över funktioner och gör HTTP-anrop till Kudu-slutpunkten. Inställningar på plattformsnivå under fliken **Plattformsfunktioner** är fortfarande tillgängliga.

Så här verifierar du konfigurationen av apptjänstmiljön:
1. Gå till nätverkssäkerhetsgruppen (NSG) i undernätet där App Service-miljön finns.
1. Validera de inkommande reglerna så att trafik som kommer från den offentliga IP-adressen för den dator där du använder programmet. 
   
Du kan också använda portalen från en dator som är ansluten till det virtuella nätverket som kör appen eller till en virtuell dator som körs i det virtuella nätverket. 

Mer information om konfiguration av inkommande regel finns i avsnittet "Nätverkssäkerhetsgrupper" i [Nätverksöverväganden för en App Service-miljö](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups).

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du övervakar dina funktionsappar:

> [!div class="nextstepaction"]
> [Övervaka Azure Functions](functions-monitoring.md)
