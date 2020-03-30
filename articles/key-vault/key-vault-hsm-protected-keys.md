---
title: Så här genererar och överför HSM-skyddade nycklar för Azure Key Vault - Azure Key Vault | Microsoft-dokument
description: Använd den här artikeln för att planera för, generera och sedan överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Även känd som BYOK eller ta med din egen nyckel.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082905"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importera HSM-skyddade nycklar till Key Vault

När du använder Azure Key Vault för extra säkerhet kan du importera eller generera nycklar i maskinvarusäkerhetsmoduler (HSM) som aldrig lämnar HSM-gränsen. Det här scenariot kallas ofta *BYOK* (Bring Your Own Key). Azure Key Vault använder nCipher nShield-familjen av HSM -enheter (FIPS 140-2-nivå 2 validerat) för att skydda dina nycklar.

Den här funktionen är inte tillgänglig för Azure China 21Vianet.

> [!NOTE]
> Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-overview.md)  
> En självstudiekurs för att komma igång, som inkluderar att skapa ett nyckelvalv för HSM-skyddade nycklar, finns i [Vad är Azure Key Vault?](key-vault-overview.md).

## <a name="supported-hsms"></a>HSM-moduler som stöds

Överföring av HSM-skyddade nycklar till Key Vault stöds via två olika metoder beroende på vilka HSM-moduler du använder. Använd tabellen nedan för att avgöra vilken metod som ska användas för dina HSM:er att generera och överför sedan dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. 

|Leverantörens namn|Leverantörstyp|HSM-modeller som stöds|HSM-nyckelöverföringsmetod som stöds|
|---|---|---|---|
|nChiffrera|Tillverkare|<ul><li>nShield familj av HSMs</li></ul>|[Använda äldre BYOK-metod](hsm-protected-keys-legacy.md)|
|Thales|Tillverkare|<ul><li>SafeNet Luna HSM 7-familjen med firmware version 7.3 eller nyare</li></ul>| [Använda den nya BYOK-metoden (förhandsgranskning)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix (av )|HSM som en tjänst|<ul><li>Självförvaltande nyckelhanteringstjänst (SDKMS)</li></ul>|[Använda den nya BYOK-metoden (förhandsgranskning)](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>Nästa steg

Följ Best Practices för [Key Vault](key-vault-best-practices.md) för att säkerställa säkerhet, hållbarhet och övervakning för dina nycklar.
