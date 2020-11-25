---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023215"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Om ditt data lager finns i ett lokalt nätverk, ett virtuellt Azure-nätverk eller ett Amazon-virtuellt privat moln, måste du konfigurera en lokal [integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) för att ansluta till den.

Alternativt kan du använda Azure integration Runtime om ditt data lager är en hanterad moln data tjänst. Om åtkomsten är begränsad till IP-adresser som har godkänts i brand Väggs reglerna kan du lägga till [Azure integration runtime IP-adresser](../articles/data-factory/azure-integration-runtime-ip-addresses.md) i listan över tillåtna. 

Mer information om de mekanismer och alternativ för nätverks säkerhet som stöds av Data Factory finns i [data åtkomst strategier](../articles/data-factory/data-access-strategies.md).
