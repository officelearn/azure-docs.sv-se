---
title: Ansikts tjänst kryptering av vilande data
titleSuffix: Azure Cognitive Services
description: Ansikts tjänst kryptering av data i vila.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201933"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Ansikts tjänst kryptering av vilande data

Ansikts tjänsten krypterar automatiskt dina data när de sparas i molnet. Kryptering av ansikts tjänst skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga på pris nivån E0. Om du vill begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar in [fråge formuläret för Kundhanterad nyckel för kund tjänst](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med ansikts tjänsten måste du skapa en ny ansikts resurs och välja E0 som pris nivå. När din ansikte-resurs med E0-pris nivån har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

### <a name="regional-availability"></a>Regional tillgänglighet

Kundhanterade nycklar är för närvarande tillgängliga i följande regioner:

* USA, södra centrala
* USA, västra 2
* USA, östra
* US Gov, Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Nästa steg

* [Formulär för kund-hanterad nyckel för ansikts tjänst](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


