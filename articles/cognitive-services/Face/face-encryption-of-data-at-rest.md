---
title: Ansiktstjänstkryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Ansiktstjänstkryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 793c21bb2341033aa51ff8c639846e57ada4bae3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372220"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Ansiktstjänstkryptering av data i vila

Face-tjänsten krypterar automatiskt dina data när de sparas till molnet. Face-tjänstens kryptering skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är endast tillgängliga på E0-prisnivån. Om du vill begära möjligheten att använda kundhanterade nycklar fyller du i och skickar [formuläret för kundhanterad nyckelbegäran för face service.](https://aka.ms/cogsvc-cmk) Det tar cirka 3-5 arbetsdagar att höra om statusen för din begäran. Beroende på efterfrågan kan du placeras i en kö och godkännas när utrymme blir tillgängligt. När du har godkänts för att använda CMK med Face-tjänsten måste du skapa en ny Face-resurs och välja E0 som prisnivå. När din Face-resurs med E0-prisnivån har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Nästa steg

* [Formulär för kundhanterad nyckelbegäran för face service](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


