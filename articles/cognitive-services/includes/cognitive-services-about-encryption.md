---
title: inkludera fil
description: inkludera fil
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89069932"
---
## <a name="about-cognitive-services-encryption"></a>Om Cognitive Services kryptering

Data krypteras och dekrypteras med hjälp av [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-kompatibla 256-bitars AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) kryptering. Kryptering och dekryptering är transparent, vilket innebär att kryptering och åtkomst hanteras åt dig. Dina data skyddas som standard och du behöver inte ändra din kod eller dina program för att utnyttja krypteringen.

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Som standard använder din prenumeration krypteringsnycklar som hanteras av Microsoft. Det finns också möjlighet att hantera din prenumeration med dina egna nycklar som kallas Kundhanterade nycklar (CMK). CMK erbjuder större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data. Om CMK har kon figurer ATS för din prenumeration tillhandahålls dubbla kryptering, vilket ger ett sekundärt skydds lager, samtidigt som du kan kontrol lera krypterings nyckeln via din Azure Key Vault.