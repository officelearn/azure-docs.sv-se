---
title: Så här genererar och överför du HSM-skyddade nycklar för Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Använd den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som du kan använda med Azure Key Vault. Kallas även för att ta med din egen nyckel (BYOK).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 9b8f1065660ea8331853f8804e709134fe682ba7
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566122"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importera HSM-skyddade nycklar till Key Vault (förhandsversion)

> [!NOTE]
> Den här funktionen är i för hands version och endast tillgänglig i Azure-regionerna *USA, östra 2 EUAP* och *centrala US EUAP*. 

För extra trygghet när du använder Azure Key Vault kan du importera eller generera en nyckel i en modul för maskin varu säkerhet (HSM). nyckeln lämnar aldrig HSM-gränser. Det här scenariot kallas ofta för att *ta med din egen nyckel* (BYOK). Key Vault använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er (FIPS 140-2 nivå 2, verifierade) för att skydda dina nycklar.

Använd informationen i den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som du kan använda med Azure Key Vault.

> [!NOTE]
> Den här funktionen är inte tillgänglig för Azure Kina 21Vianet. 
> 
> Den här import metoden är endast tillgänglig för [HSM: er som stöds](#supported-hsms). 

Mer information och en själv studie kurs om hur du kommer igång med Key Vault (inklusive hur du skapar ett nyckel valv för HSM-skyddade nycklar) finns i [Vad är Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Översikt

Här är en översikt över processen. De åtgärder som ska utföras beskrivs senare i artikeln.

* I Key Vault genererar du en nyckel (kallas *nyckel utbytes nyckel* (KEK)). KEK måste vara en RSA-HSM-nyckel som bara har den `import` nyckel åtgärden. Endast Key Vault Premium SKU stöder RSA-HSM-nycklar.
* Hämta den offentliga nyckeln KEK som en. PEM-fil.
* Överför den offentliga KEK-nyckeln till en frånkopplad dator som är ansluten till en lokal HSM.
* I den frånkopplade datorn använder du verktyget BYOK som tillhandahålls av din HSM-leverantör för att skapa en BYOK-fil. 
* Mål nyckeln krypteras med en KEK, som förblir krypterad tills den överförs till Key Vault HSM. Endast den krypterade versionen av nyckeln lämnar den lokala HSM.
* En KEK som genereras inuti en Key Vault HSM kan inte exporteras. HSM: er tillämpar regeln att ingen tydlig version av en KEK finns utanför en Key Vault HSM.
* KEK måste finnas i samma nyckel valv där mål nyckeln kommer att importeras.
* När BYOK-filen överförs till Key Vault, använder en Key Vault HSM den privata KEK-nyckeln för att dekryptera mål nyckel materialet och importera det som en HSM-nyckel. Den här åtgärden sker helt i en Key Vault HSM. Mål nyckeln finns alltid kvar i HSM-skyddets gränser.

## <a name="prerequisites"></a>Förutsättningar

I följande tabell visas förutsättningar för att använda BYOK i Azure Key Vault:

| Krav | Mer information |
| --- | --- |
| En Azure-prenumeration |Om du vill skapa ett nyckel valv i Azure Key Vault behöver du en Azure-prenumeration. [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/). |
| En Key Vault Premium-SKU för import av HSM-skyddade nycklar |Mer information om tjänst nivåer och funktioner i Azure Key Vault finns [Key Vault prissättning](https://azure.microsoft.com/pricing/details/key-vault/). |
| En HSM från listan över HSM: er som stöds och ett BYOK-verktyg och instruktioner från din HSM-leverantör | Du måste ha behörighet för en HSM och grundläggande kunskap om hur du använder HSM. Se [HSM: er som stöds](#supported-hsms). |
| Azure CLI-version 2.1.0 eller senare | Se [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).|

## <a name="supported-hsms"></a>HSM: er som stöds

|HSM-leverantörens namn|HSM-modeller som stöds|Mer information|
|---|---|---|
|Thales|SafeNet Luna HSM 7-serien med inbyggd program vara version 7,3 eller senare| [SafeNet Luna BYOK-verktyg och dokumentation](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|

> [!NOTE]
> Om du vill importera HSM-skyddade nycklar från hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien med HSM: er använder du den [äldre BYOK-proceduren](hsm-protected-keys-legacy.md).

## <a name="supported-key-types"></a>Nyckel typer som stöds

|Nyckelnamn|Nyckeltyp|Nyckel storlek|Ursprung|Beskrivning|
|---|---|---|---|---|
|Nyckel utbytes nyckel (KEK)|RSA| 2 048-bitars<br />3 072-bitars<br />4 096-bitars|Azure Key Vault HSM|Ett HSM-backat RSA-nyckelpar som genererades i Azure Key Vault|
|Mål nyckel|RSA|2 048-bitars<br />3 072-bitars<br />4 096-bitars|Vendor HSM|Nyckeln som ska överföras till Azure Key Vault HSM|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Generera och överför din nyckel till Key Vault HSM

Så här genererar och överför du nyckeln till en Key Vault HSM:

* [Steg 1: skapa en KEK](#step-1-generate-a-kek)
* [Steg 2: Ladda ned den offentliga nyckeln KEK](#step-2-download-the-kek-public-key)
* [Steg 3: generera och Förbered din nyckel för överföring](#step-3-generate-and-prepare-your-key-for-transfer)
* [Steg 4: överför din nyckel till Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Steg 1: skapa en KEK

En KEK är en RSA-nyckel som genereras i en Key Vault HSM. KEK används för att kryptera den nyckel som du vill importera ( *mål* nyckeln).

KEK måste vara:
- En RSA-HSM-nyckel (2 048-bitar; 3 072-bit; eller 4 096-bitars)
- genereras i samma nyckel valv där du tänker importera mål nyckeln
- Har skapats med tillåtna nyckel åtgärder inställt på `import`

Använd kommandot [AZ Key Vault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) för att skapa en KEK som har nyckel åtgärder inställt på `import`. Registrera nyckel identifieraren (`kid`) som returneras från följande kommando. (Du kommer att använda `kid`-värdet i [steg 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Steg 2: Ladda ned den offentliga nyckeln KEK

Använd [AZ Key Vault Key Download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) för att hämta den offentliga KEK-nyckeln till en. PEM-fil. Mål nyckeln som du importerar krypteras med hjälp av den offentliga nyckeln KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Överför filen KEKforBYOK. PublicKey. pem till din frånkopplade dator. Du kommer att behöva den här filen i nästa steg.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Steg 3: generera och Förbered din nyckel för överföring

Se din HSM-leverantörs dokumentation för att ladda ned och installera BYOK-verktyget. Följ anvisningarna från din HSM-leverantör för att generera en mål nyckel och skapa sedan ett nyckel överförings paket (en BYOK-fil). BYOK-verktyget använder `kid` från [steg 1](#step-1-generate-a-kek) och filen KEKforBYOK. PublicKey. pem som du laddade ned i [steg 2](#step-2-download-the-kek-public-key) för att generera en krypterad mål nyckel i en BYOK-fil.

Överför BYOK-filen till den anslutna datorn.

> [!NOTE] 
> Det finns inte stöd för att importera RSA 1 024-bitars nycklar. Det finns för närvarande inte stöd för att importera en Elliptic Curve-nyckel (EC).
> 
> **Känt problem**: det går inte att importera en RSA 4K-mål nyckel från SafeNet Luna HSM: er. När problemet har lösts kommer den här artikeln att uppdateras.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Steg 4: överför din nyckel till Azure Key Vault

Slutför nyckel importen genom att överföra nyckel överförings paketet (en BYOK-fil) från den frånkopplade datorn till den Internet-anslutna datorn. Använd kommandot [AZ Key Vault Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) för att överföra BYOK-filen till Key Vault HSM.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om överföringen lyckas, visar Azure CLI egenskaperna för den importerade nyckeln.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i ditt nyckel valv. Mer information finns i [detta pris-och funktions jämförelse](https://azure.microsoft.com/pricing/details/key-vault/).



