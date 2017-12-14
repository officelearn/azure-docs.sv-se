---
title: "Runtime översikt över Azure Functions | Microsoft Docs"
description: "Översikt över förhandsversionen av Azure Functions-Runtime"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 557f071e2cd8d4f639c881274e6e74a8fb745859
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="azure-functions-runtime-overview"></a>Översikt över Azure Functions-Runtime

Azure Functions-Runtime är en ny metod att dra nytta av enkelheten och flexibilitet i Azure Functions programming modellen på lokalt. Azure Functions-Runtime är bygger på öppen källkod samma rot som Azure Functions distribuerade lokalt att ge en nästan identisk utvecklingsarbetet som Molntjänsten.

![Azure Functions-Runtime Preview-portalen][1]

Azure Functions-Runtime kan du uppleva Azure Functions innan du genomför till molnet. På så sätt kan kan kod tillgångarna som du skapar sedan tas med dig till molnet när du migrerar.  Körningen öppnas också nya alternativ för dig, som att använda ledig datorkraft lokala datorer för att köra batchprocesser över natten. Du kan också använda enheter inom organisationen villkorligt skicka data till andra system, både lokalt och i molnet.

Azure Functions-Runtime består av två delar:

* Azure Functions-Runtime Ledningsroll
* Azure Functions-Runtime Worker-rollen

## <a name="azure-functions-management-role"></a>Azure Functions roll

Azure Functions Management rollen ger en värd för hanteringen av dina funktioner lokala. Den här rollen utför följande uppgifter:

* Att vara värd för Azure Functions hanteringsportalen, vilket är samma som visas i den [Azure-portalen](https://portal.azure.com). Den ger en konsekvent användarupplevelse som låter dig utveckla dina funktioner på samma sätt som i Azure-portalen.
* Distribuera funktioner på flera funktioner arbetare.
* Tillhandahåller en publishing slutpunkt så att du kan publicera dina funktioner direkt från Microsoft Visual Studio genom att hämta och importera publiceringsprofilen.

## <a name="azure-functions-worker-role"></a>Azure Functions Worker-rollen

Azure Functions-arbetsroller distribueras i Windows-behållare och är där Funktionskoden körs.  Du kan distribuera flera arbetsroller i organisationen och det här alternativet är viktiga sätt där kunder kan göra använda datorkraft som ledig.  Ett exempel på där ledig beräkning finns i många organisationer är påslagen ständigt datorer men inte används för stora tidsperioder.

## <a name="minimum-requirements"></a>Minimikrav

Om du vill komma igång med Azure Functions-Runtime, måste du ha en dator med Windows Server 2016 eller Windows 10 skapare Update med åtkomst till en SQL Server-instans.

## <a name="next-steps"></a>Nästa steg

Installera den [Azure Functions-Runtime preview](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
