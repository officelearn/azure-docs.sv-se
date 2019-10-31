---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 10/30/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c141e67157f3ec17d475062ec76406ec765c4f50
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199172"
---
Du kan använda taggar till dina Azure-resurser för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

När du har lagt till taggar kan du hämta alla resurserna i din prenumeration med det taggnamnet och taggvärdet. Med taggar kan du hämta relaterade resurser från olika resurs grupper. Den här metoden är användbar när du behöver organisera resurser för fakturering eller hantering.

Din taxonomi bör överväga en strategi för att tagga en egen metadata utöver en strategi för automatisk taggning för att minska belastningen på användare och öka noggrannheten.

Följande begränsningar gäller för taggar:

* Inte alla resurs typer stöder taggar. Information om hur du kan använda en tagg för en resurs typ finns i [tagga stöd för Azure-resurser](../articles/azure-resource-manager/tag-support.md).
* Varje resurs eller resurs grupp får innehålla högst 50 taggnamn/värdepar. Om du behöver använda fler taggar än det högsta tillåtna antalet använder du en JSON-sträng för värdet. JSON-strängen kan innehålla många värden som tillämpas på ett enda taggnamn. En resurs grupp kan innehålla många resurser som var och en har 50 tagg namn/värde-par.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Generaliserade virtuella datorer stöder inte taggar.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.
* Det går inte att använda taggar för klassiska resurser som Cloud Services.
* Taggnamn får inte innehålla följande tecken: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > För närvarande Azure DNS zoner och Traffic Manager-tjänster inte heller att använda blank steg i taggen. 
