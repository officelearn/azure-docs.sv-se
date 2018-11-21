---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f411504b0f4b7872e92a64c57fecbde863f532c6
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52272360"
---
Du lägga till taggar till Azure-resurser så att metadata för att organisera dem logiskt i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

När du har lagt till taggar kan du hämta alla resurserna i din prenumeration med det taggnamnet och taggvärdet. Taggarna gör att du kan hämta relaterade resurser från olika resursgrupper. Den här metoden är användbar när du behöver organisera resurser för fakturering eller hantering.

Din taxonomi bör en självbetjäning metadata taggning strategi förutom en automatisk märkning strategi för att minska belastningen på användare och öka tillförlitligheten.

Följande begränsningar gäller för taggar:

* Inte alla resurstyper stöder taggar. För att avgöra om du kan använda en tagg för en resurstyp, se [tagga stöd för Azure-resurser](../articles/azure-resource-manager/tag-support.md).
* Varje resurs eller resursgrupp kan innehålla upp till 15 taggnamn-/taggvärdepar. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggnamn-/taggvärdepar. Om du har fler än 15 värden som du vill koppla till en resurs använder du en JSON-sträng för taggvärdet. JSON-strängen kan innehålla många värden som tillämpas på ett enda taggnamn. Den här artikeln innehåller ett exempel som illustrerar hur du tilldelar en JSON-sträng till taggen.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Virtuella datorer är begränsad till högst 2048 tecken för alla taggnamn och värden.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.
* Taggar kan inte tillämpas på klassiska resurser, till exempel molntjänster.
* Taggen får inte innehålla följande tecken: `<`, `>`, `%`, `&`, `\`, `?`, `/`
