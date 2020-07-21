---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 994b8285999bfa52e2aea9a57ad832aefddcfb76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544886"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Om ditt data lager finns i ett lokalt nätverk, ett virtuellt Azure-nätverk eller ett Amazon-virtuellt privat moln, måste du konfigurera en lokal [integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) för att ansluta till den.

Om ditt data lager är en hanterad moln data tjänst, där åtkomsten är begränsad till IP-adresser som är vit listas i brand Väggs reglerna, kan du använda Azure integration Runtime och lägga till [dess IP-adresser](../articles/data-factory/azure-integration-runtime-ip-addresses.md) i listan över tillåtna. 

Se [data åtkomst strategier](../articles/data-factory/data-access-strategies.md) för information om de nätverks säkerhetsmekanismer som stöds av Data Factory för att få åtkomst till data lager i allmänhet.
