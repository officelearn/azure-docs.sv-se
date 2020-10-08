---
title: 'PowerShell: Distribuera FHIR-Server för Azure – Azure API för FHIR'
description: Den här snabb starten förklarar hur du distribuerar Microsoft Open Source FHIR-servern med PowerShell.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: c6c1a7e21f0a1554c67c7f1860a2bd3382c941f5
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91817963"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-powershell"></a>Snabb start: Distribuera FHIR-server med öppen källkod med PowerShell

I den här snabb starten lär du dig hur du distribuerar Microsoft FHIR-servern med öppen källkod för Azure med hjälp av PowerShell.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Välj ett namn för resurs gruppen som ska innehålla de etablerade resurserna och skapa den:

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = New-AzResourceGroup -Name $fhirServiceName -Location westus2
```

## <a name="deploy-the-fhir-server-template"></a>Distribuera FHIR Server-mall

Microsoft FHIR-servern för Azure [GitHub-lagringsplatsen](https://github.com/Microsoft/fhir-server) innehåller en mall som distribuerar alla nödvändiga resurser. Distributions processen tar flera minuter eftersom nödvändiga Azure-resurser skapas och konfigureras. Distribuera den med:

```azurepowershell-interactive
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg.ResourceGroupName -serviceName $fhirServiceName
```

> [!NOTE]
> Om du inte redan är inloggad kör du följande kommandon först.

```azurepowershell-interactive
Connect-AzAccount
get-azsubscription
Set-AzContext -SubscriptionId yoursubscriptionid
```

Om du vill använda en befintlig resurs grupp ändrar du $rg värden på $rg variabelns definitions rad och kommando raden för distribution av Azure ARM-mallar, som visas i koden.

```azurepowershell-interactive
$fhirServiceName = "MyFhirService"
$rg = "MyExistingResourceGroupName"
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Microsoft/fhir-server/master/samples/templates/default-azuredeploy.json -ResourceGroupName $rg -serviceName $fhirServiceName
```

## <a name="verify-fhir-server-is-running"></a>Verifiera att FHIR-servern körs

```azurepowershell-interactive
$metadataUrl = "https://" + $fhirServiceName + ".azurewebsites.net/metadata" 
$metadata = Invoke-WebRequest -Uri $metadataUrl
$metadata.RawContent
```

Det tar en minut eller så att servern svarar första gången.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort resurs gruppen med följande steg:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rg.ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du distribuerat Microsoft FHIR-server med öppen källkod för Azure i din prenumeration. Om du vill veta mer om hur du kommer åt FHIR-API: et med Postman fortsätter du till självstudien för Postman.
 
>[!div class="nextstepaction"]
>[Åtkomst till FHIR-API med Postman](access-fhir-postman-tutorial.md)
