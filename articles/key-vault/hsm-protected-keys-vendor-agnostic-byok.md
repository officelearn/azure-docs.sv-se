---
title: Så här genererar och överför du HSM-skyddade nycklar för Azure Key Vault-Azure Key Vault | Microsoft Docs
description: Använd den här artikeln för att hjälpa dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault. Kallas även BYOK eller ta med din egen nyckel.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 2f5269587d222be9a1628b72c1f3f0dc1b105f3c
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461751"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Importera HSM-skyddade nycklar till Key Vault (för hands version)

> [!NOTE]
> Den här funktionen är en för hands version och endast tillgänglig i regionerna **USA, östra 2 EUAP** och **centrala USA EUAP** . 

För extra trygghet när du använder Azure Key Vault kan du importera eller generera nycklar i HSM: er (Hardware Security modules) som aldrig lämnar HSM-gränser. Det här scenariot kallas ofta för att *ta med din egen nyckel*eller BYOK. Azure Key Vault använder hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er (FIPS 140-2 nivå 2 verifieras) för att skydda dina nycklar.

Använd informationen i det här avsnittet när du planerar för, genererar och överför dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault.

> [!NOTE]
> Den här funktionen är inte tillgänglig för Azure Kina 21Vianet. 
> 
> Den här import metoden är endast tillgänglig för [HSM: er som stöds](#supported-hsms). 

Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](key-vault-overview.md)  En vägledning för att komma igång, vilket innefattar att skapa ett nyckel valv för HSM-skyddade nycklar, finns i [Azure Key Vault?](key-vault-overview.md).

## <a name="overview"></a>Översikt

* Generera en nyckel (kallas Key Exchange Key eller KEK) i Key Vault. Detta måste vara en RSA-HSM-nyckel med ' import ' som den enda nyckel åtgärden. Endast Key Vault Premium SKU stöder RSA-HSM-nycklar.
* Hämta den offentliga nyckeln för KEK som en. PEM-fil
* Överför offentlig KEK-nyckel till din frånkopplade arbets station som är ansluten till en lokal HSM.
* Använd det BYOK-verktyg som tillhandahålls av din HSM-leverantör för att skapa en BYOK-fil från din frånkopplade arbets Station. 
* Mål nyckeln krypteras med en KEK, som förblir krypterad tills den överförs till Azure Key Vault HSM: er. Endast den krypterade versionen av nyckeln lämnar den lokala HSM: en.
* KEK som genereras i Azure Key Vault-HSM: er och kan inte exporteras. HSM: er genomdriver att det inte går att ta bort en tydlig version av KEK utanför Key Vault HSM: er.
* KEK måste finnas i samma nyckel valv där mål nyckeln ska importeras.
* När BYOK-filen laddas upp till Key Vault, Key Vault HSM: er använda KEK privata nyckel för att dekryptera mål nyckel materialet och importera det som en HSM-nyckel. Den här åtgärden sker helt i Key Vault HSM: er och mål nyckeln är alltid kvar i HSM-skyddets gränser.

## <a name="prerequisites"></a>Förutsättningar

I följande tabell finns en lista över förutsättningar för att ta med din egen nyckel (BYOK) för Azure Key Vault.

| Krav | Mer information |
| --- | --- |
| En prenumeration på Azure |Om du vill skapa en Azure Key Vault behöver du en Azure-prenumeration: [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/) |
| Ett nyckel valv (Premium-SKU) för att importera HSM-skyddade nycklar |Mer information om tjänst nivåer och funktioner för Azure Key Vault finns på webbplatsen för [Azure Key Vault prissättning](https://azure.microsoft.com/pricing/details/key-vault/) . |
| En HSM från HSM: er-listan som stöds tillsammans med BYOK-verktyget och instruktioner från din HSM-leverantör | Du måste ha åtkomst till en modul för maskin varu säkerhet och grundläggande operativa kunskaper om din HSM: er. Se [HSM: er som stöds](#supported-hsms). |
| Azure CLI-version 2.1.0 eller senare | Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) .|

## <a name="supported-hsms"></a>HSM: er som stöds

|HSM-leverantörens namn|HSM-modeller som stöds|Ytterligare information|
|---|---|---|
|Thales|SafeNet Luna HSM 7-serien med inbyggd program vara version 7,3 eller senare| [SafeNet Luna BYOK-verktyg och dokumentation](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|


> [!NOTE]
> Så här importerar du HSM-skyddade nycklar från hjälp programmet nCipher nshield maskinvarusäkerhetsmodul-serien HSM: er [use Legacy BYOK Procedure](hsm-protected-keys-legacy.md)


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generera och överför din nyckel till Azure Key Vault HSM

Du kommer att använda följande steg för att generera och överföra din nyckel till en Azure Key Vault HSM:

* [Steg 1: skapa en KEK](#step-1-generate-a-kek)
* [Steg 2: Hämta KEK offentlig nyckel](#step-2-download-kek-public-key)
* [Steg 3: generera och Förbered din nyckel för överföring](#step-3-generate-and-prepare-your-key-for-transfer)
* [Steg 4: överför din nyckel till Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Steg 1: skapa en KEK

KEK (nyckel utbytes nyckel) är en RSA-nyckel som genereras i Key Vaults HSM. Den här nyckeln används för att kryptera nyckeln som ska importeras (mål nyckel).

KEK måste vara:
1. en **RSA-HSM-** nyckel (2048-bitars eller 3072-bitars eller 4096-bitars)
2. genereras i samma nyckel valv där du tänker importera mål nyckeln
3. skapad med tillåten nyckel åtgärd angavs för **import**

Använd kommandot [AZ Key Vault Key Create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) för att skapa KEK med de nyckel åtgärder som ska importeras. Anteckna nyckel identifieraren ' Kill ' som returneras från kommandot nedan. Du behöver det i [steg 3](#step-3-generate-and-prepare-your-key-for-transfer).


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>Steg 2: Hämta KEK offentlig nyckel

Använd [AZ](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) för att ladda ned den offentliga KEK-nyckeln till en. PEM-fil. Mål nyckeln som du importerar krypteras med den offentliga nyckeln KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Överför filen KEKforBYOK. PublicKey. pem till din frånkopplade arbets Station. Du kommer att behöva den här filen under nästa steg.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Steg 3: generera och Förbered din nyckel för överföring

Se din HSM-leverantörs dokumentation för att ladda ned och installera BYOK-verktyget. Följ anvisningarna från din HSM-leverantör för att generera en mål nyckel och skapa sedan ett nyckel överförings paket (en BYOK-fil). BYOK-verktyget använder nyckel identifieraren från [steg 1](#step-1-generate-a-kek) och KEKforBYOK. PublicKey. pem-filen som du laddade ned i [steg 2](#step-2-download-kek-public-key) för att generera en krypterad mål nyckel i en BYOK-fil.

Överför BYOK-filen till den anslutna arbets stationen.

> [!NOTE] 
> Mål nyckeln måste vara en RSA-nyckel med storleken 2048-bitars eller 3072-bitars eller 4096-bitars. Det finns inte stöd för att importera Elliptic kurv nycklar för tillfället.
> <br/><strong>Känt problem:</strong> Det går inte att importera RSA 4K Target-nyckeln från SafeNet Luna HSM: er. När problemet är löst kommer det här dokumentet att uppdateras.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Steg 4: överför din nyckel till Azure Key Vault

I det här sista steget överför du nyckel överförings paketet (en BYOK-fil) från den frånkopplade arbets stationen till den Internet-anslutna arbets stationen och använder sedan kommandot [AZ File Vault Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) för att ladda upp BYOK-filen Azure Key Vault HSM för att slutföra nyckel importen.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Om uppladdningen är klar visas egenskaperna för den nyckel som du precis har importerat.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda den här HSM-skyddade nyckeln i ditt nyckel valv. Mer information finns i detta pris-och funktions [jämförelse](https://azure.microsoft.com/pricing/details/key-vault/).
