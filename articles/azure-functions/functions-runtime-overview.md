---
title: Översikt över Azure Functions Runtime
description: Översikt över förhandsversionen av Azure Functions Runtime
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74226630"
---
# <a name="azure-functions-runtime-overview-preview"></a>Översikt över Azure Functions Runtime (förhandsversion)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure Functions Runtime (förhandsversion) är ett nytt sätt för dig att dra nytta av enkelheten och flexibiliteten i Azure Functions programmeringsmodell lokalt. Azure Functions Runtime bygger på samma rötter med öppen källkod som Azure Functions Runtime lokalt för att ge en nästan identisk utvecklingsupplevelse som molntjänsten.

![Förhandsportal för Azure-funktioner runtime][1]

Azure Functions Runtime är ett sätt för dig att uppleva Azure-funktioner innan du bestämmer dig för molnet. På så sätt kan de kodtillgångar du skapar sedan tas med dig till molnet när du migrerar.  Körningen öppnar också nya alternativ för dig, till exempel att använda den extra beräkningskraften för dina lokala datorer för att köra batchprocesser över natten. Du kan också använda enheter inom organisationen för att villkorligt skicka data till andra system, både lokalt och i molnet.

Azure Functions Runtime består av två delar:

* Azure-funktionshanteringsroll för körning
* Azure-funktioner körtid arbetarroll

## <a name="azure-functions-management-role"></a>Hanteringsroll för Azure Functions

Azure Functions Management-rollen är en värd för hanteringen av dina funktioner lokalt. Den här rollen utför följande uppgifter:

* Värd för Azure Functions Management Portal, som är samma som du ser i [Azure-portalen](https://portal.azure.com). Portalen ger en konsekvent upplevelse som gör att du kan utveckla dina funktioner på samma sätt som i Azure-portalen.
* Distribuera funktioner över flera Functions-arbetare.
* Tillhandahålla en publiceringsslutpunkt så att du kan publicera dina funktioner direkt från Microsoft Visual Studio genom att hämta och importera publiceringsprofilen.

## <a name="azure-functions-worker-role"></a>Arbetarrollen Azure-funktioner

Azure Functions Worker Roles distribueras i Windows Containers och är där din funktionskod körs.  Du kan distribuera flera arbetarroller i hela organisationen och det här alternativet är ett viktigt sätt på vilket kunder kan använda sig av extra beräkningskraft.  Ett exempel på var ledig beräkning finns i många organisationer är maskiner som drivs på hela tiden men inte används under stora tidsperioder.

## <a name="minimum-requirements"></a>Minimikrav

För att komma igång med Azure Functions Runtime måste du ha en dator med Windows Server 2016 eller Windows 10 Creators Update med åtkomst till en SQL Server-instans.

## <a name="next-steps"></a>Efterföljande moment

Installera [förhandsversionen](https://aka.ms/azafrdoc) av Azure Functions Runtime

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
