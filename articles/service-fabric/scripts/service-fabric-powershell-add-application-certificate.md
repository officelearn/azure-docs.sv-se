---
title: Skriptexempel för Azure PowerShell – Lägga till programcertifikat till ett kluster | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Lägga till ett programcertifikat till ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: be097f88f774df9e4a6429af444c6c742737f4c9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Distribuera ett programcertifikat till ett Service Fabric-kluster

Det här skriptexemplet skapar ett självsignerat certifikat i det angivna nyckelvalvet för Azure och installerar det på alla noder i Service Fabric-klustret. Certifikatet kan även laddas ned till en lokal mapp. Namnet på det nedladdade certifikatet är samma som namnet på certifikatet i nyckelvalvet. Anpassa parametrarna efter behov.

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning med Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | Lägg till ett nytt programcertifikat för den virtuella datorns skalningsuppsättning som ingår i klustret.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
