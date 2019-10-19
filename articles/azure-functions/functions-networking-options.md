---
title: Azure Functions nätverks alternativ
description: En översikt över alla nätverks alternativ som är tillgängliga i Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 967988d802a1b3d33ff50f578650e44794015583
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550859"
---
# <a name="azure-functions-networking-options"></a>Azure Functions nätverks alternativ

I den här artikeln beskrivs de nätverksfunktioner som är tillgängliga i värd alternativen för Azure Functions. Alla följande nätverks alternativ ger viss möjlighet att komma åt resurser utan att använda Internet adresser som inte är flyttbara eller begränsa Internet åtkomst till en Function-app. 

Värd modellerna har olika nivåer av nätverks isolering tillgänglig. Genom att välja rätt kan du uppfylla kraven på nätverks isolering.

Du kan vara värd för funktions appar på ett par olika sätt:

* Det finns en uppsättning plan alternativ som körs i en infrastruktur för flera innehavare, med olika nivåer av anslutningar för virtuella nätverk och skalnings alternativ:
    * [Förbruknings planen](functions-scale.md#consumption-plan), som kan skalas dynamiskt som svar på belastning och erbjuder minimala alternativ för nätverks isolering.
    * [Premium-planen](functions-scale.md#premium-plan), som också skalas dynamiskt, samtidigt som den erbjuder mer omfattande nätverks isolering.
    * Azure- [App Service plan](functions-scale.md#app-service-plan), som fungerar med en fast skala och erbjuder liknande nätverks isolering för Premium planen.
* Du kan köra funktioner i en [App Service-miljön](../app-service/environment/intro.md). Den här metoden distribuerar din funktion till det virtuella nätverket och ger fullständig nätverks kontroll och isolering.

## <a name="matrix-of-networking-features"></a>Matris med nätverksfunktioner

|                |[Förbruknings plan](functions-scale.md#consumption-plan)|[Premium-plan (för hands version)](functions-scale.md#premium-plan)|[App Service-plan](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Inkommande IP-begränsningar & åtkomst till privata platser](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrering med virtuellt nätverk](#virtual-network-integration)|❌No|✅Yes (regional)|✅Yes (regional och gateway)|✅Yes|
|[Virtuella nätverks utlösare (icke-HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[Hybridanslutningar](#hybrid-connections)|❌No|❌No|✅Yes|✅Yes|
|[Utgående IP-begränsningar](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|


## <a name="inbound-ip-restrictions"></a>Inkommande IP-begränsningar

Du kan använda IP-begränsningar för att definiera en prioriterad lista med IP-adresser som tillåts/nekas åtkomst till din app. Listan kan innehålla IPv4-och IPv6-adresser. När det finns en eller flera poster finns implicit "Neka alla" i slutet av listan. IP-begränsningar fungerar med alla funktions värd alternativ.

> [!NOTE]
> Med nätverks begränsningar på plats kan du bara använda Portal redigeraren i det virtuella nätverket eller när du har vit listas IP-adressen för den dator som du använder för att få åtkomst till Azure Portal. Men du kan fortfarande komma åt funktioner på fliken **plattforms funktioner** från vilken dator som helst.

Läs mer i [Azure App Service statiska åtkomst begränsningar](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Åtkomst till privat plats

Åtkomst till privata webbplatser syftar bara på att göra appen tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. 
* Åtkomst till privata webbplatser är tillgängligt i [Premium](./functions-premium-plan.md), [förbrukning], (Function-Scale. MD # förbrukning-plan) och [App Service plan](functions-scale.md#app-service-plan) när **tjänst slut punkter** konfigureras. 
    * Tjänstens slut punkter kan konfigureras per app under plattforms funktioner > nätverks > Konfigurera åtkomst begränsningar > Lägg till regel. Virtuella nätverk kan väljas nu som "typ" för en regel.
    * Mer information finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md)
        * Tänk på att med tjänst slut punkter har din funktion fortfarande fullständig utgående åtkomst till Internet, även om du har konfigurerat Virtual Network-integrering.
* Åtkomst till privata webbplatser är också tillgänglig med en App Service-miljön som kon figurer ATS med en intern belastningsutjämnare (ILB). Mer information finns i [skapa och använda en intern belastningsutjämnare med en app service-miljön](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Virtual Network-integration

Med integrering med virtuella nätverk får du till gång till resurser i ett virtuellt nätverk med hjälp av din Function-app. Den här funktionen är tillgänglig i både Premium-och App Service plan. Om din app finns i ett App Service-miljön är den redan i ett virtuellt nätverk och kräver inte att integrering med virtuella nätverk används för att komma åt resurser i samma virtuella nätverk.

Du kan använda integrering med virtuella nätverk för att ge åtkomst från appar till databaser och webb tjänster som körs i det virtuella nätverket. Med Virtual Network-integrering behöver du inte exponera en offentlig slut punkt för program på den virtuella datorn. Du kan använda privata, icke-Internet-dirigerbart adresser i stället.

Det finns två formulär för funktionen för integrering av virtuella nätverk

1. Regional integrering av virtuella nätverk möjliggör integrering med virtuella nätverk i samma region. Den här typen av funktion kräver ett undernät i ett virtuellt nätverk i samma region. Den här funktionen är fortfarande i för hands version men stöds för arbets belastningar för Windows-programproduktioner med vissa villkor som anges nedan.
2. Gateway krävs virtuell nätverks integrering möjliggör integrering med virtuella nätverk i fjärrregioner eller med klassiska virtuella nätverk. Den här versionen av funktionen kräver distribution av en Virtual Network Gateway till ditt VNet. Det här är den punkt-till-plats-baserade VPN-baserade funktionen och stöds bara med Windows-appar.

En app kan bara använda en form av funktionen VNet-integrering i taget. Frågan är sedan vilken funktion du ska använda. Du kan använda antingen för många saker. De tydliga differentieringarna är:

| Problem  | Lösning | 
|----------|----------|
| Vill komma åt en RFC 1918-adress (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) i samma region | regional VNet-integrering |
| Vill du komma åt resurser i ett klassiskt VNet eller ett VNet i en annan region | Gateway krävs VNet-integrering |
| Vill komma åt RFC 1918-slutpunkter över ExpressRoute | regional VNet-integrering |
| Vill du uppnå resurser över tjänst slut punkter | regional VNet-integrering |

Ingen av funktionerna gör att du kan komma åt icke-RFC 1918-adresser i ExpressRoute. Om du vill göra det måste du använda en ASE för tillfället.

Att använda regional VNet-integrering ansluter inte ditt VNet till lokala eller konfigurera tjänst slut punkter. Det är en separat nätverks konfiguration. Den regionala VNet-integrationen gör det enkelt för din app att ringa upp samtal mellan dessa anslutnings typer.

Oavsett vilken version som används ger VNet-integration din app-åtkomst till resurser i det virtuella nätverket, men beviljar inte privat plats åtkomst till din Function-app från det virtuella nätverket. Åtkomst till privata webbplatser avser att göra din app endast tillgänglig från ett privat nätverk, till exempel från ett virtuellt Azure-nätverk. VNet-integrering är bara för att göra utgående samtal från din app till ditt VNet. 

Funktionen för VNet-integrering:

* Kräver en standard-, Premium-eller PremiumV2-App Service plan
* stöder TCP och UDP
* fungerar med App Service appar och Function-appar

Det finns vissa saker som VNet-integrering inte stöder, inklusive:

* Montering av en enhet
* AD-integrering 
* NetBios

Integrering av virtuella nätverk i functions använder delad infrastruktur med App Service Web Apps. Läs mer om de två typerna av integrering av virtuella nätverk i:
* [Regional VNET-integration](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Gateway krävs VNet-integrering](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Mer information om hur du använder integrering med virtuella nätverk finns i [integrera en Function-app med ett virtuellt Azure-nätverk](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Ansluter till tjänst slut punktens säkra resurser

> [!note] 
> Tillfälligt kan det ta upp till 12 timmar innan nya tjänst slut punkter blir tillgängliga för din Function-app när du konfigurerar åtkomst begränsningar för den underordnade resursen. Under den här tiden är resursen helt otillgänglig för din app.

För att tillhandahålla en högre säkerhets nivå kan du begränsa ett antal Azure-tjänster till ett virtuellt nätverk med hjälp av tjänst slut punkter. Du måste sedan integrera din Function-app med det virtuella nätverket för att få åtkomst till resursen. Den här konfigurationen stöds på alla planer som stöder integrering av virtuella nätverk.

[Läs mer om tjänst slut punkter för virtuella nätverk här.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Begränsa ditt lagrings konto till ett virtuellt nätverk
När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring. Det går för närvarande inte att använda några begränsningar för virtuella nätverk på det här kontot. Om du konfigurerar en tjänst slut punkt för virtuellt nätverk på det lagrings konto som du använder för din Function-app kommer appen att brytas.

[Läs mer om krav för lagrings konton här.](./functions-create-function-app-portal.md#storage-account-requirements
) 

## <a name="virtual-network-triggers-non-http"></a>Virtuella nätverks utlösare (icke-HTTP)

För att kunna använda andra funktions utlösare än HTTP i ett virtuellt nätverk måste du köra din Function-app i en App Service plan eller i en App Service-miljön.

Om du till exempel vill konfigurera Azure Cosmos DB för att endast acceptera trafik från ett virtuellt nätverk måste du distribuera din Function-app i en app service-plan med virtuell nätverks integrering med det virtuella nätverket för att konfigurera Azure Cosmos DB utlösare från den resursen. Under för hands versionen tillåter konfiguration av VNET-integrering inte att Premium planen utlöses av den Azure Cosmos DB resursen.

Kontrol lera [den här listan för alla icke-http-utlösare](./functions-triggers-bindings.md#supported-bindings) för att kontrol lera vad som stöds.

## <a name="hybrid-connections"></a>Hybridanslutningar

[Hybridanslutningar](../service-bus-relay/relay-hybrid-connections-protocol.md) är en funktion i Azure Relay som du kan använda för att få åtkomst till program resurser i andra nätverk. Den ger åtkomst från din app till en program slut punkt. Du kan inte använda den för att få åtkomst till ditt program. Hybridanslutningar är tillgängligt för funktioner som körs i en [App Service plan](functions-scale.md#app-service-plan) och en [App Service-miljön](../app-service/environment/intro.md).

Som används i Azure Functions motsvarar varje hybrid anslutning en enskild TCP-värd och port-kombination. Det innebär att hybrid anslutningens slut punkt kan finnas i alla operativ system och program, så länge som du har åtkomst till en TCP-lyssnings port. Hybridanslutningar funktionen känner inte igen eller bryr sig om vad applikations protokollet är eller vad du använder. Det ger bara nätverks åtkomst.

Mer information finns i [App Service dokumentationen för hybridanslutningar](../app-service/app-service-hybrid-connections.md), som stöder funktioner i en app service plan.

## <a name="outbound-ip-restrictions"></a>Utgående IP-begränsningar

Utgående IP-begränsningar är bara tillgängliga för funktioner som distribueras till en App Service-miljön. Du kan konfigurera utgående begränsningar för det virtuella nätverk där App Service-miljön har distribuerats.

När du integrerar en Function-app i en Premium-plan eller App Service plan med ett virtuellt nätverk kan appen fortfarande göra utgående anrop till Internet.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om nätverk och Azure Functions: 

* [Följ självstudien om att komma igång med integrering av virtuella nätverk](./functions-create-vnet.md)
* [Läs vanliga frågor och svar om funktionen Networking](./functions-networking-faq.md)
* [Lär dig mer om virtuell nätverks integrering med App Service/Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Lär dig mer om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera fler nätverksfunktioner och kontroll funktioner med App Service miljöer](../app-service/environment/intro.md)
* [Anslut till enskilda lokala resurser utan brand Väggs ändringar med hjälp av Hybridanslutningar](../app-service/app-service-hybrid-connections.md)
