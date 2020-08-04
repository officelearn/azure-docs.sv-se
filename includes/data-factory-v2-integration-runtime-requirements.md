---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: 6c348b3dd0005eeab154aa2d74abc617cbd1d0cb
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529404"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Om ditt data lager finns i ett lokalt nätverk, ett virtuellt Azure-nätverk eller ett Amazon-virtuellt privat moln, måste du konfigurera en lokal [integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) för att ansluta till den.

Om ditt data lager är en hanterad moln data tjänst kan du använda Azure integration Runtime. Om åtkomsten är begränsad till IP-adresser som är vit listas i brand Väggs reglerna kan du välja att lägga till [Azure integration runtime IP-adresser](../articles/data-factory/azure-integration-runtime-ip-addresses.md) i listan över tillåtna. 

Mer information om de mekanismer och alternativ för nätverks säkerhet som stöds av Data Factory finns i [data åtkomst strategier](../articles/data-factory/data-access-strategies.md).
