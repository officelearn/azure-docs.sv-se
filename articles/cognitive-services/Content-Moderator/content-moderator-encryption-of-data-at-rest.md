---
title: Content Moderator kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Content Moderator kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: b41d822791f61fce274f628b87c478c3a986f4c3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372165"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator kryptering av data i vila

Content Moderator krypterar dina data automatiskt när de sparas i molnet, vilket hjälper till att uppfylla organisationens säkerhets-och efterlevnads mål.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga på pris nivån E0. Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar det [Content moderator kundens hanterade nyckel fråge formulär](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med Content Moderator tjänsten måste du skapa en ny Content Moderator resurs och välja E0 som pris nivå. När din Content Moderator-resurs med pris nivån E0 har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Aktivera data kryptering för Content Moderator-teamet

Om du vill aktivera data kryptering för Content Moderator granska teamet går du till [snabb start: prova Content moderator på webben](quick-start.md#create-a-review-team).  

> [!NOTE]
> Du måste ange ett _resurs-ID_ med Content moderator pris nivå för E0.


## <a name="next-steps"></a>Nästa steg

* [Formulär för Content Moderator kundhanterad nyckel](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
