---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 0b03957178af0578d2c6cd91d7377c93f413cec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84629488"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Om ditt data lager har kon figurer ATS på något av följande sätt måste du konfigurera en lokal [integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) för att ansluta till data lagret:

- Data lagret finns i ett lokalt nätverk, i ett virtuellt Azure-nätverk eller i ett Amazon-virtuellt privat moln.
- Data lagret är en hanterad moln data tjänst där åtkomsten är begränsad till IP-adresser som vit listas i brand Väggs reglerna.
