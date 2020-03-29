---
title: ta med fil
description: ta med fil
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372176"
---
## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services-kryptering

Data krypteras och dekrypteras med [FIPS 140-2-kompatibel](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bitars AES-kryptering.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Kryptering och dekryptering är transparenta, vilket innebär att kryptering och åtkomst hanteras för dig. Dina data är säkra som standard och du behöver inte ändra din kod eller dina program för att dra nytta av kryptering.

## <a name="about-encryption-key-management"></a>Om hantering av krypteringsnyckel

Som standard använder din prenumeration Microsoft-hanterade krypteringsnycklar. Om du använder en prisnivå som stöder kundhanterade nycklar kan du se krypteringsinställningarna för din resurs i avsnittet **Kryptering** i [Azure-portalen](https://portal.azure.com), som visas i följande avbildning.

![Visa krypteringsinställningar](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

För prenumerationer som bara stöder Microsoft-hanterade krypteringsnycklar har du inget **krypteringsavsnitt.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Det finns också en möjlighet att hantera din prenumeration med dina egna nycklar. Customer-managed keys (CMK), även känd som Bring your own key (BYOK), erbjuder större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.
