---
title: Så här genererar och överför du HSM-skyddade nycklar för Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Använd den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller ta med din egen nyckel.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: d7f9527aa5aa3353dc9087f4bcc5f3a5fb241637
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184561"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importera HSM-skyddade nycklar till Key Vault

När du använder Azure Key Vault kan du med extra garantier importera eller generera nycklar i HSM: er (Hardware Security modules) som aldrig lämnar HSM-gränser. Det här scenariot kallas ofta för att *ta med din egen nyckel*eller BYOK. Azure Key Vault använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er (FIPS 140-2 nivå 2 verifieras) för att skydda dina nycklar.

Den här funktionen är inte tillgänglig för Azure Kina 21Vianet.

> [!NOTE]
> Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-overview.md)  
> En vägledning för att komma igång, vilket innefattar att skapa ett nyckel valv för HSM-skyddade nycklar, finns i [Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>HSM: er som stöds

Överföring av HSM-skyddade nycklar till Key Vault stöds via två olika metoder beroende på vilken HSM: er du använder. Använd tabellen nedan för att avgöra vilken metod som ska användas för din HSM: er för att generera och sedan överföra egna HSM-skyddade nycklar som ska användas med Azure Key Vault. 

|HSM-leverantörens namn|HSM-modeller som stöds|Överförings metod som stöds av HSM-nyckel|
|---|---|---|
|Thales|<ul><li>SafeNet Luna HSM 7-serien med inbyggd program vara version 7,3 eller senare</li></ul>| [Använd ny BYOK-metod (förhands granskning)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Hjälp programmet nCipher|<ul><li>nshield maskinvarusäkerhetsmodul-serien HSM: er</li></ul>|[Använd äldre BYOK-metod](hsm-protected-keys-legacy.md)|


## <a name="next-steps"></a>Nästa steg

Följ [Key Vault bästa praxis](key-vault-best-practices.md) för att säkerställa säkerhet, hållbarhet och övervakning för dina nycklar.
