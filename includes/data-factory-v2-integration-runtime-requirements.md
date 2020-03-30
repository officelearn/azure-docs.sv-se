---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68966363"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Om ditt datalager är konfigurerat på något av följande sätt måste du konfigurera en [självvärderad integrationskörning för](../articles/data-factory/create-self-hosted-integration-runtime.md) att kunna ansluta till det här datalagret:

- Datalagret finns i ett lokalt nätverk, i Azure Virtual Network eller i Amazon Virtual Private Cloud.
- Datalagret är en hanterad molndatatjänst där åtkomsten är begränsad till IP-adresser som vitlistats i brandväggsreglerna.
