---
title: 'Azure CLI: Distribuera FHIR-server med öppen källkod för Azure – Azure API för Azure'
description: Den här snabb starten förklarar hur du distribuerar Microsoft FHIR-servern med öppen källkod för Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: f8047ebeb8e47f609db79e3ac1235b5cd65a4fd4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "84820230"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-cli"></a>Snabb start: Distribuera FHIR-server med öppen källkod med Azure CLI

I den här snabb starten får du lära dig hur du distribuerar en FHIR- &reg; Server med öppen källkod i Azure med hjälp av Azure CLI.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

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

```console
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