---
title: Vanliga frågor och svar om nätverk i Azure Functions
description: Svar på några av de vanligaste frågorna och scenarierna för nätverk med Azure Functions.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409536"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Vanliga frågor och svar om nätverk i Azure Functions

I den här artikeln visas vanliga frågor om nätverk i Azure Functions. En mer omfattande översikt finns i [Alternativ för funktioner i nätverk](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hur ställer jag in en statisk IP i Functions?

Distribuera en funktion i en App Service-miljö är för närvarande det enda sättet att ha en statisk inkommande och utgående IP för din funktion. Om du vill ha information om hur du använder en App Service Environment börjar du med artikeln [Skapa och använd en intern belastningsutjämnare med en App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hur begränsar jag internetåtkomsten till min funktion?

Du kan begränsa internetuppkopplingen på ett par olika sätt:

* [IP-begränsningar](../app-service/app-service-ip-restrictions.md): Begränsa inkommande trafik till din funktionsapp per IP-intervall.
    * Under IP-begränsningar kan du också konfigurera [tjänstslutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md), vilket begränsar din funktion till att endast acceptera inkommande trafik från ett visst virtuellt nätverk.
* Borttagning av alla HTTP-utlösare. För vissa program räcker det att helt enkelt undvika HTTP-utlösare och använda någon annan händelsekälla för att utlösa din funktion.

Tänk på att Azure Portal Editor kräver direkt åtkomst till din körfunktion. Alla kodändringar via Azure-portalen kräver att enheten du använder för att bläddra i portalen för att få sin IP vitlistad. Men du kan fortfarande använda vad som helst under fliken plattformsfunktioner med nätverksbegränsningar på plats.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Hur begränsar jag min funktionsapp till ett virtuellt nätverk?

Du kan begränsa **inkommande** trafik för en funktionsapp till ett virtuellt nätverk med hjälp av [Tjänstslutpunkter](./functions-networking-options.md#private-site-access). Den här konfigurationen gör det fortfarande möjligt för funktionsappen att ringa utgående samtal till internet.

Det enda sättet att helt begränsa en funktion så att all trafik flödar genom ett virtuellt nätverk är att använda en internt belastningsbalanserad App Service Environment. Det här alternativet distribuerar din webbplats på en dedikerad infrastruktur i ett virtuellt nätverk och skickar alla utlösare och trafik via det virtuella nätverket. 

Om du vill ha information om hur du använder en App Service Environment börjar du med artikeln [Skapa och använd en intern belastningsutjämnare med en App Service Environment](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Hur kommer jag åt resurser i ett virtuellt nätverk från en funktionsapp?

Du kan komma åt resurser i ett virtuellt nätverk från en funktion som körs med hjälp av integrering av virtuella nätverk. Mer information finns i [Integrering av virtuella nätverk](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hur kommer jag åt resurser som skyddas av tjänstslutpunkter?

Med hjälp av integrering av virtuella nätverk kan du komma åt tjänst-endpoint-säkrade resurser från en funktion som körs. Mer information finns i [integrering av virtuella nätverk](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Hur kan jag utlösa en funktion från en resurs i ett virtuellt nätverk?

Du kan tillåta att HTTP-utlösare anropas från ett virtuellt nätverk med hjälp av [Tjänstslutpunkter](./functions-networking-options.md#private-site-access). 

Du kan också utlösa en funktion från alla andra resurser i ett virtuellt nätverk genom att distribuera din funktionsapp till en Premium-plan, App Service-plan eller App Service Environment. Mer information finns i utlösare av [virtuella HTTP-nätverk](./functions-networking-options.md#virtual-network-triggers-non-http)

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Hur kan jag distribuera min funktionsapp i ett virtuellt nätverk?

Distribuera till en App Service Environment är det enda sättet att skapa en funktion app som är helt i ett virtuellt nätverk. Om du vill ha information om hur du använder en intern belastningsutjämnare med en App Service-miljö börjar du med artikeln [Skapa och använd en intern belastningsutjämnare med en App Service Environment](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Scenarier där du bara behöver enkel åtkomst till virtuella nätverksresurser eller mindre omfattande nätverksisolering finns i [översikten Funktioner i nätverket](functions-networking-options.md).

## <a name="next-steps"></a>Nästa steg

Så här lär du dig mer om nätverk och funktioner: 

* [Följ självstudien om hur du kommer igång med integrering av virtuella nätverk](./functions-create-vnet.md)
* [Läs mer om nätverksalternativen i Azure Functions](./functions-networking-options.md)
* [Läs mer om integrering av virtuella nätverk med App Service och -funktioner](../app-service/web-sites-integrate-with-vnet.md)
* [Läs mer om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera fler nätverksfunktioner och kontroll med App Service-miljöer](../app-service/environment/intro.md)
