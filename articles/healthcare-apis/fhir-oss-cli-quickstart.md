---
title: 'Azure CLI: Distribuera FHIR-server med öppen källkod för Azure – Azure API för Azure'
description: Den här snabb starten förklarar hur du distribuerar Microsoft FHIR-servern med öppen källkod för Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.custom: devx-track-azurecli
ms.openlocfilehash: 10af71afd8843e75d5df3be57c909c56a7abca01
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87843597"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Snabb start: Distribuera FHIR-server med öppen källkod med Azure CLI

I den här snabb starten får du lära dig hur du distribuerar en FHIR- &reg; Server med öppen källkod i Azure med hjälp av Azure CLI.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Skapa resursgrupp

Välj ett namn för resurs gruppen som ska innehålla de etablerade resurserna och skapa den:

```azurecli-interactive
servicename="myfhirservice"
az group create --name $servicename --location westus2
```

## <a name="deploy-template"></a>Distribuera mallen

Microsoft FHIR-servern för Azure [GitHub-lagringsplatsen](https://github.com/Microsoft/fhir-server) innehåller en mall som distribuerar alla nödvändiga resurser. Distribuera den med:

```azurecli-interactive
az group deployment create -g $servicename --template-uri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json --parameters serviceName=$servicename
```

## <a name="verify-fhir-server-is-running"></a>Verifiera att FHIR-servern körs

Hämta en funktions sats från FHIR-servern med:

```azurecli-interactive
metadataurl="https://${servicename}.azurewebsites.net/metadata"
curl --url $metadataurl
```

Det tar en minut eller så att servern svarar första gången.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort resurs gruppen med följande steg:

```azurecli-interactive
az group delete --name $servicename
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat Microsoft FHIR-server med öppen källkod för Azure i din prenumeration. Om du vill veta mer om hur du kommer åt FHIR-API: et med Postman fortsätter du till självstudien för Postman.
 
>[!div class="nextstepaction"]
>[Åtkomst till FHIR-API med Postman](access-fhir-postman-tutorial.md)
