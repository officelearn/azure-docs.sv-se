---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2e90d218aa6dc90746ba0e928fb3393f0bdb5e5a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966363"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Om ditt data lager har kon figurer ATS på något av följande sätt måste du konfigurera en [egen värd integration runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) för att kunna ansluta till det här data lagret:

- Data lagret finns i ett lokalt nätverk, inom Azure Virtual Network eller i ett Amazon-virtuellt privat moln.
- Data lagret är en hanterad moln data tjänst där åtkomsten är begränsad till IP-vit listas i brand Väggs reglerna.
