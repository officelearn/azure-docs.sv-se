---
title: Skapa en virtuell Linux-dator i Azure från en mall
description: Så här använder du Azure CLI för att skapa en Virtuell Linux-dator från en Resource Manager-mall
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969532"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Skapa en virtuell Linux-dator med Azure Resource Manager-mallar

Lär dig hur du skapar en virtuell Linux-dator (VM) med hjälp av en Azure Resource Manager-mall och Azure CLI från Azure Cloud-skalet. Information om hur du skapar en virtuell Windows-dator finns i [Skapa en virtuell Windows-dator från en Resource Manager-mall](../windows/ps-template.md).

## <a name="templates-overview"></a>Översikt över mallar

Azure Resource Manager-mallar är JSON-filer som definierar infrastrukturen och konfigurationen av din Azure-lösning. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent. Mer information om mallens format och hur du skapar den finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). JSON-syntaxen för resurstyper finns i [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definiera resurser i Azure Resource Manager-mallar).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Att skapa en virtuell Azure-dator innehåller vanligtvis två steg:

1. Skapa en resursgrupp. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn.
1. Skapa en virtuell dator.

I följande exempel skapas en virtuell dator från en [Azure Quickstart-mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Endast SSH-autentisering tillåts för den här distributionen. Ange värdet på din egen SSH-nyckel, till exempel innehållet i *~/.ssh/id_rsa.pub.* Om du behöver skapa ett SSH-nyckelpar läser du Så här skapar och använder du [ett SSH-nyckelpar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md). Här är en kopia av mallen:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Om du vill köra CLI-skriptet väljer du **Prova det** för att öppna Azure Cloud-gränssnittet. Om du vill klistra in skriptet högerklickar du på skalet och väljer sedan **Klistra in:**

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Det senaste Azure CLI-kommandot visar den offentliga IP-adressen för den nyskapade virtuella datorn. Du behöver den offentliga IP-adressen för att ansluta till den virtuella datorn. Se nästa avsnitt i den här artikeln.

I föregående exempel angav du en mall som lagras i GitHub. Du kan också hämta eller skapa en mall `--template-file` och ange den lokala sökvägen med parametern.

Här följer några ytterligare resurser:

- Mer information om hur du utvecklar Resource Manager-mallar finns i [Azure Resource Manager-dokumentationen](/azure/azure-resource-manager/).
- Information om hur du ser Azure-schemana för virtuella datorer finns i [Azure-mallreferens](/azure/templates/microsoft.compute/allversions).
- Mer information om hur du ser fler exempel på mallar för virtuella datorer finns i [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Du kan sedan SSH till din virtuella dator som vanligt. Ange egen offentlig IP-adress från föregående kommando:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Nästa steg

I det här exemplet har du skapat en grundläggande virtuell Linux-dator. Om du vill ha fler Resource Manager-mallar som innehåller programramverk eller skapar mer komplexa miljöer bläddrar du i [Azure Quickstart-mallarna](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Om du vill veta mer om hur du skapar mallar visar du JSON-syntaxen och egenskaperna för de resurstyper som du har distribuerat:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAdresser](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
