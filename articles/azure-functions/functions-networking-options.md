---
title: Nätverksalternativ för Azure Functions
description: En översikt över alla Nätverksalternativ som är tillgängliga i Azure Functions
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: conceptual
ms.date: 1/14/2019
ms.author: alkarche
ms.openlocfilehash: 10d7daa6da45c56e20c622fcbca9ee288e737dab
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358148"
---
# <a name="azure-functions-networking-options"></a>Nätverksalternativ för Azure Functions

Det här dokumentet beskriver suite nätverksfunktioner som är tillgängliga i Azure-funktioner som är värd för alternativ. Alla följande Nätverksalternativ ger vissa möjlighet att komma åt resurser utan att använda internet-dirigerbara adresser eller begränsa åtkomst till internet till en Funktionsapp. De värdbaserade modellerna som alla har olika nivåer av isolering av nätverk som är tillgängliga och välja rätt gör att du uppfyller dina krav för isolering av nätverk.

Funktionsappar kan finnas på flera olika sätt.

* Det finns en uppsättning alternativ som körs på flera innehavare infrastruktur med olika nivåer av VNET-anslutning och skalningsalternativ.
    1. Med förbrukningsplanen som kan skalas dynamiskt som svar på Läs in och erbjuder alternativ för minimal nätverk isolering.
    1. Till Premium Plan, vilket också skalas dynamiskt, och erbjuder mer omfattande isolering av nätverk.
    1. App Service-planen, som fungerar på en fast skala och erbjuder liknande isolering av nätverk till Premium-prenumerationen.
* Functions kan även köras på en App Service Environment (ASE) som distribuerar din funktion i ditt virtuella nätverk och erbjuder fullständig Klusternätverkets kontroll- och isolering.

## <a name="networking-feature-matrix"></a>Nätverk funktionen matris

|                |[Förbrukningsplan](functions-scale.md#consumption-plan)|⚠ [Premium-abonnemang](functions-scale.md##premium-plan-public-preview)|[App Service-plan](functions-scale.md#app-service-plan)|[App Service Environment](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[**Inkommande IP-restriktioner**](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[**VNET-integrering**](#vnet-integration)|❌NO|⚠ Ja|✅Yes|✅Yes|
|[**Förhandsversion av VNET-Integration (Express Route & Tjänsteslutpunkter)**](#preview-vnet-integration)|❌NO|⚠ Ja|⚠ Ja|✅Yes|
|[**Hybridanslutningar**](#hybrid-connections)|❌NO|❌NO|✅Yes|✅Yes|
|[**Privat åtkomst till webbplatsen**](#private-site-access)|❌NO| ❌NO|❌NO|✅Yes|

⚠ Förhandsversion av funktionen, inte för användning i produktion

## <a name="inbound-ip-restrictions"></a>Inkommande IP-restriktioner

IP-restriktioner kan du definiera en prioritet sorterade tillåta/Neka lista med IP-adresser som ska kunna komma åt din app. Listan över tillåtna kan innehålla IPv4 och IPv6-adresser. När det finns en eller flera poster, är det en implicit neka allt som finns i slutet av listan. Begränsningar för IP-funktionen fungerar med alla funktionen som är värd för alternativ.

> [!NOTE]
> Om du vill kunna använda Azure portal redigeraren portalen måste kunna få direkt åtkomst till funktionsappen som körs och den enhet som du använder för åtkomst till portalen måste ha sin IP-godkänd. Begränsningar för nätverk på plats, kan du fortfarande använda alla funktioner i den **plattformsfunktioner** fliken.

[Läs mer här](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)

## <a name="vnet-integration"></a>VNET-integrering

VNET-integrering kan funktionsappen att komma åt resurser i ett virtuellt nätverk. VNET-integrering är tillgängligt i både premiumplan och App Service-plan. Om din app finns i en App Service Environment, sedan den redan i ett virtuellt nätverk och kräver inte användning av funktionen VNet-integrering för att nå resurser i samma virtuella nätverk.

VNet-integrering ger din funktionsapp tillgång till resurser i det virtuella nätverket men inte bevilja [privata Platsåtkomst](#private-site-access) till din funktionsapp från det virtuella nätverket.

VNet-integrering används ofta för att aktivera åtkomst från appar till en databaser och webbtjänster som körs i ditt virtuella nätverk. Med VNet-integrering behöver du inte Exponerar en offentlig slutpunkt för program på den virtuella datorn men kan använda privata adresser för andra program än internet-dirigerbara i stället.

Den allmänt tillgängliga versionen av VNET-integrering är beroende av en VPN-gateway för att ansluta Funktionsappar till ett virtuellt nätverk. Det är tillgängligt i Functions som körs i en app service-plan. Information om hur du konfigurerar den här funktionen finns i den [App Service-dokumentet för samma funktioner](../app-service/web-sites-integrate-with-vnet.md#enabling-vnet-integration).

### <a name="preview-vnet-integration"></a>Förhandsversion av VNET-integrering

Det finns en ny version av funktionen för VNet-integrering som finns i en förhandsversion. Den beroende av inte punkt-till-plats VPN-anslutning och stöder också åtkomst till resurser över ExpressRoute eller Tjänsteslutpunkter. Den här funktionen är tillgänglig i Premium-prenumerationen och i App Service-planer som skalas till PremiumV2.

Den nya versionen av VNet-integrering som finns för närvarande i förhandsversion, ger följande fördelar:

* Ingen gateway krävs för att använda den nya funktionen för VNet-integrering
* Du kan komma åt resurser över ExpressRoute-anslutningar utan någon ytterligare konfiguration utöver att integrera med ExpressRoute anslutna virtuella nätverk.
* Du kan använda tjänstslutpunkt skyddade resurser från att köra Functions. Gör du genom att aktivera tjänstslutpunkter på det undernät som används för VNet-integrering.
* Du kan inte konfigurera utlösare för att använda tjänsteslutpunkt skyddade resurser med hjälp av den nya funktionen för VNet-integrering. 
* Både funktionsappen och det virtuella nätverket måste vara i samma region.
* Den nya funktionen kräver en oanvända undernät i ditt VNet i Resource Manager.
* Produktionsarbetsbelastningar stöds inte på den nya versionen av VNet-integrering i förhandsversion.
* Routningstabeller och global peering är inte tillgängliga än med den nya VNet-integrering.
* En adress används för varje potentiell funktionen app-instansen. Eftersom undernätets storlek inte kan ändras efter tilldelning, kan du använda ett undernät som enkelt kan hantera storleken på din maximal skala. Till exempel för att stödja en premiumplan som kan skalas till 80 instanser, rekommenderar vi en `/25` undernät som innehåller 126 värdadresser.

Mer information om hur du använder förhandsversionen VNET-integrering finns [integrera en funktionsapp med Azure Virtual Network](functions-create-vnet.md).

## <a name="hybrid-connections"></a>Hybridanslutningar

[Hybridanslutningar](../service-bus-relay/relay-hybrid-connections-protocol.md) är en funktion i Azure Relay som kan användas för att få åtkomst till resurser i andra nätverk. Det ger åtkomst från din app till en programslutpunkt. Det kan inte användas för åtkomst till ditt program. Hybrid Connections är tillgängliga för funktioner som körs en [apptjänstplan](functions-scale.md#app-service-plan) och en [App Service Environment](../app-service/environment/intro.md).

Som används i funktionerna analyseras motsvarar varje Hybridanslutning en enskild kombination av TCP-värd och port. Det innebär att Hybrid Anslutningens slutpunkt kan vara på alla operativsystem och alla program som du ansluter till en TCP-lyssningsporten. Funktionen Hybridanslutningar vet inte eller hand programprotokoll är eller vilka du använder. Det är bara att tillhandahålla åtkomst till nätverket.

Mer information finns i den [dokumentation om App Service för Hybridanslutningar](../app-service/app-service-hybrid-connections.md), som stöder både Functions och Webbappar.

## <a name="private-site-access"></a>Privat åtkomst till webbplatsen

Åtkomst till privata webbplatsen refererar till att appen endast kan nås från ett privat nätverk som från inom en Azure-nätverk. Privat plats åtkomsten är endast tillgänglig med en ASE som konfigurerats med en intern belastningsutjämnaren (ILB). Mer information om hur du använder en ILB ASE finns i [skapa och använda en ILB ASE](../app-service/environment/create-ilb-ase.md).

Det finns många sätt att få åtkomst till VNET-resurser i andra värdalternativ, men en ASE är det enda sättet att tillåta utlösare för en funktion ska ske via ett virtuellt nätverk.
