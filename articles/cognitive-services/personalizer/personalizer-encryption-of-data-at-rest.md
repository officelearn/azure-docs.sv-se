---
title: Tjänst kryptering av data i vila
titleSuffix: Azure Cognitive Services
description: Tjänst kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 63526454bb366b214c27bddce24ed9ebc09556b3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80071093"
---
# <a name="personalizer-service-encryption-of-data-at-rest"></a>Tjänst kryptering av data i vila

Tjänsten personanpassa krypterar automatiskt dina data när de sparas i molnet. Dekrypteringen för depersonanpassar-tjänsten skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga på pris nivån E0. Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar det [anpassade formuläret för Kundhanterad nyckel för Customer Service](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med tjänsten personanpassa måste du skapa en ny personanpassa resurs och välja E0 som pris nivå. När din personanpassa resurs med E0-pris nivån har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Nästa steg

* [Customer Service – kundhanterad nyckel förfrågnings formulär](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
