---
title: Kryptera diskar på en Linux-VM med Azure CLI 1.0 | Microsoft Docs
description: Hur du krypterar diskar på en Linux VM som använder Azure CLI 1.0 och Resource Manager-distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: d1704ba37b1d2acc595cb0c354b22bfcf1c57036
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Kryptera diskar på en Linux VM som använder Azure CLI 1.0
För förbättrad virtuell dator (VM) säkerhet och efterlevnad, kan virtuella diskar i Azure krypteras i vila. Diskar krypteras med kryptografiska nycklar som skyddas i ett Azure Key Vault. Du styr dessa kryptografiska nycklar och granska deras användning. Den här artikeln beskrivs hur du krypterar virtuella diskar på en Linux VM som använder Azure CLI 1.0 och Resource Manager-distributionsmodellen.

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#quick-commands) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="quick-commands"></a>Snabbkommandon
Om du behöver utföra aktiviteten följande avsnittet beskriver grundläggande kommandon för att kryptera virtuella diskar på den virtuella datorn. Mer detaljerad information och kontext för varje steg finns resten av dokumentet [startar här](#overview-of-disk-encryption).

Du behöver den [senaste Azure CLI 1.0](../../xplat-cli-install.md) installerad och logga in med Resource Manager-läget på följande sätt:

```azurecli
azure config mode arm
```

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar `myResourceGroup`, `myKeyVault`, och `myVM`.

Först aktivera Azure Key Vault-providern i din Azure-prenumeration och skapa en resursgrupp. I följande exempel skapas ett Resursgruppsnamn `myResourceGroup` i den `WestUS` plats:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Skapa ett Azure Key Vault. I följande exempel skapas ett Nyckelvalv med namnet `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Skapa en kryptografisk nyckel i ditt Nyckelvalv och aktivera kryptering. I följande exempel skapas en nyckel som heter `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Skapa en slutpunkt som använder Azure Active Directory för att hantera autentisering och byta ut kryptografiska nycklar från Nyckelvalvet. Den `--home-page` och `--identifier-uris` inte behöver vara faktiska dirigerbara adresser. Högsta möjliga säkerhetsnivå ska klienthemlighet användas i stället för lösenord. Azure CLI generera inte för närvarande klienthemlighet. Klienthemlighet kan bara skapas i Azure-portalen. I följande exempel skapas en Azure Active Directory-slutpunkt med namnet `myAADApp` och använder ett lösenord på `myPassword`. Ange ditt eget lösenord på följande sätt:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Observera den `applicationId` visas i utdata från kommandot ovan. Program-ID används i följande steg:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Lägg till en datadisk till en befintlig virtuell dator. I följande exempel läggs en datadisk till en virtuell dator med namnet `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Granska informationen för Key Vault och den nyckel som du skapade. Key Vault-ID, URI och nyckel måste URL: en i det sista steget. I följande exempel granskar detaljerna för ett Nyckelvalv med namnet `myKeyVault` och nyckel som heter `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Kryptera din diskar på följande sätt att ange egna parameternamn i hela:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI anger inte utförlig fel under krypteringsprocessen. Ytterligare felsökningsinformation `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Som föregående kommando har många variabler och kan ge mycket uppgift om varför processen misslyckas, är ett exempel på fullständiga kommandot på följande sätt:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Slutligen granska krypteringsstatus igen för att bekräfta att din virtuella diskar nu har krypterats. I följande exempel kontrollerar status för en virtuell dator med namnet `myVM` i den `myResourceGroup` resursgrupp:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på virtuella Linux-datorer krypteras på resten med hjälp av [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Det är gratis för kryptering av virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med skydd av programvara eller du kan importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) certifierade FIPS 140-2 level 2-standarder. Du behålla kontrollen över dessa kryptografiska nycklar och granska deras användning. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. En slutpunkt för Azure Active Directory ger en säker mekanism för att utfärda de kryptografiska nycklarna som virtuella datorer är igång på och stänga av.

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i en Azure Key Vault.
2. Konfigurera den kryptografiska nyckeln som kan användas för att kryptera diskar.
3. Skapa en slutpunkt som använder Azure Active Directory med rätt behörighet för att läsa den kryptografiska nyckeln från Azure Key Vault.
4. Utfärda kommandot för att kryptera din virtuella diskar, Azure Active Directory-slutpunkten och lämpliga kryptografiska nyckel som ska användas.
5. Azure Active Directory-slutpunkten begär den kryptografiska nyckeln som krävs från Azure Key Vault.
6. De virtuella diskarna krypteras med den angivna kryptografiska nyckeln.

## <a name="supporting-services-and-encryption-process"></a>Stöd för tjänster och kryptering
Kryptering är beroende av följande ytterligare komponenter:

* **Azure Key Vault** – används för att skydda krypteringsnycklar och hemligheter som används för kryptering/dekryptering processen disk.
  * Om det finns ett kan du använda en befintlig Azure Key Vault. Du har inte kryptera diskar för ett Nyckelvalv.
  * Du kan skapa ett dedikerat Nyckelvalv för att avgränsa administrativa gränser och viktiga synlighet.
* **Azure Active Directory** -hanterar säker utbyte av kryptografiska nycklar som krävs och autentisering för begärda åtgärder.
  * Vanligtvis kan du använda en befintlig instans av Azure Active Directory för ditt program.
  * Programmet är av en slutpunkt för Nyckelvalvet och virtuella datorn att begära och hämta utfärdade lämpliga kryptografiska nycklar. Du utvecklar inte ett verkligt program som integreras med Azure Active Directory.

## <a name="requirements-and-limitations"></a>Krav och begränsningar
Krav för diskkryptering och scenarier som stöds:

* Följande Linux-servern SKU - Ubuntu, CentOS, SUSE och SUSE Linux Enterprise Server (SLES) och Red Hat Enterprise Linux.
* Alla resurser (till exempel Nyckelvalv lagringskonto och VM) måste finnas i samma Azure-region och prenumeration.
* Standard A, D, DS, G och GS-serien virtuella datorer.

Diskkryptering stöds inte i följande scenarier:

* Grundnivån virtuella datorer.
* Virtuella datorer skapas med den klassiska distributionsmodellen.
* Om du inaktiverar kryptering för OS-disk på virtuella Linux-datorer.
* Uppdaterar de kryptografiska nycklarna på en redan krypterade Linux-VM.

## <a name="create-the-azure-key-vault-and-keys"></a>Skapa Azure Key Vault och nycklar
Slutför resten av den här guiden, måste den [senaste Azure CLI 1.0](../../xplat-cli-install.md) installerad och logga in med Resource Manager-läget på följande sätt:

```azurecli
azure config mode arm
```

Ersätt alla exempel parametrar med egna namn, plats och nyckelvärden under hela kommandoexempel. I följande exempel används en konvention för `myResourceGroup`, `myKeyVault`, `myAADApp`osv.

Det första steget är att skapa en Azure Key Vault för att lagra kryptografiska nycklar. Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör det möjligt att implementera dem på ett säkert sätt i dina program och tjänster. För virtuell diskkryptering använder du Key Vault för att lagra en krypteringsnyckel som används för att kryptera eller dekryptera din virtuella diskar.

Aktivera Azure Key Vault-providern i din Azure-prenumeration och sedan skapa en resursgrupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i den `WestUS` plats:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste finnas i samma region. I följande exempel skapas en Azure Key Vault som heter `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara säkerhet modellen (HSM)-skydd. Använda en HSM kräver en premium Key Vault. Det finns en extra kostnad för att skapa en premium Key Vault snarare än standard Key Vault som lagrar programvara-skyddade nycklar. När du skapar en premium Key Vault i föregående steg till `--sku Premium` i kommandot. I följande exempel används programvaruskyddad nycklar eftersom vi har skapat ett Nyckelvalv som standard.

För båda modellerna skydd måste Azure-plattformen beviljas åtkomst för att begära kryptografiska nycklar när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en krypteringsnyckel i ditt Nyckelvalv och sedan aktivera den för användning med virtuell diskkryptering. I följande exempel skapas en nyckel som heter `myKey` och gör det möjligt för diskkryptering:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Skapa Azure Active Directory-program
När virtuella diskar krypteras och dekrypteras, använder du en slutpunkt för att hantera autentisering och byta ut kryptografiska nycklar från Nyckelvalvet. Den här slutpunkten måste ett Azure Active Directory-program kan Azure-plattformen begära lämpliga kryptografiska nycklar för den virtuella datorn. En standardinstans för Azure Active Directory finns i din prenumeration, även om många organisationer har särskilda Azure Active Directory-kataloger.

När du inte skapar en fullständig Azure Active Directory-program i `--home-page` och `--identifier-uris` parametrar i följande exempel behöver inte vara faktiska dirigerbara adresser. I följande exempel anger också en lösenordsbaserad hemlighet i stället för att nycklarna från i Azure-portalen. Som den här tiden kan kan generera nycklar inte utföras från Azure CLI.

Skapa Azure Active Directory-program. I följande exempel skapas ett program med namnet `myAADApp` och använder ett lösenord på `myPassword`. Ange ditt eget lösenord på följande sätt:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Anteckna den `applicationId` som returneras i utdata från kommandot ovan. Program-ID används i vissa av stegen. Skapa sedan en tjänstens huvudnamn (SPN) så att programmet är tillgänglig i din miljö. För att kunna kryptera eller dekryptera virtuella diskar, måste behörigheter på den kryptografiska nyckel som lagras i Nyckelvalvet anges för att tillåta Azure Active Directory-programmet att läsa nycklarna.

Skapa ett SPN och ange de behörigheter som krävs på följande sätt:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Lägg till en virtuell disk och granska krypteringsstatus
Att faktiskt kryptera vissa virtuella diskar, kan du lägga till en disk i en befintlig virtuell dator. Lägg till en disk på 5Gb data till en befintlig virtuell dator på följande sätt:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

De virtuella diskarna krypteras inte. Granska aktuella krypteringsstatus för den virtuella datorn på följande sätt:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Kryptera virtuella diskar
För att kryptera nu virtuella diskar, samordnar du tidigare komponenter:

1. Ange Azure Active Directory-program och lösenord.
2. Ange Nyckelvalv för att lagra metadata för krypterade diskarna.
3. Ange de kryptografiska nycklarna som ska användas för den faktiska kryptering och dekryptering.
4. Ange om du vill kryptera OS-disk, datadiskar eller alla.

Kan granska informationen för Azure Key Vault och den nyckel som du skapade när du behöver Key Vault ID URI, och ange sedan URL det sista steget:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Kryptera din virtuella diskar med hjälp av utdata från den `azure keyvault show` och `azure keyvault key show` kommandon på följande sätt:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Som det föregående kommandot har många variabler, är följande exempel det fullständiga kommandot referens:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI anger inte utförlig fel under krypteringsprocessen. Ytterligare felsökningsinformation `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` på den virtuella datorn du krypterar.

Slutligen kan du granska krypteringsstatus igen för att bekräfta att din virtuella diskar nu har krypterats:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Lägg till ytterligare datadiskar
När du har krypterade datadiskarna kan du också kryptera dem senare lägga till ytterligare virtuella diskar till den virtuella datorn. När du kör den `azure vm enable-disk-encryption` kommandot, öka sekvens version använder den `--sequence-version` parameter. Den här aktivitetssekvensen versionsparametern kan du utföra upprepade åtgärder på samma virtuella dator.

Exempelvis kan du lägga till en andra virtuell disk till den virtuella datorn på följande sätt:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Kör kommandot för att kryptera de virtuella diskarna denna tid att lägga till den `--sequence-version` parameter och öka värdet från våra första gången du kör på följande sätt:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du hanterar Azure Key Vault, inklusive ta bort krypteringsnycklarna och valv, [hantera Key Vault med hjälp av CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Läs mer om diskkryptering, till exempel förbereda en krypterad anpassade VM att överföra till Azure, [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
