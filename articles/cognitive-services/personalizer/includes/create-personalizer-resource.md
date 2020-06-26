---
title: inkludera fil
description: inkludera fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: b1013b261e9449aef45f629c729579f4c87c1f6b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378565"
---
## <a name="create-a-personalizer-azure-resource"></a>Skapa en personanpassa Azure-resurs

Skapa en resurs för Personanpassare med hjälp av [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) eller [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) på den lokala datorn. 

När du har skaffat en nyckel från din resurs skapar du två [miljö variabel](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY`för resurs nyckeln.
* `PERSONALIZER_RESOURCE_ENDPOINT`för resurs slut punkten.

I Azure Portal är både nyckel-och slut punkts värden tillgängliga från **snabb starts** sidan.
