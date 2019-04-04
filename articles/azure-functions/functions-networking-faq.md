---
title: Vanliga frågor och svar om nätverk i Azure Functions
description: Svar på några av de vanligaste frågorna och scenarier för nätverk med Azure Functions.
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 2/26/2019
ms.author: alkarche
ms.openlocfilehash: 7946b7f45ff3df9225a27b70ccfbdb895bfd03c4
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896490"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Vanliga frågor och svar om nätverk i Azure Functions

Nedan visas en lista med vanliga frågor för nätverk. En mer omfattande översikt finns på [fungerar nätverk alternativ dokumentet](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Hur ställer jag en statisk IP-adress i Functions?

Distribuera en funktion i en App Service Environment (ASE) är för närvarande det enda sättet att ha en statisk inkommande och utgående IP-adress för din funktion. Mer information om hur du använder en ASE börjar du med den här artikeln: [Skapa och använda en ILB ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Hur begränsar Internet-åtkomst till min funktion?

Du kan begränsa Internetåtkomst på flera olika sätt som anges nedan.

* [IP-restriktioner](../app-service/app-service-ip-restrictions.md): begränsa inkommande trafik till din funktionsapp med IP-intervall.
* Ta bort alla HTTP-utlösare. För vissa program räcker det att bara undvika HTTP-utlösare och använda andra händelsekällan för att utlösa din funktion.

Viktiga överväganden när du gör är att ha i åtanke som redigeraren för Azure-portalen kräver direkt åtkomst till din funktion som körs ska användas. Kodändringar via Azure-portalen kräver den enhet som du använder för att bläddra på portalen om du vill att dess IP-godkänd. Du kan fortfarande, men använda något under fliken plattform funktioner med nätverksbegränsningar på plats.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>Hur begränsar jag mina function-app till ett virtuellt nätverk?

Det enda sättet att helt begränsa en funktion så att all trafik som flödar genom ett virtuellt nätverk är att använda ett internt belastningsutjämnade (ILB) App Service Environment (ASE). Det här alternativet distribuerar din webbplats på dedikerad infrastruktur i ett virtuellt nätverk och skickar alla utlösare och trafik via det virtuella nätverket. Mer information om hur du använder en ASE börjar du med den här artikeln: [Skapa och använda en ILB ASE](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>Hur kan jag för att komma åt resurser i ett virtuellt nätverk från en funktionsapp?

Du kan komma åt resurser i ett virtuellt nätverk från en funktion som körs med hjälp av VNET-integrering. Mer information finns i [VNET-integrering](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Hur kommer jag åt resurser som skyddas av tjänstslutpunkter?

Med den nya VNET-integrationen (för närvarande i förhandsversion) kan du komma åt tjänstslutpunkt skyddade resurser från en funktion som körs. Mer information finns i [Förhandsgranska VNET-integrering](functions-networking-options.md#preview-vnet-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>Hur kan jag för att utlösa en funktion från en resurs i ett virtuellt nätverk?

Du kan endast utlösa en funktion från en resurs i ett virtuellt nätverk genom att distribuera funktionsappen till en App Service Environment. Mer information om hur du använder en ASE finns i [skapa och använda en ILB ASE](../app-service/environment/create-ilb-ase.md).


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>Hur kan jag distribuera Mina function-app i ett virtuellt nätverk?

Distribuera till en App Service Environment är det enda sättet att skapa en funktionsapp som är helt inne i ett virtuellt nätverk för information om hur du använder en ILB ASE, börja med den här artikeln: [Skapa och använda en ILB ASE](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Scenarier där du behöver bara enkelriktad åtkomst till VNET-resurser eller mindre omfattande nätverksisolering kan se den [fungerar Nätverksöversikt](functions-networking-options.md).
