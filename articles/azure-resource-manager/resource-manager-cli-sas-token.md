---
title: Distribuera Azure-mall med SAS-token och Azure CLI | Microsoft Docs
description: Använda Azure Resource Manager och Azure CLI för att distribuera resurser till Azure från en mall som är skyddat med SAS-token.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: c869b76a0d1ba10bc27aefa60cbe4ed5b8d8201a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61061370"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Distribuera en privat Resource Manager-mall med SAS-token och Azure CLI

När mallen finns i ett lagringskonto, kan du begränsa åtkomsten till mallen och ange en token för delad åtkomst (signatur) under distributionen. Det här avsnittet beskriver hur du använder Azure PowerShell med Resource Manager-mallar för att ange en SAS-token under distributionen. 

## <a name="add-private-template-to-storage-account"></a>Lägg till privat mall till storage-konto

Du kan lägga till dina mallar till ett lagringskonto och en länk till dem under distributionen med en SAS-token.

> [!IMPORTANT]
> Blobben som innehåller mallen är tillgänglig för endast ägare genom att följa stegen nedan. Men när du skapar en SAS-token för bloben är blob tillgängliga för alla med denna URI. Om en annan användare spärras URI: N, kan som användaren komma åt mallen. Med hjälp av en SAS-token är ett bra sätt för att begränsa åtkomsten till dina mallar, men du får inte innehålla känsliga data som lösenord direkt i mallen.
> 
> 

I följande exempel ställer in en privat lagringskontobehållare och överför en mall:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Ange SAS-token under distributionen
Generera en SAS-token för att distribuera en privat mall i ett lagringskonto, och inkludera den i URI: N för mallen. Ange förfallotiden till att det finns tillräckligt med tid att slutföra distributionen.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Ett exempel på hur du använder en SAS-token med länkade mallar finns i [med länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Nästa steg
* En introduktion till att distribuera mallar finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](resource-group-template-deploy-cli.md).
* En fullständig exempelskript som distribuerar en mall finns i [skript för distribuera Resource Manager-mallar](resource-manager-samples-cli-deploy.md)
* För att definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
