---
title: Azure Functions nätverks alternativ
description: En översikt över alla nätverks alternativ som är tillgängliga i Azure Functions.
author: alexkarcher-msft
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 34759077bd7223d17fea70d32bda63fd1b2595eb
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668129"
---
# <a name="azure-functions-networking-options"></a>Azure Functions nätverks alternativ

I den här artikeln beskrivs de nätverksfunktioner som är tillgängliga i värd alternativen för Azure Functions. Alla följande nätverks alternativ ger dig möjlighet att komma åt resurser utan att använda Internet-dirigerbart adresser, eller för att begränsa Internet åtkomst till en Function-app.

Värd modellerna har olika nivåer av nätverks isolering tillgänglig. Genom att välja rätt kan du uppfylla kraven på nätverks isolering.

Du kan vara värd för funktions appar på ett par olika sätt:

* Det finns en uppsättning plan alternativ som körs på en infrastruktur för flera innehavare, med olika nivåer av anslutningar för virtuella nätverk och skalnings alternativ:
    * [Förbruknings planen](functions-scale.md#consumption-plan), som kan skalas dynamiskt som svar på belastning och erbjuder minimala alternativ för nätverks isolering.
    * [Premium-planen](functions-scale.md#premium-plan), som också skalas dynamiskt, samtidigt som den erbjuder mer omfattande nätverks isolering.
    * Azure- [App Service plan](functions-scale.md#app-service-plan), som fungerar med en fast skala och erbjuder liknande nätverks isolering som Premium abonnemang.
* Du kan köra funktioner i en [App Service-miljön](../app-service/environment/intro.md). Den här metoden distribuerar din funktion till det virtuella nätverket och ger fullständig nätverks kontroll och isolering.

## <a name="matrix-of-networking-features"></a>Matris med nätverksfunktioner

|                |[Förbruknings plan](functions-scale.md#consumption-plan)|[Premium-plan](functions-scale.md#premium-plan)|[App Service-plan](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Inkommande IP-begränsningar & åtkomst till privata platser](#inbound-ip-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integrering med virtuellt nätverk](#virtual-network-integration)|❌nej|✅Ja (regional)|✅Ja (regional och gateway)|✅Ja|
|[Virtuella nätverks utlösare (icke-HTTP)](#virtual-network-triggers-non-http)|❌nej| ❌nej|✅Ja|✅Ja|
|[Hybridanslutningar](#hybrid-connections)|❌nej|✅Ja|✅Ja|✅Ja|
|[Utgående IP-begränsningar](#outbound-ip-restrictions)|❌nej| ❌nej|❌nej|✅Ja|

## <a name="inbound-ip-restrictions"></a>Inkommande IP-begränsningar

Du kan använda IP-begränsningar för att definiera en prioriterad lista över IP-adresser som tillåts eller nekas åtkomst till din app. Listan kan innehålla IPv4-och IPv6-adresser. När det finns en eller flera poster finns implicit "Neka alla" i slutet av listan. IP-begränsningar fungerar med alla funktions värd alternativ.

> [!NOTE]
> Med nätverks begränsningar på plats kan du bara använda Portal redigeraren från det virtuella nätverket eller när du har placerat IP-adressen för den dator som du använder för att komma åt Azure Portal i listan Betrodda mottagare. Men du kan fortfarande komma åt funktioner på fliken **plattforms funktioner** från vilken dator som helst.

Läs mer i [Azure App Service statiska åtkomst begränsningar](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Åtkomst till privat plats

Åtkomst till privata webbplatser syftar bara på att göra appen tillgänglig från ett privat nätverk, till exempel ett virtuellt Azure-nätverk.

* Åtkomst till privata webbplatser är tillgängligt i [Premium](./functions-premium-plan.md)-, [konsumtions](functions-scale.md#consumption-plan)-och [app Services](functions-scale.md#app-service-plan) planerna när tjänstens slut punkter konfigureras.
    * Tjänstens slut punkter kan konfigureras per app under **plattforms funktioner** > **nätverks** > **Konfigurera åtkomst begränsningar** > **Lägg till regel**. Virtuella nätverk kan nu väljas som regel typ.
    * Mer information finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md).
    * Tänk på att med tjänst slut punkter har din funktion fortfarande fullständig utgående åtkomst till Internet, även om du har konfigurerat Virtual Network-integrering.
* Åtkomst till privata webbplatser är också tillgänglig i en App Service-miljön som har kon figurer ATS med en intern belastningsutjämnare (ILB). Mer information finns i [skapa och använda en intern belastningsutjämnare med en app service-miljön](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Virtual Network-integration

Med integrering med virtuella nätverk får du till gång till resurser i ett virtuellt nätverk med hjälp av din Function-app. Den här funktionen är tillgänglig i både Premium-och App Service plan. Om din app finns i ett App Service-miljön är den redan i ett virtuellt nätverk och kräver inte integrering av virtuella nätverk för att komma åt resurser i samma virtuella nätverk.

Du kan använda integrering med virtuella nätverk för att ge åtkomst från appar till databaser och webb tjänster som körs i det virtuella nätverket. Med Virtual Network-integrering behöver du inte exponera en offentlig slut punkt för program på den virtuella datorn. Du kan använda privata, icke-Internet-dirigerbart adresser i stället.

Det finns två former av integrering av virtuella nätverk:

+ **Regional integrering av virtuella nätverk (för hands version)** : möjliggör integrering med virtuella nätverk i samma region. Den här typen av integrering kräver ett undernät i ett virtuellt nätverk i samma region. Den här funktionen är fortfarande i för hands version, men stöds för Function-appar som körs i Windows, med de varningar som beskrivs efter följande problem/lösning-tabell.
+ **Gateway krävs virtuell nätverks integrering**: möjliggör integrering med virtuella nätverk i fjärrregioner eller med klassiska virtuella nätverk. Den här typen av integrering kräver distribution av en virtuell nätverksgateway till ditt VNet. Det här är en punkt-till-plats-VPN-baserad funktion som endast stöds för Function-appar som körs i Windows.

En app kan bara använda en typ av funktionen för integrering av virtuella nätverk i taget. Även om båda är användbara för många scenarier anger följande tabell var varje ska användas:

| Problem  | Lösning |
|----------|----------|
| Vill komma åt en RFC 1918-adress (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) i samma region | Regional integrering av virtuella nätverk |
| Vill du komma åt resurser i ett klassiskt virtuellt nätverk eller ett virtuellt nätverk i en annan region | Gateway krävs för virtuell nätverks integrering |
| Vill komma åt RFC 1918-slutpunkter i Azure ExpressRoute | Regional integrering av virtuella nätverk |
| Vill du uppnå resurser över tjänst slut punkter | Regional integrering av virtuella nätverk |

Ingen av funktionerna gör att du kan komma åt icke-RFC 1918-adresser i ExpressRoute. För att göra det måste du för närvarande använda en App Service-miljön.

Genom att använda regional integrering av virtuella nätverk ansluter du inte ditt virtuella nätverk till lokala slut punkter eller konfigurerar tjänst slut punkter. Det är en separat nätverks konfiguration. Regional integrering av virtuella nätverk gör det möjligt för din app att göra anrop mellan dessa anslutnings typer.

Oavsett vilken version som används ger integrering av virtuella nätverk din app-åtkomst till resurser i det virtuella nätverket, men beviljar inte privat plats åtkomst till din funktions app från det virtuella nätverket. Åtkomst till privata webbplatser innebär att endast appen kan nås från ett privat nätverk som ett virtuellt Azure-nätverk. integrering med virtuella nätverk är bara för att göra utgående samtal från din app till ditt virtuella nätverk.

Funktionen för integrering av virtuella nätverk:

* Kräver en standard-, Premium-eller PremiumV2-App Service plan
* stöder TCP och UDP
* Fungerar med App Service appar och Functions-appar

Det finns vissa saker som Virtual Network-integration inte stöder, inklusive:

* Montering av en enhet
* Active Directory-integrering
* NetBIOS

Integrering av virtuella nätverk i Azure Functions använder delad infrastruktur med App Service Web Apps. Mer information om de två typerna av integrering med virtuella nätverk finns i:

* [Regional integrering av virtuella nätverk](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway krävs för virtuell nätverks integrering](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Mer information om hur du använder integrering med virtuella nätverk finns i [integrera en Function-app med ett virtuellt Azure-nätverk](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Ansluter till tjänst slut punktens säkra resurser

> [!NOTE]
> Det kan nu ta upp till 12 timmar innan nya tjänst slut punkter blir tillgängliga för din Function-app när du har konfigurerat åtkomst begränsningar för den underordnade resursen. Under den här tiden är resursen helt otillgänglig för din app.

För att ge en högre säkerhets nivå kan du begränsa ett antal Azure-tjänster till ett virtuellt nätverk med hjälp av tjänstens slut punkter. Du måste sedan integrera din Function-app med det virtuella nätverket för att få åtkomst till resursen. Den här konfigurationen stöds på alla planer som stöder integrering av virtuella nätverk.

[Läs mer om tjänst slut punkter för virtuella nätverk.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Begränsa ditt lagrings konto till ett virtuellt nätverk

När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring. Du kan för närvarande inte använda några begränsningar för virtuella nätverk för det här kontot. Om du konfigurerar en tjänst slut punkt för virtuellt nätverk på det lagrings konto som du använder för din Function-app, kommer din app att brytas.

[Läs mer om krav för lagrings konton.](./functions-create-function-app-portal.md#storage-account-requirements)

## <a name="virtual-network-triggers-non-http"></a>Virtuella nätverks utlösare (icke-HTTP)

För att använda andra funktions utlösare än HTTP i ett virtuellt nätverk måste du köra din Function-app i en App Service plan eller i en App Service-miljön.

Anta till exempel att du vill konfigurera Azure Cosmos DB att endast acceptera trafik från ett virtuellt nätverk. Du måste distribuera din Function-app i en app service-plan som tillhandahåller virtuell nätverks integrering med det virtuella nätverket för att kunna konfigurera Azure Cosmos DB utlösare från den resursen. Under för hands versionen tillåter konfiguration av integrering av virtuella nätverk inte att Premium-planen utlöses av den Azure Cosmos DB resursen.

Se [listan för alla icke-http-utlösare](./functions-triggers-bindings.md#supported-bindings) för att kontrol lera vad som stöds.

## <a name="hybrid-connections"></a>Hybridanslutningar

[Hybridanslutningar](../service-bus-relay/relay-hybrid-connections-protocol.md) är en funktion i Azure Relay som du kan använda för att få åtkomst till program resurser i andra nätverk. Den ger åtkomst från din app till en program slut punkt. Du kan inte använda den för att få åtkomst till ditt program. Hybridanslutningar är tillgängligt för funktioner som körs i alla utom förbruknings planen.

Som används i Azure Functions motsvarar varje hybrid anslutning en enskild TCP-värd och port-kombination. Det innebär att hybrid anslutningens slut punkt kan finnas på alla operativ system och program så länge som du har åtkomst till en TCP-lyssnings port. Hybridanslutningar funktionen känner inte igen eller bryr sig om vad applikations protokollet är eller vad du använder. Den ger bara nätverks åtkomst.

Mer information finns i [App Service dokumentationen för hybridanslutningar](../app-service/app-service-hybrid-connections.md). Samma konfigurations steg har stöd för Azure Functions.

## <a name="outbound-ip-restrictions"></a>Utgående IP-begränsningar

Utgående IP-begränsningar är bara tillgängliga för funktioner som distribueras till en App Service-miljön. Du kan konfigurera utgående begränsningar för det virtuella nätverk där App Service-miljön har distribuerats.

När du integrerar en Function-app i en Premium-plan eller en App Service plan med ett virtuellt nätverk kan appen fortfarande göra utgående anrop till Internet.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om nätverk och Azure Functions:

* [Följ självstudien om att komma igång med integrering av virtuella nätverk](./functions-create-vnet.md)
* [Läs vanliga frågor och svar om funktionen Networking](./functions-networking-faq.md)
* [Lär dig mer om virtuell nätverks integrering med App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Lär dig mer om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera fler nätverksfunktioner och kontroll funktioner med App Service miljöer](../app-service/environment/intro.md)
* [Anslut till enskilda lokala resurser utan brand Väggs ändringar med hjälp av Hybridanslutningar](../app-service/app-service-hybrid-connections.md)
