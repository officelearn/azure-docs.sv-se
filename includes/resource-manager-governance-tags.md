---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/13/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 69c67f437d2f0b7bd6c1f5311eb5ba1d962d889a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38738887"
---
Du kan ordna Azure-resurser i kategorier genom att lägga till taggar. Varje tagg består av ett namn och ett värde. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

När du har lagt till taggar kan du hämta alla resurserna i din prenumeration med det taggnamnet och taggvärdet. Taggarna gör att du kan hämta relaterade resurser från olika resursgrupper. Den här metoden är användbar när du behöver organisera resurser för fakturering eller hantering.

Följande begränsningar gäller för taggar:

* Varje resurs eller resursgrupp kan innehålla upp till 15 taggnamn-/taggvärdepar. Den här begränsningen gäller endast för taggar som tillämpas direkt på resursgruppen eller resursen. En resursgrupp kan innehålla många resurser som var och en har 15 taggnamn-/taggvärdepar. Om du har fler än 15 värden som du vill koppla till en resurs använder du en JSON-sträng för taggvärdet. JSON-strängen kan innehålla många värden som tillämpas på ett enda taggnamn. Den här artikeln innehåller ett exempel som illustrerar hur du tilldelar en JSON-sträng till taggen.
* Taggnamnet är begränsat till 512 tecken och taggvärdet är begränsat till 256 tecken. För lagringskonton är taggnamnet begränsat till 128 tecken och taggvärdet till 256 tecken.
* Taggar som lagts till för en resursgrupp ärvs inte av resurserna i den resursgruppen.
* Taggar kan inte tillämpas på klassiska resurser, till exempel molntjänster.
* Taggen får inte innehålla följande tecken: `<`, `>`, `%`, `&`, `\`, `?`, `/`
