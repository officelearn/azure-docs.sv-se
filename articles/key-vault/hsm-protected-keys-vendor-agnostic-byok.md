---
title: Så här genererar och överför HSM-skyddade nycklar för Azure Key Vault - Azure Key Vault | Microsoft-dokument
description: Använd den här artikeln för att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Även känd som ta med din egen nyckel (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080135"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importera HSM-skyddade nycklar till Key Vault (förhandsversion)

> [!NOTE]
> Den här funktionen är i förhandsversion och endast tillgänglig i Azure-regionerna *Östra USA 2 EUAP* och *centrala USA EUAP*. 

För extra säkerhet när du använder Azure Key Vault kan du importera eller generera en nyckel i en maskinvarusäkerhetsmodul (HSM); nyckeln aldrig kommer att lämna HSM-gränsen. Det här scenariot kallas ofta för att *ta med din egen nyckel* (BYOK). Key Vault använder nCipher nShield-familjen av HSM -skivor (FIPS 140-2-nivå 2 validerad) för att skydda dina nycklar.

Använd informationen i den här artikeln för att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault.

> [!NOTE]
> Den här funktionen är inte tillgänglig för Azure China 21Vianet. 
> 
> Den här importmetoden är endast tillgänglig för [HSM-moduler som stöds](#supported-hsms). 

Mer information och en självstudiekurs för att komma igång med Key Vault (inklusive hur du skapar ett nyckelvalv för HSM-skyddade nycklar) finns i [Vad är Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Översikt

Här är en översikt över processen. Specifika steg som ska slutföras beskrivs senare i artikeln.

* I Key Vault genererar du en nyckel (kallas *en nyckel för exchange-nyckel* (KEK)). KEK måste vara en RSA-HSM-nyckel `import` som bara har nyckelåtgärden. Endast Key Vault Premium SKU stöder RSA-HSM-nycklar.
* Ladda ner KEK:s offentliga nyckel som en PEM-fil.
* Överför den offentliga KEK-nyckeln till en offlinedator som är ansluten till en lokal HSM.
* I offline-datorn använder du BYOK-verktyget som tillhandahålls av HSM-leverantören för att skapa en BYOK-fil. 
* Målnyckeln krypteras med en KEK, som förblir krypterad tills den överförs till Key Vault HSM. Endast den krypterade versionen av din nyckel lämnar den lokala HSM.
* En KEK som genereras inuti en Key Vault HSM kan inte exporteras. HSM tillämpar regeln att det inte finns någon tydlig version av en KEK utanför en Key Vault HSM.
* KEK måste finnas i samma nyckelvalv där målnyckeln importeras.
* När BYOK-filen överförs till Key Vault använder en Key Vault HSM den privata KEK-nyckeln för att dekryptera målnyckelmaterialet och importera det som en HSM-nyckel. Den här åtgärden sker helt inuti en Key Vault HSM. Målnyckeln finns alltid kvar i HSM-skyddsgränsen.

## <a name="prerequisites"></a>Krav

I följande tabell visas förutsättningar för att använda BYOK i Azure Key Vault:

| Krav | Mer information |
| --- | --- |
| En Azure-prenumeration |Om du vill skapa ett nyckelvalv i Azure Key Vault behöver du en Azure-prenumeration. [Registrera dig för en gratis testperiod](https://azure.microsoft.com/pricing/free-trial/). |
| En Key Vault Premium SKU för att importera HSM-skyddade nycklar |Mer information om tjänstnivåer och funktioner i Azure Key Vault finns i Prissättning av [nyckelvalv](https://azure.microsoft.com/pricing/details/key-vault/). |
| En HSM från listan HSM-skivor som stöds och ett BYOK-verktyg och instruktioner från din HSM-leverantör | Du måste ha behörighet för en HSM och grundläggande kunskaper om hur du använder din HSM. Se [HSM-skivor som stöds](#supported-hsms). |
| Azure CLI version 2.1.0 eller senare | Se [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSM-moduler som stöds

|Leverantörens namn|Leverantörstyp|HSM-modeller som stöds|Mer information|
|---|---|---|---|
|Thales|Tillverkare|SafeNet Luna HSM 7-familjen med firmware version 7.3 eller senare| [SafeNet Luna BYOK verktyg och dokumentation](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix (av )|HSM som en tjänst|Självförvaltande nyckelhanteringstjänst (SDKMS)|[Exportera SDKMS-nycklar till molnleverantörer för BYOK - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> Om du vill importera HSM-skyddade nycklar från nCipher nShield-familjen av HSM-tillverkare använder du den [äldre BYOK-proceduren](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Nyckeltyper som stöds

|Nyckelnamn|Nyckeltyp|Nyckelstorlek|Ursprung|Beskrivning|
|---|---|---|---|---|
|Nyckel för nyckelutbyte (KEK)|RSA| 2 048 bitar<br />3 072-bitars<br />4 096 bitar|Azure Key Vault HSM|Ett HSM-stödda RSA-nyckelpar som genereras i Azure Key Vault|
|Målnyckel|RSA|2 048 bitar<br />3 072-bitars<br />4 096 bitar|Leverantör HSM|Nyckeln som ska överföras till Azure Key Vault HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generera och överför din nyckel till Key Vault HSM

Så här genererar och överför du nyckeln till en Key Vault HSM:

* [Steg 1: Generera en KEK](#step-1-generate-a-kek)
* [Steg 2: Ladda ner DEN offentliga KEK-nyckeln](#step-2-download-the-kek-public-key)
* [Steg 3: Generera och förbered din nyckel för överföring](#step-3-generate-and-prepare-your-key-for-transfer)
* [Steg 4: Överför din nyckel till Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Steg 1: Generera en KEK

En KEK är en RSA-nyckel som genereras i en Key Vault HSM. KEK används för att kryptera nyckeln som du vill importera *(målnyckeln).*

KEK måste vara:
- En RSA-HSM-nyckel (2 048 bitars; 3 072-bitars eller 4 096 bitars)
- Genereras i samma nyckelvalv där du tänker importera målnyckeln
- Skapad med tillåtna nyckelåtgärder inställda på`import`

> [!NOTE]
> KEK måste ha "import" som den enda tillåtna nyckelåtgärden. "import" utesluter varandra med alla andra viktiga transaktioner.

Använd kommandot [az keyvault-tangenten skapa](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) för att skapa `import`en KEK som har nyckelåtgärder inställda på . Registrera nyckelidentifieraren`kid`( ) som returneras från följande kommando. (Du kommer `kid` att använda värdet i [steg 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Steg 2: Ladda ner DEN offentliga KEK-nyckeln

Använd [az keyvault nyckel nedladdning](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) för att ladda ner KEK offentlig nyckel till en .pem fil. Målnyckeln du importerar krypteras med hjälp av den offentliga KEK-nyckeln.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Överför filen KEKforBYOK.publickey.pem till offlinedatorn. Du behöver den här filen i nästa steg.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Steg 3: Generera och förbered din nyckel för överföring

Se HSM-leverantörens dokumentation för att hämta och installera BYOK-verktyget. Följ instruktionerna från din HSM-leverantör för att generera en målnyckel och skapa sedan ett nyckelöverföringspaket (en BYOK-fil). Byok-verktyget använder `kid` filen från [steg 1](#step-1-generate-a-kek) och filen KEKforBYOK.publickey.pem som du hämtade i [steg 2](#step-2-download-the-kek-public-key) för att generera en krypterad målnyckel i en BYOK-fil.

Överför BYOK-filen till den anslutna datorn.

> [!NOTE] 
> Det går inte att importera RSA 1 024-bitarsnycklar. För närvarande stöds inte import av en EG-nyckel (Elliptic Curve).
> 
> **Känt problem:** Importera en RSA 4K-målnyckel från SafeNet Luna HSMs stöds endast med firmware 7.4.0 eller nyare.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Steg 4: Överför din nyckel till Azure Key Vault

Om du vill slutföra nyckelimporten överför du nyckelöverföringspaketet (en BYOK-fil) från den frånkopplade datorn till den internetanslutna datorn. Använd kommandot [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) för att överföra BYOK-filen till Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om överföringen lyckas visar Azure CLI egenskaperna för den importerade nyckeln.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i nyckelvalvet. Mer information finns i [den här pris- och funktionsjämna jämförelsen](https://azure.microsoft.com/pricing/details/key-vault/).



