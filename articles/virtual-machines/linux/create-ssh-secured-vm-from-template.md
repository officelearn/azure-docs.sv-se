---
title: Skapa en Linux-VM i Azure från en mall | Microsoft Docs
description: Hur du använder Azure CLI för att skapa en Linux VM från en Resource Manager-mall
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 334f69390e4506c6db76c1814f8ec8f1e4417ee9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60328008"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Hur du skapar en Linux-dator med Azure Resource Manager-mallar

Lär dig hur du skapar en Linux-dator (VM) med hjälp av en Azure Resource Manager-mall och Azure CLI från Azure Cloud shell. Om du vill skapa en Windows-dator, se [skapa en Windows-dator från en Resource Manager-mall](../windows/ps-template.md).

## <a name="templates-overview"></a>Översikt över mallar

Azure Resource Manager-mallar är JSON-filer som definierar infrastrukturen och konfigurationen av din Azure-lösning. Genom att använda en mall kan du distribuera lösningen flera gånger under dess livscykel och vara säker på att dina resurser distribueras konsekvent. Mer information om mallformatet och hur du konstruerar finns [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). JSON-syntaxen för resurstyper finns i [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Definiera resurser i Azure Resource Manager-mallar).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en Azure virtuell dator vanligtvis innehåller två steg:

1. Skapa en resursgrupp. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn.
1. Skapa en virtuell dator.

I följande exempel skapas en virtuell dator från en [Azure-snabbstartsmall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). SSH-autentisering tillåts för den här distributionen. När du uppmanas, anger du värdet för en egen offentlig SSH-nyckel, t.ex innehållet i *~/.ssh/id_rsa.pub*. Om du vill skapa ett SSH-nyckelpar kan du läsa [hur du skapar och använder en SSH-nyckelpar för virtuella Linux-datorer i Azure](mac-create-ssh-keys.md). Här är en kopia av mallen:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Om du vill köra CLI-skript, Välj **prova** att öppna Azure Cloud shell. Högerklicka i gränssnittet för att klistra in skriptet, och välj sedan **klistra in**:

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
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey='$key' &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Det senaste Azure CLI-kommandot visar offentlig IP-adressen för den nya virtuella datorn. Du behöver den offentliga IP-adressen för att ansluta till den virtuella datorn. Se nästa avsnitt i den här artikeln.

Du har angett en mall som lagras i GitHub i exemplet ovan. Du kan också hämta eller skapa en mall och ange den lokala sökvägen med den `--template-file` parametern.

Här följer några ytterligare resurser:

- Läs hur du utvecklar Resource Manager-mallar i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/).
- Scheman för Azure-dator finns i [Azure mallreferensen](/azure/templates/microsoft.compute/allversions).
- Flera VM-mallexempel finns i [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

Du kan sedan SSH till den virtuella datorn som vanligt. Ger dig egna offentliga IP-adress från det föregående kommandot:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Nästa steg

I det här exemplet skapade du en grundläggande Linux-VM. Fler Resource Manager-mallar som inkluderar ramverk för programmet eller skapa mer komplexa miljöer, bläddra i [Azure-snabbstartmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Mer information om hur du skapar mallar, visa JSON-syntax och egenskaper för resurstyper som du har distribuerat:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
