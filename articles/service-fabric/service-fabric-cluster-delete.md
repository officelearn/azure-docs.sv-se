---
title: Ta bort ett Azure-kluster och dess resurser | Microsoft Docs
description: Lär dig hur du vill ta bort ett Service Fabric-kluster du tar bort resursgruppen som innehåller klustret eller selektivt ta bort resurser.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/24/2017
ms.author: aljo
ms.openlocfilehash: 55840dc4b9f25c7e2676cff936390a0542eb239d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211277"
---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Ta bort ett Service Fabric-kluster på Azure och resurser som används
Service Fabric-klustret består av många andra Azure-resurser förutom klusterresursen sig själv. Så om du vill ta bort ett Service Fabric-kluster helt måste du också ta bort alla resurser det består av.
Har du två alternativ: antingen ta bort den resursgrupp som klustret tillhör (som tar bort klusterresursen och andra resurser i resursgruppen) eller ta bort klusterresursen specifikt och dess tillhörande resurser (men inte andra resurser i resursgruppen).

> [!NOTE]
> Ta bort klusterresursen **inte** ta bort alla andra resurser som består av Service Fabric-klustret.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Ta bort hela resursgruppen (RG) som Service Fabric-kluster finns i
Detta är det enklaste sättet att se till att du tar bort alla resurser som är associerade med klustret, inklusive resursgruppen. Du kan ta bort resursgruppen med PowerShell eller via Azure-portalen. Om resursgruppen har resurser som inte är relaterade till Service fabric-kluster, kan du ta bort specifika resurser.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Ta bort resursgruppen med hjälp av Azure PowerShell
Du kan också ta bort resursgruppen genom att köra följande Azure PowerShell-cmdlets. Kontrollera att Azure PowerShell 1.0 eller högre är installerad på datorn. Om du inte har gjort det, Följ stegen som beskrivs i [installera och konfigurera Azure PowerShell.](/powershell/azure/overview)

Öppna ett PowerShell-fönster och kör följande PS-cmdlets:

```powershell
Connect-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Du får en uppmaning om att bekräfta om du inte använde den *-Force* alternativet. RG och alla resurser som den innehåller tas bort på bekräftelse.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Ta bort en resursgrupp i Azure-portalen
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till det Service Fabric-kluster du vill ta bort.
3. Klicka på resursgrupp namn på klustret essentials sidan.
4. Detta öppnar den **resurs grupp Essentials** sidan.
5. Klicka på **Ta bort**.
6. Följ instruktionerna på sidan för att slutföra borttagningen av resursgruppen.

![Ta bort resursgruppen.][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Ta bort klusterresursen och resurser som används, men inte andra resurser i resursgruppen.
Om resursgruppen har resurser som är relaterade till Service Fabric-kluster som du vill ta bort, är det enklare att ta bort hela resursgruppen. Följ dessa steg om du vill välja bort resurser i taget i resursgruppen.

Om du har distribuerat ditt kluster med hjälp av portalen eller med någon av Service Fabric Resource Manager-mallar i mallgalleriet är alla resurser som klustret använder märkta med följande två taggar. Du kan använda dem för att bestämma vilka resurser som du vill ta bort.

***Taggen #1:*** nyckel = clusterName, värde = 'namn på klustret'

***Taggen #2:*** nyckel = resourceName, värde = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Ta bort specifika resurser i Azure-portalen
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till det Service Fabric-kluster du vill ta bort.
3. Gå till **alla inställningar** på bladet essentials.
4. Klicka på **taggar** under **resurshantering** i inställningsbladet.
5. Klicka på någon av de **taggar** i bladet taggar om du vill visa en lista över alla resurser med taggen.
   
    ![Resurstaggar][ResourceTags]
6. När du har märkt resurslistan klickar du på var och en av resurserna och ta bort dem.
   
    ![Taggade resurser][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Ta bort resurser med Azure PowerShell
Du kan ta bort resurser i taget genom att köra följande Azure PowerShell-cmdlets. Kontrollera att Azure PowerShell 1.0 eller högre är installerad på datorn. Om du inte har gjort det, Följ stegen som beskrivs i [installera och konfigurera Azure PowerShell.](/powershell/azure/overview)

Öppna ett PowerShell-fönster och kör följande PS-cmdlets:

```powershell
Connect-AzureRmAccount
```
Kör följande skript för var och en av de resurser som du vill ta bort:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Kör följande skript för att ta bort klusterresursen:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Nästa steg
Läs följande om du vill också lära dig om att uppgradera ett kluster och partitionering tjänster:

* [Lär dig mer om klusteruppgradering](service-fabric-cluster-upgrade.md)
* [Lär dig mer om partitionering tillståndskänsliga tjänster för maximal skala](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
