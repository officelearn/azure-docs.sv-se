---
title: Vanliga frågor och svar om nätverk i Azure Functions
description: Svar på några av de vanligaste frågorna och scenarier för nätverk med Azure Functions.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: b673e625e1a50c32e3d8580ec442792ed8611703
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64572592"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Vanliga frågor och svar om nätverk i Azure Functions

Den här artikeln innehåller vanliga frågor och svar om nätverk i Azure Functions. En mer omfattande översikt finns i [fungerar Nätverksalternativ](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hur ställer jag en statisk IP-adress i Functions?

Distribuera en funktion i en App Service Environment är för närvarande det enda sättet att ha en statisk inkommande och utgående IP-adress för din funktion. Mer information om hur du använder en App Service Environment som börjar med artikeln [skapa och använda en intern belastningsutjämnare med en App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hur begränsar internet-åtkomst till min funktion?

Du kan begränsa Internetåtkomst på ett par olika sätt:

* [IP-restriktioner](../app-service/app-service-ip-restrictions.md): Begränsa inkommande trafik till din funktionsapp av IP-intervall.
    * Under IP-restriktioner, du kan också konfigurera [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md), som begränsar din funktion så att endast acceptera inkommande trafik från ett visst virtuellt nätverk.
* Borttagning av alla HTTP-utlösare. För vissa program är det tillräckligt för att bara undvika HTTP-utlösare och använda andra händelsekällan för att utlösa din funktion.

Tänk på att redigeraren för Azure-portalen kräver direkt åtkomst till din funktion som körs. Kodändringar via Azure-portalen kräver den enhet som du använder för att bläddra på portalen om du vill att dess IP-godkänd. Men du kan fortfarande använda något under fliken plattform funktioner med nätverksbegränsningar på plats.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hur begränsar jag mina function-app till ett virtuellt nätverk?

Du kan begränsa **inkommande** trafik för en funktionsapp till ett virtuellt nätverk med hjälp av [tjänstslutpunkter](./functions-networking-options.md#private-site-access). Den här konfigurationen kan fortfarande appen att göra utgående anrop till internet.

Det enda sättet att helt begränsa en funktion så att all trafik som flödar genom ett virtuellt nätverk är att använda en internt belastningsutjämnad App Service Environment. Det här alternativet distribuerar din webbplats på en dedikerad infrastruktur i ett virtuellt nätverk och skickar alla utlösare och trafik via det virtuella nätverket. 

Mer information om hur du använder en App Service Environment som börjar med artikeln [skapa och använda en intern belastningsutjämnare med en App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hur kan jag för att komma åt resurser i ett virtuellt nätverk från en funktionsapp?

Du kan komma åt resurser i ett virtuellt nätverk från en funktion som körs med virtual network-integration. Mer information finns i [virtuell nätverksintegrering](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hur kommer jag åt resurser som skyddas av tjänstslutpunkter?

Med virtual network-integration (för närvarande i förhandsversion) kan du komma åt service-slutpunkt-säkrade resurser från en funktion som körs. Mer information finns i [Förhandsgranska virtual network-integration](functions-networking-options.md#preview-version-of-virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hur kan jag för att utlösa en funktion från en resurs i ett virtuellt nätverk?

Du kan tillåta HTTP-utlösare anropas från ett virtuellt nätverk med [tjänstslutpunkter](./functions-networking-options.md#private-site-access). 

Du kan också utlösa en funktion från en resurs i ett virtuellt nätverk genom att distribuera funktionsappen till en App Service Environment. Mer information om hur du använder en App Service Environment finns i [skapa och använda en intern belastningsutjämnare med en App Service Environment](../app-service/environment/create-ilb-ase.md).

På Premium- och App Service plan stöder HTTP-utlösare från ett virtuellt nätverk, men endast en App Service environment stöd för alla andra funktionen utlösartyperna via ett virtuellt nätverk.

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hur kan jag distribuera Mina function-app i ett virtuellt nätverk?

Distribuera till en App Service Environment är det enda sättet att skapa en funktionsapp som är helt i ett virtuellt nätverk. Mer information om hur du använder en intern belastningsutjämnare med en App Service Environment som börjar med artikeln [skapa och använda en intern belastningsutjämnare med en App Service Environment](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Scenarier där du behöver endast enkelriktad åtkomst till resurser för virtuella nätverk eller mindre omfattande nätverksisolering kan se den [fungerar Nätverksöversikt](functions-networking-options.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om nätverk och funktioner: 

* [Kursen om att komma igång med virtual network-integration](./functions-create-vnet.md)
* [Mer information om Nätverksalternativ i Azure Functions](./functions-networking-options.md)
* [Mer information om virtual network-integration med App Service och Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Mer information om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera flera nätverk funktioner och kontroll med App Service-miljöer](../app-service/environment/intro.md)
