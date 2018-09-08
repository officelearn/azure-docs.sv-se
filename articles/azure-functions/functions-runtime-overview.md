---
title: Runtime översikt över Azure Functions | Microsoft Docs
description: Översikt över Azure Functions Runtime Preview
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 4d11af1edc13fa675bef5cf9067dbe95646abff1
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091925"
---
# <a name="azure-functions-runtime-overview-preview"></a>Azure Functions Runtime-översikt (förhandsversion)

Azure Functions-körning (förhandsversion) är en ny metod som du kan dra nytta av enkelheten och flexibilitet i Azure Functions programming modellen lokalt. Bygger på öppen källkod samma rot som Azure Functions och är Azure Functions Runtime distribuerade på plats att tillhandahålla en nästan identiska utvecklingsupplevelse som Molntjänsten.

![Azure Functions Runtime Preview-portalen][1]

Azure Functions-körning är ett sätt att uppleva Azure Functions innan du genomför till molnet. På så sätt kan kan de kod som redan finns skapar du sedan tas med dig till molnet när du migrerar.  Körningen öppnas också nya alternativ för dig, som att använda ledig beräkningskraft på lokala datorer för att köra batchprocesser över natten. Du kan också använda enheter i din organisation att villkorligt skickar data till andra system, både lokalt och i molnet.

Azure Functions Runtime består av två delar:

* Azure Functions Runtime Hanteringsroll
* Azure Functions Runtime Worker-roll

## <a name="azure-functions-management-role"></a>Azure Functions Hanteringsroll

Hanteringsroll för Azure Functions tillhandahåller en värd för hantering av funktioner på plats. Den här rollen utför följande uppgifter:

* Värden för Azure Functions-hanteringsportalen, vilket är samma konto som du ser i den [Azure-portalen](https://portal.azure.com). Portalen ger en konsekvent upplevelse som låter dig utveckla dina funktioner på samma sätt som i Azure-portalen.
* Distribuera functions över flera funktioner arbetare.
* Att tillhandahålla en publiceringsslutpunkten så att du kan publicera dina funktioner direkt från Microsoft Visual Studio genom att hämta och importera publiceringsprofilen.

## <a name="azure-functions-worker-role"></a>Azure Functions-Arbetsroll

Azure Functions Worker-roller har distribuerats i Windows-behållare och är där Funktionskoden körs.  Du kan distribuera flera Worker-roller i hela organisationen och det här alternativet är ett viktiga sätt där kunder kan göra att använda ledig beräkningskraft.  Ett exempel på där det finns ledig beräkning i många organisationer är datorer som är påslagna hela tiden men inte används för stora tidsperioder.

## <a name="minimum-requirements"></a>Minimikrav

För att komma igång med Azure Functions-körning, måste du ha en dator med Windows Server 2016 eller Windows 10 Creators Update med åtkomst till en SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Installera den [Azure Functions Runtime preview](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
