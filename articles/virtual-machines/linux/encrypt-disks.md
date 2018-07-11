---
title: Kryptera diskar på en Linux-VM i Azure | Microsoft Docs
description: Kryptera virtuella diskar på en Linux-VM för ökad säkerhet med hjälp av Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 75ec087536d6f833a9a2106b1fdf4ed1fd73ef8e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932419"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Hur du krypterar en Linux-dator i Azure
För förbättrad virtuell dator (VM) säkerhet och efterlevnad, kan virtuella diskar och Virtuellt datorn krypteras. Virtuella datorer krypteras med hjälp av kryptografiska nycklar som skyddas i ett Azure Key Vault. Du kontrollerar dessa kryptografiska nycklar och kan granska deras användning. Den här artikeln beskriver hur du krypterar virtuella diskar på en Linux VM med Azure CLI 2.0. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på virtuella Linux-datorer krypteras i med hjälp av rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Det är kostnadsfritt för att kryptera virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av software protection eller kan du importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Du kan behålla kontrollen över dessa kryptografiska nycklar och kan granska deras användning. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. En Azure Active Directory-tjänstens huvudnamn är en säker mekanism för att utfärda dessa kryptografiska nycklar som virtuella datorer tillhandahålls av och sätts på.

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i ett Azure Key Vault.
2. Konfigurera den kryptografiska nyckeln kan användas för att kryptera diskar.
3. För att läsa den kryptografiska nyckeln från Azure Key Vault, skapa en Azure Active Directory-tjänstens huvudnamn med rätt behörighet.
4. Utfärda kommandot för att kryptera dina virtuella diskar, ange Azure Active Directory-tjänsten huvudnamn och lämpliga kryptografiska nyckel som ska användas.
5. Azure Active Directory-tjänstobjekt begär den nödvändiga kryptografinyckeln från Azure Key Vault.
6. Virtuella diskar krypteras med hjälp av den angivna kryptografiska nyckeln.

## <a name="encryption-process"></a>Krypteringsprocessen
Diskkryptering är beroende av följande ytterligare komponenter:

* **Azure Key Vault** – används för att skydda kryptografiska nycklar och hemligheter som används för att disk kryptering/dekryptering.
  * Om en sådan finns, kan du använda ett befintligt Azure Key Vault. Du behöver inte dedikera en Key Vault för kryptering av diskar.
  * Du kan skapa ett dedikerat Nyckelvalv för att avgränsa administrativa gränser och viktiga synlighet.
* **Azure Active Directory** -hanterar säker byte av obligatoriska kryptografiska nycklar och autentisering för begärda åtgärder.
  * Du kan normalt använda en befintlig Azure Active Directory-instans för ditt program.
  * Tjänstens huvudnamn är en säker mekanism för att begära och utfärdas giltiga kryptografiska nycklar. Du utvecklar inte ett verkligt program som kan integreras med Azure Active Directory.

## <a name="requirements-and-limitations"></a>Krav och begränsningar
Krav för diskkryptering och scenarier som stöds:

* Följande Linux-servern SKU: er – Ubuntu, CentOS, SUSE och SUSE Linux Enterprise Server (SLES) och Red Hat Enterprise Linux.
* Alla resurser (till exempel Key Vault, lagringskontot och virtuella datorer) måste finnas i samma Azure-region och prenumeration.
* Standard A, D, DS, G, GS, etc., virtuella datorer i serien.
* Uppdaterar de kryptografiska nycklarna på en redan krypterade Linux-VM.

Diskkryptering stöds inte för närvarande i följande scenarier:

* Basic-nivån virtuella datorer.
* Virtuella datorer som skapas med hjälp av den klassiska distributionsmodellen.
* Inaktiverar OS-diskkryptering på virtuella Linux-datorer.
* Användning av anpassade Linux-avbildningar.

Mer information om vilka scenarier som stöds och begränsningar finns i [Azure Disk Encryption för virtuella IaaS-datorer](../../security/azure-security-disk-encryption.md)


## <a name="create-an-azure-key-vault-and-keys"></a>Skapa ett Azure Key Vault och nycklar
I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar *myResourceGroup*, *myKey*, och *myVM*.

Det första steget är att skapa ett Azure Key Vault för att lagra kryptografiska nycklar. Azure Key Vault kan lagra nycklar, hemligheter eller lösenord så att du kan implementera dem på ett säkert sätt i dina program och tjänster. För virtuell diskkryptering använder du Key Vault för att lagra en krypteringsnyckel som används för att kryptera eller dekryptera din virtuella diskar.

Aktivera Azure Key Vault-providern i Azure-prenumerationen med [az provider register](/cli/azure/provider#az-provider-register) och skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az-group-create). I följande exempel skapas ett Resursgruppsnamn *myResourceGroup* i den `eastus` plats:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
az group create --name myResourceGroup --location eastus
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste finnas i samma region. Skapa ett Azure Key Vault med [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create) och aktivera Key Vault för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyvault_name* på följande sätt:

```azurecli-interactive
keyvault_name=myuniquekeyvaultname
az keyvault create \
    --name $keyvault_name \
    --resource-group myResourceGroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara Security Model (HSM)-skydd. Använda en HSM kräver en premium Key Vault. Det finns en ytterligare kostnad för att skapa en premium Key Vault i stället för standard Key Vault som lagrar programvaruskyddade nycklar. När du skapar en premium Key Vault i föregående steg till `--sku Premium` i kommandot. I följande exempel används programvaruskyddade nycklar eftersom du har skapat ett Nyckelvalv som standard.

För båda modellerna för skydd måste Azure-plattformen beviljas åtkomst för att begära krypteringsnycklarna när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en krypteringsnyckel i Key Vault med [az keyvault key skapa](/cli/azure/keyvault/key#az-keyvault-key-create). I följande exempel skapas en nyckel med namnet *myKey*:

```azurecli-interactive
az keyvault key create --vault-name $keyvault_name --name myKey --protection software
```


## <a name="create-an-azure-active-directory-service-principal"></a>Skapa ett tjänstens huvudnamn för Azure Active Directory
När virtuella diskar krypteras och dekrypteras, anger du ett konto för att hantera autentiseringen och utbyta kryptografiska nycklar från Key Vault. Det här kontot, ett Azure Active Directory-tjänstobjekt kan Azure-plattformen för att begära giltiga kryptografiska nycklar för den virtuella datorn. En standardinstans för Azure Active Directory är tillgängligt i din prenumeration, även om många organisationer har dedikerade Azure Active Directory-kataloger.

Skapa ett huvudnamn för tjänsten med hjälp av Azure Active Directory med [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac). I följande exempel läser i värden för tjänstens huvudnamn och lösenord för användning i senare kommandon:

```azurecli-interactive
read sp_id sp_password <<< $(az ad sp create-for-rbac --query [appId,password] -o tsv)
```

Lösenordet visas bara när du har skapat tjänsten huvudnamn. Om du vill visa och registrera lösenordet (`echo $sp_password`). Du kan visa din tjänsthuvudnamn med [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) och visa mer information om specifika tjänstens huvudnamn med [az ad sp show](/cli/azure/ad/sp#az-ad-sp-show).

Kryptera eller dekryptera virtuella diskar, måste du ange behörigheter på den kryptografiska nyckel som lagras i Key Vault för att tillåta Azure Active Directory-huvudnamn för tjänsten att läsa nycklarna. Ange behörigheter för Key Vault med [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). I följande exempel anges ID för tjänstens huvudnamn från föregående kommando:

```azurecli-interactive
az keyvault set-policy --name $keyvault_name --spn $sp_id \
  --key-permissions wrapKey \
  --secret-permissions set
```


## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) och bifogar en disk på 5 Gb data. Endast vissa marketplace-avbildningar stöd för diskkryptering. I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Ubuntu 16.04 LTS* bild:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH till den virtuella datorn med den *publicIpAddress* visas i utdata från föregående kommando. Skapa en partition och filsystem och montera datadisken. Mer information finns i [Anslut till en Linux VM att montera den nya disken](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Stäng din SSH-session.


## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn
För att kryptera virtuella diskar kan samla du alla tidigare komponenter:

1. Ange Azure Active Directory-tjänstobjekt och lösenord.
2. Ange Key Vault för att lagra metadata för dina krypterade diskar.
3. Ange de kryptografiska nycklarna som ska användas för den faktiska kryptering och dekryptering.
4. Ange om du vill kryptera OS-disken, datadiskar eller alla.

Kryptera din virtuella dator med [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable). I följande exempel används den *$sp_id* och *$sp_password* variabler från föregående [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) kommando:

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

Det tar lite tid för krypteringsprocessen disken att slutföra. Övervaka status för processen med [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Utdata ser ut ungefär så här trunkerat:

```json
[
  "dataDisk": "EncryptionInProgress",
  "osDisk": "EncryptionInProgress"
]
```

Vänta tills statusen för Operativsystemets disk rapporter **VMRestartPending**, starta om den virtuella datorn med [az vm restart](/cli/azure/vm#az-vm-restart):

```azurecli-interactive
az vm restart --resource-group myResourceGroup --name myVM
```

Krypteringsprocessen disk har slutförts under startprocessen, så Vänta några minuter innan kontrollerar statusen för kryptering igen med [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group myResourceGroup --name myVM
```

Statusen bör nu rapportera både OS-disk och datadisk som **krypterad**.


## <a name="add-additional-data-disks"></a>Lägga till ytterligare datadiskar
När du har krypterade datadiskar kan du också kryptera dem senare lägga till ytterligare virtuella diskar på den virtuella datorn. Exempelvis kan du lägga till en andra virtuell disk till den virtuella datorn på följande sätt:

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
* Läs mer om hur du hanterar Azure Key Vault, inklusive ta bort kryptografiska nycklar och valv, [hantera Key Vault med CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Läs mer om diskkryptering, till exempel förbereda en krypterad anpassad virtuell dator att överföra till Azure, [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
