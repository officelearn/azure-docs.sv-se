---
title: Nätverksalternativ för Azure Functions
description: En översikt över alla nätverks alternativ som är tillgängliga i Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80419566"
---
# <a name="azure-functions-networking-options"></a>Nätverksalternativ för Azure Functions

I den här artikeln beskrivs de nätverksfunktioner som är tillgängliga i värd alternativen för Azure Functions. Alla följande nätverks alternativ ger dig möjlighet att komma åt resurser utan att använda Internet adresser eller för att begränsa Internet åtkomst till en Function-app.

Värd modellerna har olika nivåer av nätverks isolering tillgänglig. Genom att välja rätt kan du uppfylla kraven på nätverks isolering.

Du kan vara värd för funktions appar på ett par olika sätt:

* Du kan välja bland plan alternativ som körs i en infrastruktur för flera innehavare, med olika nivåer av anslutningar för virtuella nätverk och skalnings alternativ:
    * [Förbruknings planen](functions-scale.md#consumption-plan) skalas dynamiskt som svar på belastning och erbjuder minimala alternativ för nätverks isolering.
    * [Premium-planen](functions-scale.md#premium-plan) kan också skalas dynamiskt och erbjuder mer omfattande nätverks isolering.
    * Azure [App Service plan](functions-scale.md#app-service-plan) fungerar med en fast skala och erbjuder nätverks isolering som liknar Premium planen.
* Du kan köra funktioner i en [App Service-miljön](../app-service/environment/intro.md). Den här metoden distribuerar din funktion till det virtuella nätverket och ger fullständig nätverks kontroll och isolering.

## <a name="matrix-of-networking-features"></a>Matris med nätverksfunktioner

|                |[Förbruknings plan](functions-scale.md#consumption-plan)|[Premiumplan](functions-scale.md#premium-plan)|[App Service plan](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Inkommande IP-begränsningar och åtkomst till privata platser](#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integrering av virtuella nätverk](#virtual-network-integration)|❌Nej|✅Ja (regional)|✅Ja (regional och gateway)|✅Ja|
|[Virtuella nätverks utlösare (icke-HTTP)](#virtual-network-triggers-non-http)|❌Nej| ✅Ja |✅Ja|✅Ja|
|[Hybrid anslutningar](#hybrid-connections) (endast Windows)|❌Nej|✅Ja|✅Ja|✅Ja|
|[Utgående IP-begränsningar](#outbound-ip-restrictions)|❌Nej| ✅Ja|✅Ja|✅Ja|

## <a name="inbound-ip-restrictions"></a>Inkommande IP-begränsningar

Du kan använda IP-begränsningar för att definiera en prioriterad lista över IP-adresser som tillåts eller nekas åtkomst till din app. Listan kan innehålla IPv4-och IPv6-adresser. När det finns en eller flera poster finns implicit "Neka alla" i slutet av listan. IP-begränsningar fungerar med alla funktions värd alternativ.

> [!NOTE]
> Med nätverks begränsningar på plats kan du bara använda Portal redigeraren från det virtuella nätverket eller när du har placerat IP-adressen för den dator som du använder för att komma åt Azure Portal i listan Betrodda mottagare. Men du kan fortfarande komma åt funktioner på fliken **plattforms funktioner** från vilken dator som helst.

Läs mer i [Azure App Service statiska åtkomst begränsningar](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Åtkomst till privat plats

Åtkomst till privata webbplatser syftar bara på att göra appen tillgänglig från ett privat nätverk, till exempel ett virtuellt Azure-nätverk.

* Åtkomst till privata webbplatser är tillgängligt i [Premium](./functions-premium-plan.md)-, [konsumtions](functions-scale.md#consumption-plan)-och [app Services](functions-scale.md#app-service-plan) planerna när tjänstens slut punkter konfigureras.
    * Tjänst slut punkter kan konfigureras per app med hjälp av **plattforms funktioner** > **nätverk** > **Konfigurera åtkomst begränsningar** > **Lägg till regel**. Virtuella nätverk kan nu väljas som regel typ.
    * Mer information finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Tänk på att med tjänst slut punkter har din funktion fortfarande fullständig utgående åtkomst till Internet, även om du har konfigurerat Virtual Network-integrering.
* Åtkomst till privata webbplatser är också tillgänglig i en App Service-miljön som har kon figurer ATS med en intern belastningsutjämnare (ILB). Mer information finns i [skapa och använda en intern belastningsutjämnare med en app service-miljön](../app-service/environment/create-ilb-ase.md).

Information om hur du konfigurerar åtkomst till en privat plats finns i [upprätta Azure Functions privat plats åtkomst](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Virtual Network-integration

Med integrering med virtuella nätverk får du till gång till resurser i ett virtuellt nätverk med hjälp av din Function-app.
Azure Functions stöder två typer av integrering av virtuella nätverk:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Integrering av virtuella nätverk i Azure Functions använder delad infrastruktur med App Service Web Apps. Mer information om de två typerna av integrering med virtuella nätverk finns i:

* [Regional integrering av virtuella nätverk](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway – nödvändig virtuell nätverks integrering](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Information om hur du konfigurerar integrering av virtuella nätverk finns i [integrera en Function-app med ett virtuellt Azure-nätverk](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Regional integrering av virtuella nätverk

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Anslut till tjänst slut punktens säkra resurser

För att ge en högre säkerhets nivå kan du begränsa ett antal Azure-tjänster till ett virtuellt nätverk med hjälp av tjänstens slut punkter. Du måste sedan integrera din Function-app med det virtuella nätverket för att få åtkomst till resursen. Den här konfigurationen stöds på alla planer som stöder integrering av virtuella nätverk.

Mer information finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Begränsa ditt lagrings konto till ett virtuellt nätverk

När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring. Du kan för närvarande inte använda några begränsningar för virtuella nätverk för det här kontot. Om du konfigurerar en tjänst slut punkt för virtuellt nätverk på det lagrings konto som du använder för din Function-app kommer den konfigurationen att bryta din app.

Mer information finns i [krav för lagrings konton](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Använda Key Vault referenser

Du kan använda Azure Key Vault referenser för att använda hemligheter från Azure Key Vault i Azure Functions program utan att behöva göra några kod ändringar. Azure Key Vault är en tjänst som tillhandahåller centraliserad hemligheter-hantering med fullständig kontroll över åtkomst principer och gransknings historik.

[Key Vault referenser](../app-service/app-service-key-vault-references.md) fungerar för närvarande inte om ditt nyckel valv skyddas med tjänst slut punkter. Om du vill ansluta till ett nyckel valv med hjälp av integrering med virtuella nätverk måste du anropa Key Vault i din program kod.

## <a name="virtual-network-triggers-non-http"></a>Virtuella nätverks utlösare (icke-HTTP)

För närvarande kan du använda icke-HTTP-utlösare i ett virtuellt nätverk på något av två sätt:

+ Kör din Function-app i en Premium-plan och aktivera stöd för virtuell nätverks utlösare.
+ Kör din Function-app i en App Service plan eller App Service-miljön.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium plan med virtuella nätverks utlösare

När du kör en Premium-plan kan du ansluta funktioner som inte är HTTP-utlösare till tjänster som körs i ett virtuellt nätverk. För att göra detta måste du aktivera stöd för virtuell nätverks utlösare för din Function-app. Inställningen **stöd för virtuell nätverks utlösare** finns i [Azure Portal](https://portal.azure.com) under **Function app-inställningar**.

![Växla virtuellt nätverk](media/functions-networking-options/virtual-network-trigger-toggle.png)

Du kan också aktivera virtuella nätverks utlösare med hjälp av följande Azure CLI-kommando:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Virtuella nätverks utlösare stöds i version 2. x och senare av Functions-körningen. Följande typer av icke-HTTP-utlösare stöds.

| Anknytning | Lägsta version |
|-----------|---------| 
|[Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 eller senare |
|[Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 eller senare|
|[Microsoft. Azure. WebJobs. Extensions. Service Bus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 eller senare|
|[Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 eller senare|
|[Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 eller senare|

> [!IMPORTANT]
> När du aktiverar stöd för virtuell nätverks utlösare är det bara de utlösare typer som visas i den föregående tabellen dynamiskt med ditt program. Du kan fortfarande använda utlösare som inte finns i tabellen, men de skalas inte förbi antalet förvärmade instanser. En fullständig lista över utlösare finns i [utlösare och bindningar](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service plan och App Service-miljön med virtuella nätverks utlösare

När din Function-App körs i antingen en App Service plan eller en App Service-miljön, kan du använda funktioner som inte är HTTP-utlösare. För att funktionerna ska kunna aktive ras korrekt måste du vara ansluten till ett virtuellt nätverk med åtkomst till den resurs som definierats i utlösaren.

Anta till exempel att du vill konfigurera Azure Cosmos DB att endast acceptera trafik från ett virtuellt nätverk. I så fall måste du distribuera din Function-app i en App Service plan som tillhandahåller integrering av virtuella nätverk med det virtuella nätverket. Integrering aktiverar en funktion som utlöses av den Azure Cosmos DB resursen.

## <a name="hybrid-connections"></a>Hybridanslutningar

[Hybridanslutningar](../service-bus-relay/relay-hybrid-connections-protocol.md) är en funktion i Azure Relay som du kan använda för att få åtkomst till program resurser i andra nätverk. Den ger åtkomst från din app till en program slut punkt. Du kan inte använda den för att få åtkomst till ditt program. Hybridanslutningar är tillgängligt för funktioner som körs i Windows i alla utom förbruknings planen.

Som används i Azure Functions motsvarar varje hybrid anslutning en enskild TCP-värd och port-kombination. Det innebär att hybrid anslutningens slut punkt kan finnas på alla operativ system och program så länge som du har åtkomst till en TCP-lyssnings port. Hybridanslutningar funktionen känner inte igen eller bryr sig om vad applikations protokollet är eller vad du använder. Den ger bara nätverks åtkomst.

Mer information finns i [App Service dokumentationen för hybridanslutningar](../app-service/app-service-hybrid-connections.md). Samma konfigurations steg har stöd för Azure Functions.

>[!IMPORTANT]
> Hybridanslutningar stöds endast på Windows-planer. Linux stöds inte.

## <a name="outbound-ip-restrictions"></a>Utgående IP-begränsningar

Utgående IP-begränsningar är tillgängliga i en Premium-plan, App Service plan eller App Service-miljön. Du kan konfigurera utgående begränsningar för det virtuella nätverk där App Service-miljön har distribuerats.

När du integrerar en Function-app i en Premium-plan eller en App Service plan med ett virtuellt nätverk kan appen fortfarande göra utgående anrop till Internet som standard. Genom att lägga till program `WEBSITE_VNET_ROUTE_ALL=1`inställningen tvingar du all utgående trafik att skickas till det virtuella nätverket, där regler för nätverks säkerhets grupper kan användas för att begränsa trafiken.

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om nätverk och Azure Functions:

* [Följ självstudien om att komma igång med integrering av virtuella nätverk](./functions-create-vnet.md)
* [Läs vanliga frågor och svar om funktionen Networking](./functions-networking-faq.md)
* [Lär dig mer om virtuell nätverks integrering med App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Lär dig mer om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera fler nätverksfunktioner och kontroll funktioner med App Service miljöer](../app-service/environment/intro.md)
* [Anslut till enskilda lokala resurser utan brand Väggs ändringar med hjälp av Hybridanslutningar](../app-service/app-service-hybrid-connections.md)
