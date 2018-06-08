---
title: Kryptera diskar på en Linux-VM i Azure | Microsoft Docs
description: Kryptera virtuella diskar på en Linux-VM för ökad säkerhet med hjälp av Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/31/2018
ms.author: iainfou
ms.openlocfilehash: 86f5124143d0606fd3de4d9f7a49ab31a06b17ae
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839630"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Hur du krypterar en virtuell Linux-dator i Azure
För förbättrad virtuell dator (VM) säkerhet och efterlevnad, kan virtuella diskar och Virtuellt datorn krypteras. Virtuella datorer krypteras med kryptografiska nycklar som skyddas i ett Azure Key Vault. Du styr dessa kryptografiska nycklar och granska deras användning. Den här artikeln beskrivs hur du krypterar virtuella diskar på en Linux-VM som använder Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt i den här artikeln kräver att du använder Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på virtuella Linux-datorer krypteras på resten med hjälp av [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Det är gratis för kryptering av virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med skydd av programvara eller du kan importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) certifierade FIPS 140-2 level 2-standarder. Du behålla kontrollen över dessa kryptografiska nycklar och granska deras användning. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Ett Azure Active Directory-tjänstens huvudnamn ger en säker mekanism för att utfärda de kryptografiska nycklarna som virtuella datorer är igång på och stänga av.

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i en Azure Key Vault.
2. Konfigurera den kryptografiska nyckeln som kan användas för att kryptera diskar.
3. Om du vill läsa den kryptografiska nyckeln från Azure Key Vault, skapa en Azure Active Directory service principal med rätt behörighet.
4. Utfärda kommandot för att kryptera din virtuella diskar, ange den Azure Active Directory service principal och lämpligt kryptografiska nyckeln som ska användas.
5. Azure Active Directory-tjänstens huvudnamn begär den kryptografiska nyckeln som krävs från Azure Key Vault.
6. De virtuella diskarna krypteras med den angivna kryptografiska nyckeln.

## <a name="encryption-process"></a>Krypteringsprocessen
Kryptering är beroende av följande ytterligare komponenter:

* **Azure Key Vault** – används för att skydda krypteringsnycklar och hemligheter som används för kryptering/dekryptering processen disk.
  * Om det finns ett kan du använda en befintlig Azure Key Vault. Du har inte kryptera diskar för ett Nyckelvalv.
  * Du kan skapa ett dedikerat Nyckelvalv för att avgränsa administrativa gränser och viktiga synlighet.
* **Azure Active Directory** -hanterar säker utbyte av kryptografiska nycklar som krävs och autentisering för begärda åtgärder.
  * Vanligtvis kan du använda en befintlig instans av Azure Active Directory för ditt program.
  * Tjänstens huvudnamn ger säker möjlighet att begära och utfärdas lämpliga kryptografiska nycklar. Du utvecklar inte ett verkligt program som integreras med Azure Active Directory.

## <a name="requirements-and-limitations"></a>Krav och begränsningar
Krav för diskkryptering och scenarier som stöds:

* Följande Linux-servern SKU - Ubuntu, CentOS, SUSE och SUSE Linux Enterprise Server (SLES) och Red Hat Enterprise Linux.
* Alla resurser (till exempel Nyckelvalv lagringskonto och VM) måste finnas i samma Azure-region och prenumeration.
* Standard A, D, DS, G, GS, etc., serie virtuella datorer.
* Uppdaterar de kryptografiska nycklarna på en redan krypterade Linux-VM.

Diskkryptering stöds inte i följande scenarier:

* Grundnivån virtuella datorer.
* Virtuella datorer skapas med den klassiska distributionsmodellen.
* Om du inaktiverar kryptering för OS-disk på virtuella Linux-datorer.
* Användning av anpassade Linux-avbildningar.

Mer information om scenarier som stöds och begränsningar finns [Azure Disk Encryption för IaaS-VM](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Skapa ett Azure Key Vault och nycklar
Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar *myResourceGroup*, *MinNyckel*, och *myVM*.

Det första steget är att skapa en Azure Key Vault för att lagra kryptografiska nycklar. Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör det möjligt att implementera dem på ett säkert sätt i dina program och tjänster. För virtuell diskkryptering använder du Key Vault för att lagra en krypteringsnyckel som används för att kryptera eller dekryptera din virtuella diskar.

Aktivera Azure Key Vault-providern i din Azure-prenumeration med [az providern registrera](/cli/azure/provider#az-provider-register) och skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az-group-create). I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den `eastus` plats:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste finnas i samma region. Skapa ett Azure Key Vault med [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create) och aktivera Nyckelvalvet för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyvault_name* på följande sätt:

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara säkerhet modellen (HSM)-skydd. Använda en HSM kräver en premium Key Vault. Det finns en extra kostnad för att skapa en premium Key Vault snarare än standard Key Vault som lagrar programvara-skyddade nycklar. När du skapar en premium Key Vault i föregående steg till `--sku Premium` i kommandot. I följande exempel används programvaruskyddad nycklar eftersom du har skapat ett Nyckelvalv som standard.

För båda modellerna skydd måste Azure-plattformen beviljas åtkomst för att begära kryptografiska nycklar när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en kryptografisk nyckel i ditt Nyckelvalv med [az keyvault nyckel skapa](/cli/azure/keyvault/key#az-keyvault-key-create). I följande exempel skapas en nyckel som heter *MinNyckel*:

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Skapa ett tjänstens huvudnamn för Azure Active Directory
När virtuella diskar krypteras och dekrypteras, anger du ett konto för att hantera autentisering och byta ut kryptografiska nycklar från Nyckelvalvet. Det här kontot, tjänstens huvudnamn för ett Azure Active Directory kan Azure-plattformen begära lämpliga kryptografiska nycklar för den virtuella datorn. En standardinstans för Azure Active Directory finns i din prenumeration, även om många organisationer har särskilda Azure Active Directory-kataloger.

Skapa ett huvudnamn för tjänsten med hjälp av Azure Active Directory med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). I följande exempel läser i värden för tjänstens huvudnamn och lösenord som ska användas i senare kommandon:

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Lösenordet visas bara när du skapar tjänsten huvudnamn. Om du vill visa och registrera lösenordet (`echo $sp_password`). Du kan visa din service principal med [az sp annonslista](/cli/azure/ad/sp#az-ad-sp-list) och visa ytterligare information om en specifik tjänstens huvudnamn med [az ad sp visa](/cli/azure/ad/sp#az-ad-sp-show).

För att kunna kryptera eller dekryptera virtuella diskar, måste behörigheter på den kryptografiska nyckel som lagras i Nyckelvalvet anges för att tillåta Azure Active Directory-tjänstens huvudnamn att läsa nycklarna. Ange behörigheter för ditt Nyckelvalv med [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). I följande exempel anges tjänsten ägar-ID från föregående kommando:

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) och bifoga en disk på 5 Gb data. Endast vissa marketplace-bilder stöd för kryptering. I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Ubuntu 16.04 LTS* avbildningen:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH till virtuell dator med hjälp av den *publicIpAddress* visas i utdata från kommandot ovan. Skapa en partition och filsystemet och sedan montera datadisken. Mer information finns i [Anslut till en Linux-VM för att montera den nya disken](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Stäng SSH-sessionen.


## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn
Om du vill kryptera virtuella diskar samordnar du tidigare komponenter:

1. Ange tjänstens huvudnamn för Azure Active Directory och lösenord.
2. Ange Nyckelvalv för att lagra metadata för krypterade diskarna.
3. Ange de kryptografiska nycklarna som ska användas för den faktiska kryptering och dekryptering.
4. Ange om du vill kryptera OS-disk, datadiskar eller alla.

Kryptera din virtuella dator med [az vm-kryptering aktiverar](/cli/azure/vm/encryption#az-vm-encryption-enable). I följande exempel används den *$sp_id* och *$sp_password* variabler från den föregående [az ad sp skapa-för-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) kommando:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Det tar en stund innan krypteringsprocessen disken att slutföra. Övervaka status för processen med [az vm kryptering visa](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Utdata liknar följande trunkerat exempel:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Vänta tills status för OS-disken rapporter **VMRestartPending**, starta om den virtuella datorn med [az vm omstart](/cli/azure/vm#az-vm-restart):

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

Krypteringsprocessen disk är slutförd under startprocessen, så Vänta några minuter innan du kontrollerar statusen för kryptering igen med [az vm kryptering visa](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Status bör nu rapportera både OS-disk- och datadisk som **krypterade**.


## <a name="add-additional-data-disks"></a>Lägg till ytterligare datadiskar
När du har krypterade datadiskarna kan du också kryptera dem senare lägga till ytterligare virtuella diskar till den virtuella datorn. Exempelvis kan du lägga till en andra virtuell disk till den virtuella datorn på följande sätt:

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --disk myDataDisk \
    --new \
    --size-gb 5
```

Kör kommandot för att kryptera virtuella diskar på följande sätt:

```azurecli-interactive
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```


## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du hanterar Azure Key Vault, inklusive ta bort krypteringsnycklarna och valv, [hantera Key Vault med hjälp av CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Läs mer om diskkryptering, till exempel förbereda en krypterad anpassade VM att överföra till Azure, [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
