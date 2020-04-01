---
title: Nätverksalternativ för Azure Functions
description: En översikt över alla nätverksalternativ som är tillgängliga i Azure Functions.
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 6637627d48df8f9b6126debc215aac9bceb76f6b
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419566"
---
# <a name="azure-functions-networking-options"></a>Nätverksalternativ för Azure Functions

I den här artikeln beskrivs de nätverksfunktioner som är tillgängliga i värdalternativen för Azure Functions. Alla följande nätverksalternativ ger dig viss möjlighet att komma åt resurser utan att använda internet-routable adresser eller att begränsa internettillgång till en funktion app.

Värdmodellerna har olika nivåer av nätverksisolering tillgängliga. Om du väljer rätt kan du uppfylla kraven på nätverksisolering.

Du kan vara värd för funktionsappar på ett par olika sätt:

* Du kan välja mellan planalternativ som körs på en infrastruktur med flera liggande uppgifter, med olika nivåer av virtuella nätverksanslutnings- och skalningsalternativ:
    * [Förbrukningsplanen](functions-scale.md#consumption-plan) skalas dynamiskt som svar på belastning och erbjuder minimala alternativ för nätverksisolering.
    * [Premium-planen](functions-scale.md#premium-plan) skalas också dynamiskt och erbjuder mer omfattande nätverksisolering.
    * Azure [App Service-planen](functions-scale.md#app-service-plan) fungerar i en fast skala och erbjuder nätverksisolering som liknar Premium-planen.
* Du kan köra funktioner i en [apptjänstmiljö](../app-service/environment/intro.md). Den här metoden distribuerar din funktion till ditt virtuella nätverk och erbjuder fullständig nätverkskontroll och isolering.

## <a name="matrix-of-networking-features"></a>Matris för nätverksfunktioner

|                |[Konsumtionsplan](functions-scale.md#consumption-plan)|[Premiumplan](functions-scale.md#premium-plan)|[App Service plan](functions-scale.md#app-service-plan)|[App Service-miljön](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Inkommande IP-begränsningar och privat åtkomst till webbplatsen](#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integrering av virtuella nätverk](#virtual-network-integration)|❌Nej|✅Ja (regionalt)|✅Ja (regional och gateway)|✅Ja|
|[Virtuella nätverksutlösare (icke-HTTP)](#virtual-network-triggers-non-http)|❌Nej| ✅Ja |✅Ja|✅Ja|
|[Hybridanslutningar](#hybrid-connections) (endast Windows)|❌Nej|✅Ja|✅Ja|✅Ja|
|[Utgående IP-begränsningar](#outbound-ip-restrictions)|❌Nej| ✅Ja|✅Ja|✅Ja|

## <a name="inbound-ip-restrictions"></a>Inkommande IP-begränsningar

Du kan använda IP-begränsningar för att definiera en prioriterad lista över IP-adresser som tillåts eller nekas åtkomst till din app. Listan kan innehålla IPv4- och IPv6-adresser. När det finns en eller flera poster finns det en implicit "neka alla" i slutet av listan. IP-begränsningar fungerar med alla funktionsvärdalternativ.

> [!NOTE]
> Med nätverksbegränsningar på plats kan du bara använda portalredigeraren från det virtuella nätverket eller när du har placerat IP-adressen för den dator som du använder för att komma åt Azure-portalen i listan Betrodda mottagare. Du kan dock fortfarande komma åt alla funktioner på fliken **Plattformsfunktioner** från vilken dator som helst.

Mer information finns i [statiska åtkomstbegränsningar för Azure App Service](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Åtkomst till privat plats

Privat webbplatsåtkomst avser att göra din app endast tillgänglig från ett privat nätverk, till exempel ett virtuellt Azure-nätverk.

* Privat webbplatsåtkomst är tillgänglig i [abonnemangen För Premium,](./functions-premium-plan.md) [Förbrukning](functions-scale.md#consumption-plan)och [App Service](functions-scale.md#app-service-plan) när tjänstslutpunkter har konfigurerats.
    * Tjänstslutpunkter kan konfigureras per app under **Plattformsfunktioner** > **Konfigurera** > **åtkomstbegränsningar** > **Konfigurera åtkomstbegränsningar**. Virtuella nätverk kan nu väljas som regeltyp.
    * Mer information finns i [slutpunkter för virtuella nätverkstjänster.](../virtual-network/virtual-network-service-endpoints-overview.md)
    * Tänk på att med tjänstslutpunkter har din funktion fortfarande full utgående åtkomst till Internet, även med virtual network integration konfigurerad.
* Privat platsåtkomst är också tillgänglig i en App Service-miljö som är konfigurerad med en intern belastningsutjämnare (ILB). Mer information finns i [Skapa och använda en intern belastningsutjämnare med en App Service Environment](../app-service/environment/create-ilb-ase.md).

Mer information om hur du konfigurerar privat platsåtkomst finns i [Upprätta privat platsåtkomst för Azure Functions](functions-create-private-site-access.md).

## <a name="virtual-network-integration"></a>Virtual Network-integration

Med integrering av virtuella nätverk kan funktionsappen komma åt resurser i ett virtuellt nätverk.
Azure Functions stöder två typer av integrering av virtuella nätverk:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

Integrering av virtuella nätverk i Azure Functions använder delad infrastruktur med App Service-webbappar. Mer information om de två typerna av integrering av virtuella nätverk finns i:

* [Regional integrering av virtuella nätverk](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integrering av gatewayer krävs](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Mer information om hur du konfigurerar integrering av virtuella nätverk finns i [Integrera en funktionsapp med ett virtuellt Azure-nätverk](functions-create-vnet.md).

## <a name="regional-virtual-network-integration"></a>Regional integrering av virtuella nätverk

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

## <a name="connect-to-service-endpoint-secured-resources"></a>Säkra resurser för anslutning till tjänstens slutpunkt

Om du vill ha en högre säkerhetsnivå kan du begränsa ett antal Azure-tjänster till ett virtuellt nätverk med hjälp av tjänstslutpunkter. Du måste sedan integrera din funktionsapp med det virtuella nätverket för att komma åt resursen. Den här konfigurationen stöds på alla planer som stöder integrering av virtuella nätverk.

Mer information finns i [Slutpunkter för virtuella nätverkstjänster](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Begränsa ditt lagringskonto till ett virtuellt nätverk

När du skapar en funktionsapp måste du skapa eller länka till ett Azure Storage-konto med allmänt syfte som stöder Blob-, kö- och Tabelllagring. Du kan för närvarande inte använda några begränsningar för virtuella nätverk för det här kontot. Om du konfigurerar en slutpunkt för en virtuell nätverkstjänst på det lagringskonto som du använder för funktionsappen, kommer den konfigurationen att bryta din app.

Mer information finns i [Krav på lagringskonto](./functions-create-function-app-portal.md#storage-account-requirements).

## <a name="use-key-vault-references"></a>Använda Key Vault-referenser

Du kan använda Azure Key Vault-referenser för att använda hemligheter från Azure Key Vault i ditt Azure Functions-program utan att kräva några kodändringar. Azure Key Vault är en tjänst som tillhandahåller centraliserad hemligheter hantering, med full kontroll över åtkomstprinciper och granskningshistorik.

För närvarande fungerar inte [Key Vault-referenser](../app-service/app-service-key-vault-references.md) om nyckelvalvet är skyddat med tjänstslutpunkter. Om du vill ansluta till ett nyckelvalv med hjälp av integrering av virtuella nätverk måste du anropa Key Vault i programkoden.

## <a name="virtual-network-triggers-non-http"></a>Virtuella nätverksutlösare (icke-HTTP)

För närvarande kan du använda icke-HTTP-utlösarfunktioner från ett virtuellt nätverk på ett av två sätt:

+ Kör din funktionsapp i en Premium-plan och aktivera stöd för virtuella nätverksutlösare.
+ Kör din funktionsapp i en App Service-plan eller App Service Environment.

### <a name="premium-plan-with-virtual-network-triggers"></a>Premium-plan med virtuella nätverksutlösare

När du kör en Premium-plan kan du ansluta icke-HTTP-utlösarfunktioner till tjänster som körs i ett virtuellt nätverk. För att kunna göra detta måste du aktivera stöd för stöd för virtuell nätverksutlösare för funktionsappen. **Supportinställningen för virtual network trigger** finns i [Azure-portalen](https://portal.azure.com) under **Funktionsappinställningar**.

![Växla virtuellt nätverk](media/functions-networking-options/virtual-network-trigger-toggle.png)

Du kan också aktivera utlösare av virtuella nätverk med hjälp av följande Azure CLI-kommando:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

Virtuella nätverksutlösare stöds i version 2.x och högre av funktionskörningen. Följande utlösartyper som inte är HTTP stöds.

| Anknytning | Lägsta version |
|-----------|---------| 
|[Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/) | 3.0.10 eller högre |
|[Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs)| 4.1.0 eller högre|
|[Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus)| 3.2.0 eller högre|
|[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)| 3.0.5 eller högre|
|[Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)| 2.0.0 eller högre|

> [!IMPORTANT]
> När du aktiverar stöd för virtuell nätverksutlösare skalas endast de utlösartyper som visas i föregående tabell dynamiskt med ditt program. Du kan fortfarande använda utlösare som inte finns i tabellen, men de skalas inte utöver antalet förvärmda instanser. För hela listan över [utlösare, se Utlösare och bindningar](./functions-triggers-bindings.md#supported-bindings).

### <a name="app-service-plan-and-app-service-environment-with-virtual-network-triggers"></a>App Service plan och App Service Environment med virtuella nätverksutlösare

När funktionsappen körs i antingen en App Service-plan eller i en apptjänstmiljö kan du använda icke-HTTP-utlösarfunktioner. För att dina funktioner ska kunna utlösas korrekt måste du vara ansluten till ett virtuellt nätverk med åtkomst till resursen som definierats i utlösaranslutningen.

Anta till exempel att du vill konfigurera Azure Cosmos DB så att den bara accepterar trafik från ett virtuellt nätverk. I det här fallet måste du distribuera din funktionsapp i en App Service-plan som tillhandahåller integrering av virtuella nätverk med det virtuella nätverket. Integrering gör att en funktion kan utlösas av den Azure Cosmos DB-resursen.

## <a name="hybrid-connections"></a>Hybridanslutningar

[Hybridanslutningar](../service-bus-relay/relay-hybrid-connections-protocol.md) är en funktion i Azure Relay som du kan använda för att komma åt programresurser i andra nätverk. Den ger åtkomst från din app till en programslutpunkt. Du kan inte använda den för att komma åt ditt program. Hybridanslutningar är tillgängliga för funktioner som körs i Windows i alla utom förbrukningsplanen.

Som används i Azure Functions korrelerar varje hybridanslutning till en enda TCP-värd- och portkombination. Det innebär att hybridanslutningens slutpunkt kan finnas på alla operativsystem och alla program så länge du använder en TCP-lyssningsport. Funktionen Hybridanslutningar vet inte eller tar hand om vad programprotokollet är eller vad du använder. Det ger bara nätverksåtkomst.

Mer information finns i [Dokumentationen till App Service för hybridanslutningar](../app-service/app-service-hybrid-connections.md). Samma konfigurationssteg stöder Azure Functions.

>[!IMPORTANT]
> Hybridanslutningar stöds bara i Windows-abonnemang. Linux stöds inte.

## <a name="outbound-ip-restrictions"></a>Utgående IP-begränsningar

Utgående IP-begränsningar är tillgängliga i en Premium-plan, App Service-plan eller App Service Environment. Du kan konfigurera utgående begränsningar för det virtuella nätverket där apptjänstmiljön distribueras.

När du integrerar en funktionsapp i en Premium-plan eller en App Service-plan med ett virtuellt nätverk kan appen fortfarande ringa utgående samtal till internet som standard. Genom att lägga `WEBSITE_VNET_ROUTE_ALL=1`till programinställningen tvingar du all utgående trafik att skickas till det virtuella nätverket, där nätverkssäkerhetsgruppsregler kan användas för att begränsa trafiken.

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om nätverk och Azure-funktioner:

* [Följ självstudien om hur du kommer igång med integrering av virtuella nätverk](./functions-create-vnet.md)
* [Läs vanliga frågor om funktioner i nätverk](./functions-networking-faq.md)
* [Läs mer om integrering av virtuella nätverk med App Service/Funktioner](../app-service/web-sites-integrate-with-vnet.md)
* [Läs mer om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera fler nätverksfunktioner och kontroll med App Service-miljöer](../app-service/environment/intro.md)
* [Ansluta till enskilda lokala resurser utan brandväggsändringar med hjälp av Hybridanslutningar](../app-service/app-service-hybrid-connections.md)
