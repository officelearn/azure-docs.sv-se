---
title: "Kryptera diskar på en Linux-VM i Azure | Microsoft Docs"
description: "Kryptera virtuella diskar på en Linux-VM för ökad säkerhet med hjälp av Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/05/2017
ms.author: iainfou
ms.openlocfilehash: 172b4c8f5c098d776cb689543f5d8f163b8895b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-encrypt-virtual-disks-on-a-linux-vm"></a>Kryptera virtuella diskar på en Linux-VM
För förbättrad virtuell dator (VM) säkerhet och efterlevnad krypteras virtuella diskar i Azure. Diskar krypteras med kryptografiska nycklar som skyddas i ett Azure Key Vault. Du styr dessa kryptografiska nycklar och granska deras användning. Den här artikeln beskrivs hur du krypterar virtuella diskar på en Linux-VM som använder Azure CLI 2.0. Du kan också utföra dessa steg med [Azure CLI 1.0](encrypt-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Snabbkommandon
Om du behöver utföra aktiviteten följande avsnittet beskriver grundläggande kommandon för att kryptera virtuella diskar på den virtuella datorn. Mer detaljerad information och kontext för varje steg finns resten av dokumentet [startar här](#overview-of-disk-encryption).

Du behöver senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login). Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar *myResourceGroup*, *MinNyckel*, och *myVM*.

Först aktiverar Azure Key Vault-providern i din Azure-prenumeration med [az providern registrera](/cli/azure/provider#register) och skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den *eastus* plats:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Skapa ett Azure Key Vault med [az keyvault skapa](/cli/azure/keyvault#create) och aktivera Nyckelvalvet för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyvault_name* på följande sätt:

```azurecli
keyvault_name=mykeyvaultikf
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Skapa en kryptografisk nyckel i ditt Nyckelvalv med [az keyvault nyckel skapa](/cli/azure/keyvault/key#create). I följande exempel skapas en nyckel som heter *MinNyckel*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```

Skapa ett huvudnamn för tjänsten med hjälp av Azure Active Directory med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac). Tjänstens huvudnamn hanterar autentisering och utbyte av kryptografiska nycklar från Nyckelvalvet. I följande exempel läser i värden för tjänstens huvudnamn-Id och lösenord för användning i senare kommandon:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Lösenordet är bara utdata när du skapar tjänstens huvudnamn. Om du vill visa och registrera lösenordet (`echo $sp_password`). Du kan visa din service principal med [az sp annonslista](/cli/azure/ad/sp#list) och visa ytterligare information om en specifik tjänstens huvudnamn med [az ad sp visa](/cli/azure/ad/sp#show).

Ange behörigheter för ditt Nyckelvalv med [az keyvault set-policy](/cli/azure/keyvault#set-policy). I följande exempel anges tjänsten ägar-ID från föregående kommando:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
    --key-permissions wrapKey \
    --secret-permissions set
```

Skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create) och bifoga en disk på 5 Gb data. Endast vissa marketplace-bilder stöd för kryptering. I följande exempel skapas en virtuell dator med namnet `myVM` med hjälp av en **CentOS 7.2n** avbildningen:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH till virtuell dator med hjälp av den `publicIpAddress` visas i utdata från kommandot ovan. Skapa en partition och filsystemet och sedan montera datadisken. Mer information finns i [Anslut till en Linux-VM för att montera den nya disken](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Stäng SSH-sessionen.

Kryptera din virtuella dator med [az vm-kryptering aktiverar](/cli/azure/vm/encryption#enable). I följande exempel används den `$sp_id` och `$sp_password` variabler från den föregående `ad sp create-for-rbac` kommando:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Det tar en stund innan krypteringsprocessen disken att slutföra. Övervaka status för processen med [az vm kryptering visa](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Status visas **EncryptionInProgress**. Vänta tills status för OS-disken rapporter **VMRestartPending**, starta om den virtuella datorn med [az vm omstart](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Krypteringsprocessen disk är slutförd under startprocessen, så Vänta några minuter innan du kontrollerar statusen för kryptering igen med **az vm kryptering visa**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Status bör nu rapportera både OS-disk- och datadisk som **krypterade**.

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
* Standard A, D, DS, G och GS-serien virtuella datorer.

Diskkryptering stöds inte i följande scenarier:

* Grundnivån virtuella datorer.
* Virtuella datorer skapas med den klassiska distributionsmodellen.
* Om du inaktiverar kryptering för OS-disk på virtuella Linux-datorer.
* Uppdaterar de kryptografiska nycklarna på en redan krypterade Linux-VM.

## <a name="create-azure-key-vault-and-keys"></a>Skapa Azure Key Vault och nycklar
Du behöver senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login). Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar *myResourceGroup*, *MinNyckel*, och *myVM*.

Det första steget är att skapa en Azure Key Vault för att lagra kryptografiska nycklar. Azure Key Vault kan lagra nycklar, hemligheter eller lösenord som gör det möjligt att implementera dem på ett säkert sätt i dina program och tjänster. För virtuell diskkryptering använder du Key Vault för att lagra en krypteringsnyckel som används för att kryptera eller dekryptera din virtuella diskar.

Aktivera Azure Key Vault-providern i din Azure-prenumeration med [az providern registrera](/cli/azure/provider#register) och skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den `eastus` plats:

```azurecli
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste finnas i samma region. Skapa ett Azure Key Vault med [az keyvault skapa](/cli/azure/keyvault#create) och aktivera Nyckelvalvet för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyvault_name* på följande sätt:

```azurecli
keyvault_name=myUniqueKeyVaultName
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara säkerhet modellen (HSM)-skydd. Använda en HSM kräver en premium Key Vault. Det finns en extra kostnad för att skapa en premium Key Vault snarare än standard Key Vault som lagrar programvara-skyddade nycklar. När du skapar en premium Key Vault i föregående steg till `--sku Premium` i kommandot. I följande exempel används programvaruskyddad nycklar eftersom vi har skapat ett Nyckelvalv som standard.

För båda modellerna skydd måste Azure-plattformen beviljas åtkomst för att begära kryptografiska nycklar när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en kryptografisk nyckel i ditt Nyckelvalv med [az keyvault nyckel skapa](/cli/azure/keyvault/key#create). I följande exempel skapas en nyckel som heter *MinNyckel*:

```azurecli
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-the-azure-active-directory-service-principal"></a>Skapa Azure Active Directory huvudnamn för tjänsten
När virtuella diskar krypteras och dekrypteras, anger du ett konto för att hantera autentisering och byta ut kryptografiska nycklar från Nyckelvalvet. Det här kontot, tjänstens huvudnamn för ett Azure Active Directory kan Azure-plattformen begära lämpliga kryptografiska nycklar för den virtuella datorn. En standardinstans för Azure Active Directory finns i din prenumeration, även om många organisationer har särskilda Azure Active Directory-kataloger.

Skapa ett huvudnamn för tjänsten med hjälp av Azure Active Directory med [az ad sp skapa-för-rbac](/cli/azure/ad/sp#create-for-rbac). I följande exempel läser i värden för tjänstens huvudnamn-Id och lösenord för användning i senare kommandon:

```azurecli
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Lösenordet visas bara när du skapar tjänsten huvudnamn. Om du vill visa och registrera lösenordet (`echo $sp_password`). Du kan visa din service principal med [az sp annonslista](/cli/azure/ad/sp#list) och visa ytterligare information om en specifik tjänstens huvudnamn med [az ad sp visa](/cli/azure/ad/sp#show).

För att kunna kryptera eller dekryptera virtuella diskar, måste behörigheter på den kryptografiska nyckel som lagras i Nyckelvalvet anges för att tillåta Azure Active Directory-tjänstens huvudnamn att läsa nycklarna. Ange behörigheter för ditt Nyckelvalv med [az keyvault set-policy](/cli/azure/keyvault#set-policy). I följande exempel anges tjänsten ägar-ID från föregående kommando:

```azurecli
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Att faktiskt kryptera vissa virtuella diskar, kan du skapa en virtuell dator och lägger till en datadisk. Skapa en virtuell dator för att kryptera med [az vm skapa](/cli/azure/vm#create) och bifoga en disk på 5 Gb data. Endast vissa marketplace-bilder stöd för kryptering. I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en **CentOS 7.2n** avbildningen:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image OpenLogic:CentOS:7.2n:7.2.20160629 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH till virtuell dator med hjälp av den `publicIpAddress` visas i utdata från kommandot ovan. Skapa en partition och filsystemet och sedan montera datadisken. Mer information finns i [Anslut till en Linux-VM för att montera den nya disken](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Stäng SSH-sessionen.


## <a name="encrypt-virtual-machine"></a>Kryptera en virtuell dator
Om du vill kryptera virtuella diskar samordnar du tidigare komponenter:

1. Ange tjänstens huvudnamn för Azure Active Directory och lösenord.
2. Ange Nyckelvalv för att lagra metadata för krypterade diskarna.
3. Ange de kryptografiska nycklarna som ska användas för den faktiska kryptering och dekryptering.
4. Ange om du vill kryptera OS-disk, datadiskar eller alla.

Kryptera din virtuella dator med [az vm-kryptering aktiverar](/cli/azure/vm/encryption#enable). I följande exempel används den `$sp_id` och `$sp_password` variabler från den föregående `ad sp create-for-rbac` kommando:

```azurecli
az vm encryption enable \
    --resource-group myResourceGroup \
    --name myVM \
    --aad-client-id $sp_id \
    --aad-client-secret $sp_password \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Det tar en stund innan krypteringsprocessen disken att slutföra. Övervaka status för processen med [az vm kryptering visa](/cli/azure/vm/encryption#show):

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Utdata liknar följande trunkerat exempel:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress",
]
```

Vänta tills status för OS-disken rapporter **VMRestartPending**, starta om den virtuella datorn med [az vm omstart](/cli/azure/vm#restart):

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

Krypteringsprocessen disk är slutförd under startprocessen, så Vänta några minuter innan du kontrollerar statusen för kryptering igen med **az vm kryptering visa**:

```azurecli
az vm encryption show --resource-group myResourceGroup --name myVM
```

Status bör nu rapportera både OS-disk- och datadisk som **krypterade**.


## <a name="add-additional-data-disks"></a>Lägg till ytterligare datadiskar
När du har krypterade datadiskarna kan du också kryptera dem senare lägga till ytterligare virtuella diskar till den virtuella datorn. Exempelvis kan du lägga till en andra virtuell disk till den virtuella datorn på följande sätt:

```azurecli
az vm disk attach-new --resource-group myResourceGroup --vm-name myVM --size-in-gb 5
```

Nytt Kör kommando för att kryptera virtuella diskar på följande sätt:

```azurecli
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
