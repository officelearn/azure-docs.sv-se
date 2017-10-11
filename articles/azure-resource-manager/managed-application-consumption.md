---
title: "Använda en Azure hanterat program | Microsoft Docs"
description: "Beskriver hur en kund skapar en Azure hanterade program från publicerade filer."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/23/2017
ms.author: gauravbh; tomfitz
ms.openlocfilehash: ed8fbaf2a4546c8e31eeced11cd0b5627fd62c0c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="consume-an-internal-managed-application"></a>Använda en intern hanterad App

Du kan använda Azure [hanterade program](managed-application-overview.md) som är avsedda för medlemmar i din organisation. Du kan till exempel välja hanterade program från IT-avdelningen som säkerställa efterlevnad med organisationens normer. Dessa hanterade program är tillgängliga via Tjänstkatalogen inte Azure Marketplace.

Innan du fortsätter med den här artikeln, måste du ha ett hanterat program som är tillgängliga i tjänstkatalogen för din prenumeration. Om någon i din organisation redan inte har skapat ett hanterat program, se [publicera ett hanterat program för internt bruk](managed-application-publishing.md).

För närvarande kan kan du använda Azure CLI eller Azure-portalen för att använda ett hanterat program.

## <a name="create-the-managed-application-by-using-the-portal"></a>Skapa det hanterade programmet med hjälp av portalen

Följ anvisningarna nedan om du vill distribuera ett hanterat program via portalen

1. Gå till Azure-portalen. Sök efter **Tjänstkatalogen hanterat program**.

   ![Tjänstkatalogen hanterade program](./media/managed-application-consumption/create-service-catalog-managed-application.png)

1. Välj det hanterade programmet som du vill skapa i listan över tillgängliga lösningar. Välj **Skapa**.

   ![Val av hanterade program](./media/managed-application-consumption/select-offer.png)

1. Ange värden för parametrarna som krävs för att etablera resurserna. Välj **Väst centrala oss** för platsen. Välj **OK**.

   ![Parametrar för hanterade program](./media/managed-application-consumption/input-parameters.png)

1. Mallen verifierar de värden du angav. Om verifieringen lyckas, väljer **OK** att starta distributionen.

   ![Validering av hanterade program](./media/managed-application-consumption/validation.png)

När distributionen är klar etableras lämpliga resurser som definierats i mallen i den hantera resursgruppen som du angav.

## <a name="create-the-managed-application-by-using-azure-cli"></a>Skapa det hanterade programmet med hjälp av Azure CLI

Det finns två sätt att skapa ett hanterat program med hjälp av Azure CLI:

* Använd kommandot för att skapa hanterade program.
* Använd kommandot reguljära mallen distribution.

### <a name="use-the-template-deployment-command"></a>Använd mallkommandot för distribution

Distribuera filen applianceMainTemplate.json som skapats av leverantören.

Skapa sedan två resursgrupper. Den första resursgruppen finns där programresursen hanterade har skapats: Microsoft.Solutions/appliances. Andra resursgruppen innehåller alla resurser som definierats i mainTemplate.json. Den här resursgruppen hanteras av ISV: er.

```azurecli
az group create --name mainResourceGroup --location westcentralus
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Använd `westcentralus` som plats för resursgruppen.
>

Om du vill distribuera applianceMainTemplate.json i mainResourceGroup, använder du följande kommando:

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri
```

När mallen föregående körs, ombeds du ange värden för parametrar som definieras i mallen. Utöver de parametrar som behövs för att etablera resurser i en mall, behöver du två viktiga parametervärden:

- **managedResourceGroupId**: ID för resursgruppen som innehåller de resurser som definierats i applianceMainTemplate.json. ID som är i formatet `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. I föregående exempel är det ID för `managedResourceGroup`.
- **applianceDefinitionId**: ID för den hanterade programtjänsten definition resursen. Det här värdet anges av ISV: er.

> [!NOTE]
> Utgivaren måste ge åtkomst till resursgruppen som innehåller programdefinitionen av hanterade. Definition av resursen har skapats i publisher prenumerationen. Därför måste en användare, grupp eller ett program i innehavaren för customer läsbehörighet till den här resursen.

När distributionen har slutförts, visas det hanterade programmet skapas i mainResourceGroup. StorageAccount resursen har skapats i managedResourceGroup.

### <a name="use-the-create-command"></a>Använd kommandot create

Du kan använda den `az managedapp create` kommando för att skapa ett hanterat program från definition för hanterade program.

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus"
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401"
    --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401"
    --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401"
    --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}"
    --debug
```

* **installation-definition-Id**: resurs-ID för hanterade programdefinitionen skapades i föregående steg. Kör följande kommando för att få detta ID:

  ```azurecli
  az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
  ```

  Det här kommandot returnerar definition för hanterade program. Du måste värdet för egenskapen ID.

* **hanterade-rg-id**: namnet på resursgruppen som innehåller de resurser som definierats i applianceMainTemplate.json. Den här resursgruppen tillhör hanterade resursgruppen. Den hanteras av utgivaren. Om det inte finns, skapas den åt dig.
* **resursgruppens namn**: resursgruppen där programresursen hanterade har skapats. Resursen Microsoft.Solutions/appliance bor i den här resursgruppen.
* **parametrarna**: de parametrar som krävs för de resurser som definierats i applianceMainTemplate.json.

## <a name="known-issues"></a>Kända problem

Den här förhandsversionen omfattar följande:

* En 500 Internt serverfel visas under genereringen av det hanterade programmet. Om du stöter på problemet, är det troligt att tillfälligt. Försök igen.
* En ny resursgrupp krävs för den hantera resursgruppen. Om du använder en befintlig resursgrupp, misslyckas distributionen.
* Resursgruppen som innehåller Microsoft.Solutions/appliances resursen måste skapas i den **westcentralus** plats.

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program, se [hanteras Programöversikt](managed-application-overview.md).
* Information om hur du publicerar ett program för Tjänstkatalog hanteras finns [skapa och publicera en applikation för Tjänstkatalog hanteras](managed-application-publishing.md).
* Information om hur du publicerar hanterade program på Azure Marketplace finns [Azure hanterade program i Marketplace](managed-application-author-marketplace.md).
* Information om hur du använder ett hanterat program från Marketplace finns [använda Azure hanterade program i Marketplace](managed-application-consume-marketplace.md).
