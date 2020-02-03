---
title: Fel sökning av Körmiljö för Azure Functions kan inte kontaktas.
description: Lär dig hur du felsöker ett ogiltigt lagrings konto.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963894"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Så här felsöker du "Functions runtime går inte att komma åt"

Den här artikeln är avsedd att felsöka fel meddelandet "Functions runtime kan inte kontaktas" när det visas i Azure Portal. När det här felet uppstår visas följande fel sträng som visas i portalen.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Detta inträffar när Körmiljö för Azure Functions inte kan starta. Den vanligaste orsaken till att det här felet inträffar är att appen förlorar åtkomsten till lagrings kontot. Mer information finns i [krav för lagrings konton](storage-considerations.md#storage-account-requirements).

Resten av den här artikeln hjälper dig att felsöka följande orsaker till det här felet, inklusive hur du identifierar och löser varje ärende.

+ [Lagringskontot har tagits bort](#storage-account-deleted)
+ [Programinställningar för lagringskontot har tagits bort](#storage-account-application-settings-deleted)
+ [Autentiseringsuppgifterna för lagringskonto är ogiltiga](#storage-account-credentials-invalid)
+ [Det går inte att komma åt lagringskontot](#storage-account-inaccessible)
+ [Daglig körnings kvot har överskridits](#daily-execution-quota-full)
+ [Din app ligger bakom en brand vägg](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Lagrings kontot har tagits bort

Alla Functions-appar kräver ett lagrings konto för att fungera. Om kontot tas bort fungerar inte-funktionen.

### <a name="how-to-find-your-storage-account"></a>Så här hittar du ditt lagrings konto

Börja med att leta upp ditt lagrings konto namn i dina program inställningar. Antingen `AzureWebJobsStorage` eller `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` kommer att innehålla namnet på ditt lagrings konto i en anslutnings sträng. Läs mer om [program inställnings referensen här](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Sök efter ditt lagrings konto i Azure Portal för att se om det fortfarande finns. Om den har tagits bort måste du återskapa ett lagrings konto och ersätta lagrings anslutnings strängarna. Funktions koden går förlorad och du måste distribuera den igen.

## <a name="storage-account-application-settings-deleted"></a>Program inställningar för lagrings kontot har tagits bort

I föregående steg, om du inte har någon anslutnings sträng för lagrings kontot, togs den förmodligen bort eller skrivs över. Att ta bort appinställningar utförs vanligt vis när du använder distributions fack eller Azure Resource Manager skript för att ange program inställningar.

### <a name="required-application-settings"></a>Nödvändiga program inställningar

* Krävs
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Krävs för förbruknings plan funktioner
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Läs om de här program inställningarna här](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Vägledning

* Kontrol lera inte "plats inställning" för någon av dessa inställningar. När du växlar distributions platser bryts Function-appen.
* Ändra inte inställningarna som en del av automatiserade distributioner.
* De här inställningarna måste tillhandahållas och vara giltiga vid skapande tillfället. En automatiserad distribution som inte innehåller de här inställningarna resulterar i en Function-app som inte körs, även om inställningarna läggs till senare.

## <a name="storage-account-credentials-invalid"></a>Autentiseringsuppgifterna för lagrings kontot är ogiltiga

Ovanstående lagrings kontots anslutnings strängar måste uppdateras om du återskapar lagrings nycklar. [Läs mer om hantering av lagrings nycklar här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>Lagrings kontot är oåtkomligt

Din Function-app måste kunna komma åt lagrings kontot. Vanliga problem som blockerar en funktions åtkomst till ett lagrings konto är:

+ function-appar som distribueras till App Service miljöer (ASE) utan rätt nätverks regler för att tillåta trafik till och från lagrings kontot.

+ Lagrings kontots brand vägg är aktive rad och inte konfigurerad för att tillåta trafik till och från funktioner. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Daglig körnings kvot full

Om du har konfigurerat en daglig körnings kvot inaktive ras funktions appen tillfälligt, vilket gör att många av Portal kontrollerna blir otillgängliga. 

+ Om du vill verifiera i [Azure Portal](https://portal.azure.com)öppnar du **plattforms funktioner** > **Funktionsapp inställningar** i din Function-app. När du befinner dig över den **dagliga användnings kvot** som du har angett visas följande meddelande:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ Lös problemet genom att ta bort eller öka den dagliga kvoten och starta om appen. Annars blockeras körningen av appen till nästa dag.

## <a name="app-is-behind-a-firewall"></a>Appen ligger bakom en brand vägg

Funktions körningen kommer inte att kunna nås om din Function-app finns i en [internt belastningsutjämnad App Service-miljön](../app-service/environment/create-ilb-ase.md) och har kon figurer ATS för att blockera inkommande Internet trafik eller har [inkommande IP-begränsningar](functions-networking-options.md#inbound-ip-restrictions) som är konfigurerade för att blockera Internet åtkomst. Azure Portal gör anrop direkt till appen som körs för att hämta listan över funktioner och gör HTTP-anrop till KUDU-slutpunkten. Inställningarna för plattforms nivå på fliken `Platform Features` är fortfarande tillgängliga.

För att verifiera din ASE-konfiguration navigerar du till NSG i under nätet där ASE finns och validerar regler för inkommande trafik som tillåter trafik från den offentliga IP-adressen för den dator där du ansluter till programmet. Du kan också använda portalen från en dator som är ansluten till det virtuella nätverk som kör din app eller en virtuell dator som körs i det virtuella nätverket. [Läs mer om regel konfiguration för inkommande trafik här](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Efterföljande moment

Lär dig mer om att övervaka dina funktions appar:

> [!div class="nextstepaction"]
> [Övervaka Azure Functions](functions-monitoring.md)
