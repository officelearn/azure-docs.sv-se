---
title: Flytta ett Service Fabric Mesh-program till en annan region
description: Du kan flytta Service Fabric Mesh-resurser genom att distribuera en kopia av din aktuella mall till en ny Azure-region.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908168"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Flytta ett Service Fabric Mesh-program till en annan Azure-region

I den här artikeln beskrivs hur du flyttar ditt Service Fabric Mesh-program och dess resurser till en annan Azure-region. Du kan flytta dina resurser till en annan region av flera skäl. Till exempel, som svar på avbrott, för att få funktioner eller tjänster som är tillgängliga i specifika regioner, för att uppfylla interna princip- och styrningskrav eller som svar på kapacitetsplaneringskrav.

 [Service Fabric Mesh stöder inte](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) möjligheten att direkt flytta resurser över Azure-regioner. Du kan dock flytta resurser indirekt genom att distribuera en kopia av din aktuella Azure Resource Manager-mall till den nya målregionen och sedan omdirigera inkommande trafik och beroenden till det nyligen skapade Service Fabric Mesh-programmet.

## <a name="prerequisites"></a>Krav

* Ingress controller (till exempel [Application Gateway)](https://docs.microsoft.com/azure/application-gateway/)för att fungera som en mellanhand för routning trafik mellan klienter och ditt Service Fabric Mesh-program
* Tillgänglighet för Service Fabric Mesh (förhandsversion)`westus` `eastus`i `westeurope`målområdet Azure ( , , eller )

## <a name="prepare"></a>Förbereda

1. Ta en "ögonblicksbild" av det aktuella tillståndet för ditt Service Fabric Mesh-program genom att exportera Azure Resource Manager-mallen och parametrarna från den senaste distributionen. Det gör du genom att följa stegen i [mallen Exportera efter distribution](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) med Azure-portalen. Du kan också använda [Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)eller [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate).

2. Om tillämpligt [exporterar du andra resurser i samma resursgrupp](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) för omfördelning i målregionen.

3. Granska (och redigera om det behövs) den exporterade mallen för att säkerställa att de befintliga egenskapsvärdena är de som du vill använda i målområdet. Den `location` nya (Azure-regionen) är en parameter som du ska ange under omdistribution.

## <a name="move"></a>Flytta

1. Skapa en ny resursgrupp (eller använd en befintlig) i målområdet.

2. Med den exporterade mallen följer du stegen i [Distribuera resurser från anpassad mall](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) med hjälp av Azure-portalen. Du kan också använda [Azure CLI,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)eller [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest).

3. Mer information om hur du flyttar relaterade resurser, till exempel [Azure Storage-konton,](../storage/common/storage-account-move.md)finns i vägledning för enskilda tjänster som anges under avsnittet [Flytta Azure-resurser över regioner](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verifiera

1. När distributionen är klar testar du programslutpunkterna för att verifiera programmets funktioner.

2. Du kan också verifiera status för ditt program genom att kontrollera programstatus[(az mesh app show)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)och granska programloggar och[(az mesh code-package-log)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)kommandon med hjälp av [Azure Service Fabric Mesh CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Checka in

När du har bekräftat motsvarande funktioner i ditt Service Fabric Mesh-program i målregionen konfigurerar du ingress-styrenheten (till exempel [Application Gateway)](../application-gateway/redirect-overview.md)för att omdirigera trafik till det nya programmet.

## <a name="clean-up-source-resources"></a>Rensa källresurser

Om du vill slutföra flytten av Programmet Service Fabric Mesh [tar du bort källprogrammet och/eller den överordnade resursgruppen](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Nästa steg

* [Flytta Azure-resurser mellan regioner](../azure-resource-manager/management/move-region.md)
* [Stöd för att flytta Azure-resurser mellan regioner](../azure-resource-manager/management/region-move-support.md)
* [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Resurser som kan flyttas](../azure-resource-manager/management/move-support-resources.md
)
