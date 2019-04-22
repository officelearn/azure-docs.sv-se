---
title: Nätverksalternativ för Azure Functions
description: En översikt över alla Nätverksalternativ som är tillgängliga i Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: b7af0149a690e3cc3a357a5cb769751e3674d374
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698209"
---
# <a name="azure-functions-networking-options"></a>Nätverksalternativ för Azure Functions

Den här artikeln beskrivs de tillgängliga nätverksfunktionerna i värdalternativ för Azure Functions. Alla följande Nätverksalternativ ger vissa möjlighet att komma åt resurser utan att använda internet-dirigerbara adresser eller begränsa åtkomst till internet till en funktionsapp. 

Värdbaserade modeller har olika nivåer av isolering av nätverk som är tillgängliga. Välja rätt hjälper dig att uppfylla dina krav för isolering av nätverk.

Du kan vara värd för funktionsappar i ett par olika sätt:

* Det finns en uppsättning med alternativ som körs på en infrastruktur för flera innehavare, med olika nivåer av virtuell nätverksanslutning och skalningsalternativ:
    * Den [förbrukningsplan](functions-scale.md#consumption-plan), som kan skalas dynamiskt som svar på Läs in och erbjuder alternativ för minimal nätverk isolering.
    * Den [premiumplan](functions-scale.md#premium-plan-public-preview), som också kan skalas dynamiskt, och erbjuder mer omfattande isolering av nätverk.
    * Azure [apptjänstplan](functions-scale.md#app-service-plan), som fungerar i fasta skala och erbjuder liknande isolering av nätverk till Premium-prenumerationen.
* Du kan köra funktioner i en [App Service Environment](../app-service/environment/intro.md). Den här metoden distribuerar din funktion i ditt virtuella nätverk och erbjuder fullständig Klusternätverkets kontroll- och isolering.

## <a name="matrix-of-networking-features"></a>Matris av nätverksfunktioner

|                |[Förbrukningsplan](functions-scale.md#consumption-plan)|⚠ [Premium-abonnemang](functions-scale.md#premium-plan-public-preview)|[App Service-plan](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Inkommande IP-adressbegränsningar](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrering med virtuellt nätverk](#virtual-network-integration)|❌NO|❌NO|✅Yes|✅Yes|
|[Förhandsversionen av virtual network-integration (Azure ExpressRoute och Tjänsteslutpunkter)](#preview-version-of-virtual-network-integration)|❌NO|⚠Ja|⚠Ja|✅Yes|
|[Hybridanslutningar](#hybrid-connections)|❌NO|❌NO|✅Yes|✅Yes|
|[privat Platsåtkomst](#private-site-access)|❌NO| ❌NO|❌NO|✅Yes|

⚠ Den här förhandsversionsfunktionen är inte för användning i produktion.

## <a name="inbound-ip-restrictions"></a>Inkommande IP-adressbegränsningar

Du kan använda IP-restriktioner för att definiera en prioritet sorterad lista över IP-adresser som är tillåten/nekad åtkomst till din app. Listan kan innehålla IPv4 och IPv6-adresser. Om det finns en eller flera poster, finns en implicit ”neka alla” i slutet av listan. IP-restriktioner fungerar med alla alternativ som värd för funktionen.

> [!NOTE]
> För att använda redigeraren för Azure-portalen, kunna portalen direkt åtkomst till funktionsappen som körs. Den enhet som du använder för att få åtkomst till portalen måste också ha dess IP-godkänd. Begränsningar för nätverk på plats, kan du fortfarande använda alla funktioner på den **plattformsfunktioner** fliken.

Mer information finns i [Azure App Service statiska åtkomstbegränsningar](../app-service/app-service-ip-restrictions.md).

## <a name="virtual-network-integration"></a>Virtual Network-integration

Virtual network-integration kan funktionsappen att komma åt resurser i ett virtuellt nätverk. Den här funktionen är tillgänglig i både Premium-prenumerationen och App Service-planen. Om appen är i en App Service Environment, finns redan i ett virtuellt nätverk och kräver inte användning av virtual network-integration för att nå resurser i samma virtuella nätverk.

Virtual network-integration ger din funktionsapp tillgång till resurser i det virtuella nätverket men inte bevilja [privata Platsåtkomst](#private-site-access) till din funktionsapp från det virtuella nätverket.

Du kan använda virtual network-integration för att aktivera åtkomst från appar till databaser och webbtjänster som körs i ditt virtuella nätverk. Med virtual network-integration behöver du inte Exponerar en offentlig slutpunkt för program på den virtuella datorn. Du kan använda de privata, icke-internet dirigerbara adresserna i stället.

Den allmänt tillgängliga versionen av virtual network-integration är beroende av en VPN-gateway för att ansluta funktionsappar till ett virtuellt nätverk. Det är tillgängligt i functions som körs i en App Service-plan. Läs hur du konfigurerar den här funktionen i [integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-version-of-virtual-network-integration"></a>Förhandsversionen av virtual network-integration

En ny version av vnet-integrationsfunktionen genomgår förhandsgranskning. Den inte beroende av punkt-till-plats VPN-anslutning. Den stöder åtkomst till resurser över ExpressRoute eller Tjänsteslutpunkter. Den finns i Premium-plan och i App Service-planer som skalas till PremiumV2.

Här följer några egenskaper för den här versionen:

* Du behöver inte en gateway för att använda den.
* Du kan komma åt resurser via ExpressRoute-anslutningar utan ytterligare konfiguration utöver integrering med det ExpressRoute-anslutna virtuella nätverket.
* Du kan använda service-slutpunkt-säkrade resurser från att köra functions. Gör du genom att aktivera tjänstslutpunkter på det undernät som används för virtual network-integration.
* Du kan inte konfigurera utlösare för att använda service-slutpunkt-skyddade resurser. 
* Både funktionsappen och det virtuella nätverket måste vara i samma region.
* Den nya funktionen kräver en oanvända undernät i det virtuella nätverket som du har distribuerat via Azure Resource Manager.
* Produktionsarbetsbelastningar stöds inte eftersom funktionen är en förhandsversion.
* Routningstabeller och global peering är inte tillgängliga än med funktionen.
* En adress används för varje potentiell instans av en funktionsapp. Eftersom du inte kan ändra undernätsstorlek på efter tilldelning, Använd ett undernät som enkelt kan hantera storleken på din maximal skala. Till exempel för att stödja en premiumplan som kan skalas till 80 instanser, rekommenderar vi en `/25` undernät som innehåller 126 värdadresser.

Mer information om hur du använder förhandsversionen av virtual network-integration finns [integrera en funktionsapp med en Azure-nätverk](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Hybridanslutningar

[Hybridanslutningar](../service-bus-relay/relay-hybrid-connections-protocol.md) är en funktion i Azure Relay som du kan använda för att få åtkomst till resurser i andra nätverk. Det ger åtkomst från din app till en programslutpunkt. Du kan inte använda den för att komma åt ditt program. Hybrid Connections är tillgängliga för funktioner som körs en [apptjänstplan](functions-scale.md#app-service-plan) och en [App Service Environment](../app-service/environment/intro.md).

Som används i Azure Functions, motsvarar varje hybridanslutning en enskild kombination av TCP-värd och port. Det innebär att hybrid-Anslutningens slutpunkt kan vara på alla operativsystem och alla program, så länge som du försöker komma åt en lyssnande TCP-port. Funktionen Hybridanslutningar vet inte eller hand programprotokoll är och vad du försöker komma åt. Den innehåller bara åtkomst till nätverket.

Mer information finns i den [dokumentation om App Service för Hybridanslutningar](../app-service/app-service-hybrid-connections.md), som har stöd för funktioner i en App Service-plan.

## <a name="private-site-access"></a>Åtkomst till privat plats

Åtkomst till privata webbplatsen avser gör appen tillgänglig endast från ett privat nätverk som från inom en Azure-nätverk. Åtkomst till privata webbplatsen är tillgänglig endast med en App Service Environment som konfigurerats med en intern belastningsutjämnare (ILB). Mer information finns i [skapa och använda en intern belastningsutjämnare med en App Service Environment](../app-service/environment/create-ilb-ase.md).

Det finns många sätt att komma åt resurser i andra värdalternativ för virtuella nätverk. Men en App Service Environment är det enda sättet att tillåta utlösare för en funktion ska ske via ett virtuellt nätverk.

## <a name="next-steps"></a>Nästa steg
Mer information om nätverksprodukter och Azure Functions: 

* [Kursen om att komma igång med virtual network-integration](./functions-create-vnet.md)
* [Läs de funktioner som nätverk vanliga frågor och svar](./functions-networking-faq.md)
* [Mer information om virtual network-integration med App Service/funktioner](../app-service/web-sites-integrate-with-vnet.md)
* [Mer information om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera flera nätverk funktioner och kontroll med App Service-miljöer](../app-service/environment/intro.md)
* [Ansluta till enskilda lokala resurser utan ändringar av brandvägg med hjälp av Hybridanslutningar](../app-service/app-service-hybrid-connections.md)
