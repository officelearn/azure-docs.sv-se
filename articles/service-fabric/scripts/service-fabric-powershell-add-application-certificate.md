---
title: Skriptexempel för Azure PowerShell – Lägga till programcertifikat till ett kluster | Microsoft Docs
description: Skriptexempel för Azure PowerShell – Lägga till ett programcertifikat till ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 3d2ab7339a641164a628854c00e22f437b21c3df
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670463"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Distribuera ett programcertifikat till ett Service Fabric-kluster

Det här skriptexemplet skapar ett självsignerat certifikat i det angivna nyckelvalvet för Azure och installerar det på alla noder i Service Fabric-klustret. Certifikatet kan även laddas ned till en lokal mapp. Namnet på det nedladdade certifikatet är samma som namnet på certifikatet i nyckelvalvet. Anpassa parametrarna efter behov.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzAccount` för att skapa en anslutning med Azure. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | Lägg till ett nytt programcertifikat för den virtuella datorns skalningsuppsättning som ingår i klustret.  |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
