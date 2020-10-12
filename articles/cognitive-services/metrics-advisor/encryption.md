---
title: Kryptering av mått Advisor-tjänsten
titleSuffix: Azure Cognitive Services
description: Metric Advisor-tjänstekryptering av data i vila.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 9d6a2f8a69d41d29e635b03425ed738484d6f408
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90941536"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Tjänsten Metrics Advisor-kryptering av data i vila

Tjänsten Metrics Advisor krypterar automatiskt dina data när de sparas i molnet. Tjänsten Metrics Advisor-kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Kundhanterade nycklar är bara tillgängliga på pris nivån E0. För att begära möjlighet att använda Kundhanterade nycklar, fyller du i och skickar [tjänsten Metrics Advisor Service Customer-Managed Key Request](https://aka.ms/cogsvc-cmk). Det tar cirka 3-5 arbets dagar att höra om status för din begäran. Beroende på efter frågan kan du placera i en kö och godkännas som utrymme blir tillgängligt. När du har godkänt för att använda CMK med Metrics Advisor-tjänsten måste du skapa en ny Metrics Advisor-resurs och välja E0 som pris nivå. När din Metric Advisor-resurs med E0-pris nivån har skapats kan du använda Azure Key Vault för att konfigurera din hanterade identitet.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Nästa steg

* [Formulär för tjänsten Metrics Advisor Customer-Managed nyckel](https://aka.ms/cogsvc-cmk)
* [Läs mer om Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
