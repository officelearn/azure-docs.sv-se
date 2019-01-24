---
title: Kryptera diskar på en Linux-VM i Azure | Microsoft Docs
description: Kryptera virtuella diskar på en Linux-VM för ökad säkerhet med hjälp av Azure CLI
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
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 2a057c311a8b080b695492664df0eaabc5609b35
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856799"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Hur du krypterar en Linux-dator i Azure

För förbättrad virtuell dator (VM) säkerhet och efterlevnad, kan virtuella diskar och Virtuellt datorn krypteras. Virtuella datorer krypteras med hjälp av kryptografiska nycklar som skyddas i ett Azure Key Vault. Du kontrollerar dessa kryptografiska nycklar och kan granska deras användning. Den här artikeln beskriver hur du krypterar virtuella diskar på en Linux VM med Azure CLI. 

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Översikt över diskkryptering
Virtuella diskar på virtuella Linux-datorer krypteras i med hjälp av rest [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Det är kostnadsfritt för att kryptera virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av software protection eller kan du importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Du kan behålla kontrollen över dessa kryptografiska nycklar och kan granska deras användning. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. 

Processen för att kryptera en virtuell dator är följande:

1. Skapa en kryptografisk nyckel i ett Azure Key Vault.
1. Konfigurera den kryptografiska nyckeln kan användas för att kryptera diskar.
1. Aktivera diskkryptering för din virtuella diskar.
1. De obligatoriska kryptografiska nycklarna begärs från Azure Key Vault.
1. Virtuella diskar krypteras med hjälp av den angivna kryptografiska nyckeln.


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
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

Azure Key Vault som innehåller kryptografiska nycklar och associerade beräkningsresurser som lagring och Virtuellt datorn måste finnas i samma region. Skapa ett Azure Key Vault med [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create) och aktivera Key Vault för användning med diskkryptering. Ange ett unikt namn för Key Vault för *keyvault_name* på följande sätt:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Du kan lagra kryptografiska nycklar med hjälp av programvara eller maskinvara Security Model (HSM)-skydd. Använda en HSM kräver en premium Key Vault. Det finns en ytterligare kostnad för att skapa en premium Key Vault i stället för standard Key Vault som lagrar programvaruskyddade nycklar. När du skapar en premium Key Vault i föregående steg till `--sku Premium` i kommandot. I följande exempel används programvaruskyddade nycklar eftersom du har skapat ett Nyckelvalv som standard.

För båda modellerna för skydd måste Azure-plattformen beviljas åtkomst för att begära krypteringsnycklarna när den virtuella datorn startas för att dekryptera virtuella diskar. Skapa en krypteringsnyckel i Key Vault med [az keyvault key skapa](/cli/azure/keyvault/key#az-keyvault-key-create). I följande exempel skapas en nyckel med namnet *myKey*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator
Skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) och bifogar en disk på 5 Gb data. Endast vissa marketplace-avbildningar stöd för diskkryptering. I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av en *Ubuntu 16.04 LTS* bild:

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

SSH till den virtuella datorn med den *publicIpAddress* visas i utdata från föregående kommando. Skapa en partition och filsystem och montera datadisken. Mer information finns i [Anslut till en Linux VM att montera den nya disken](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Stäng din SSH-session.


## <a name="encrypt-the-virtual-machine"></a>Kryptera den virtuella datorn


Kryptera din virtuella dator med [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable). I följande exempel används den *$sp_id* och *$sp_password* variabler från föregående [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) kommando:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Det tar lite tid för krypteringsprocessen disken att slutföra. Övervaka status för processen med [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

När du är klar ser utdata ut ungefär som i följande exempel:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Lägga till ytterligare datadiskar
När du har krypterade datadiskar kan du lägga till ytterligare virtuella diskar i den virtuella datorn och kryptera dem. 

När datadisken har lagts till den virtuella datorn, kör du kommandot för att kryptera virtuella diskar på följande sätt:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du hanterar Azure Key Vault, inklusive ta bort kryptografiska nycklar och valv, [hantera Key Vault med CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Läs mer om diskkryptering, till exempel förbereda en krypterad anpassad virtuell dator att överföra till Azure, [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
