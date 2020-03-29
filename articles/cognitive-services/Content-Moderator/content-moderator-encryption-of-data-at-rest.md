---
title: Kryptering av data i vila för innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Innehåll Moderator kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372165"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Kryptering av data i vila för innehållsmoderator

Innehållsmoderator krypterar automatiskt dina data när de sparas i molnet, vilket hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsmål.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är endast tillgängliga på E0-prisnivån. Om du vill begära möjligheten att använda kundhanterade nycklar fyller du i och skickar [formuläret för kundhanterad nyckelbegäran för innehållsmoderator](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbetsdagar att höra om statusen för din begäran. Beroende på efterfrågan kan du placeras i en kö och godkännas när utrymme blir tillgängligt. När du har godkänts för att använda CMK med tjänsten Content Moderator måste du skapa en ny Content Moderator-resurs och välja E0 som prisnivå. När din Content Moderator-resurs med E0-prisnivån har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Aktivera datakryptering för content moderatorteamet

Information om hur du aktiverar datakryptering för granskningsteamet för innehållsmoderatorer finns i [Snabbstart: Prova Innehållsmoderator på webben](quick-start.md#create-a-review-team).  

> [!NOTE]
> Du måste ange ett _resurs-ID_ med prisnivån Innehållsmoderator E0.


## <a name="next-steps"></a>Nästa steg

* [Formulär för kundhanterad nyckelbegäran för innehållsmoderator](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
