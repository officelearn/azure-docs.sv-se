---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 07/11/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 099bca7483100da1a4ee2f8f10057c416ad145b0
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841424"
---
Du lägga till taggar till Azure-resurser så att metadata för att organisera dem logiskt i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

När du har lagt till taggar kan du hämta alla resurserna i din prenumeration med det taggnamnet och taggvärdet. Taggarna gör att du kan hämta relaterade resurser från olika resursgrupper. Den här metoden är användbar när du behöver organisera resurser för fakturering eller hantering.

Din taxonomi bör en självbetjäning metadata taggning strategi förutom en automatisk märkning strategi för att minska belastningen på användare och öka tillförlitligheten.

Följande begränsningar gäller för taggar:

* Inte alla resurstyper stöder taggar. För att avgöra om du kan använda en tagg för en resurstyp, se [tagga stöd för Azure-resurser](../articles/azure-resource-manager/tag-support.md).
* Varje resurs eller resursgrupp kan ha högst 50 taggen namn/värde-par. För närvarande lagringskonton endast stöder 15 taggar, men denna gräns kommer att ökas till 50 i en framtida version. Om du vill lägga till fler taggar än det högsta tillåtna antalet kan du använda en JSON-sträng för Taggvärdet. JSON-strängen kan innehålla många värden som tillämpas på ett enda taggnamn. En resursgrupp kan innehålla många resurser som har 50 taggen namn/värde-par.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Generaliserad virtuella datorer stöder inte taggar.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.
* Taggar kan inte tillämpas på klassiska resurser, till exempel molntjänster.
* Taggen får inte innehålla följande tecken: `<`, `>`, `%`, `&`, `\`, `?`, `/`
