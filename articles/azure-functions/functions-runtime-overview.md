---
title: Översikt över Körmiljö för Azure Functions
description: Översikt över Körmiljö för Azure Functions för hands version
author: apwestgarth
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: ab04aa4ca7f54e8de120d078a313c3096a350aa5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74226630"
---
# <a name="azure-functions-runtime-overview-preview"></a>Översikt över Körmiljö för Azure Functions (för hands version)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Körmiljö för Azure Functions (för hands version) är ett nytt sätt att dra nytta av den enklare och flexibiliteten i den Azure Functions Programmerings modellen lokalt. Körmiljö för Azure Functions har byggts med samma rötter för öppen källkod som Azure Functions, och har distribuerats lokalt för att ge en nästan identisk utvecklings upplevelse som moln tjänst.

![Körmiljö för Azure Functions för hands versions portalen][1]

Körmiljö för Azure Functions är ett sätt för dig att uppleva Azure Functions innan du genomför det i molnet. På så sätt kan de kod till gångar som du skapar sedan tas med dig till molnet när du migrerar.  Körningen öppnar också nya alternativ åt dig, till exempel att använda den extra beräknings kraften hos dina lokala datorer för att köra batch-processer över natten. Du kan också använda enheter i din organisation för att villkorligt skicka data till andra system, både lokalt och i molnet.

Körmiljö för Azure Functions består av två delar:

* Körmiljö för Azure Functions hanterings roll
* Körmiljö för Azure Functions arbets roll

## <a name="azure-functions-management-role"></a>Azure Functions hanterings roll

Rollen Azure Functions hantering tillhandahåller en värd för hantering av dina funktioner lokalt. Den här rollen utför följande uppgifter:

* Värd för Azure Functions Hanteringsportal, som är samma som visas i [Azure Portal](https://portal.azure.com). Portalen ger en enhetlig upplevelse som gör att du kan utveckla dina funktioner på samma sätt som du skulle göra i Azure Portal.
* Distribuera funktioner över flera funktions arbetare.
* Tillhandahålla en publicerings slut punkt så att du kan publicera dina funktioner direkt från Microsoft Visual Studio genom att hämta och importera publicerings profilen.

## <a name="azure-functions-worker-role"></a>Azure Functions arbets roll

Azure Functions Worker-roller distribueras i Windows-behållare och är där funktions koden körs.  Du kan distribuera flera arbets roller i organisationen och det här alternativet är ett viktigt sätt för kunderna att använda reserv beräknings kraft.  Ett exempel på var reserv beräkning finns i många organisationer är datorer som drivs ständigt men inte används under stora tids perioder.

## <a name="minimum-requirements"></a>Minimi krav

För att komma igång med Körmiljö för Azure Functions måste du ha en dator med Windows Server 2016 eller Windows 10 Creators Update med åtkomst till en SQL Server-instans.

## <a name="next-steps"></a>Efterföljande moment

Installera [körmiljö för Azure Functions för hands version](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
