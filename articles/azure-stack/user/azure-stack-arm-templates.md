---
title: Använd Azure Resource Manager-mallar i Azure-stacken | Microsoft Docs
description: Lär dig hur du använder Azure Resource Manager-mallar i Azure-stacken att etablera resurser.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9c4d538f77ae056163fd17aa547162a4ad3eff63
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Använd Azure Resource Manager-mallar i Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Du kan använda Azure Resource Manager-mallar för att distribuera och Tillhandahåll alla resurser i programmet i en enda, samordnad åtgärd. Du kan också distribuera mallar för att göra ändringar i resurser i en resursgrupp.

De här mallarna kan distribueras med Microsoft Azure Stack-portalen, PowerShell, kommandorad och Visual Studio.

Följande snabbstartsmallar är tillgängliga på [GitHub](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Distribuera SharePoint Server (distribution med hög tillgänglighet)

Använd PowerShell DSC-tillägg för att skapa en SharePoint Server 2013-grupp som innehåller följande resurser:

* Ett virtuellt nätverk
* Tre storage-konton
* Två externa belastningsutjämnare
* En virtuell dator (VM) som konfigurerats som en domänkontrollant för en ny skog med en enda domän
* En virtuell dator som konfigurerats som en fristående server för SQL Server 2014
* En virtuell dator konfigurerad som en SharePoint Server 2013-grupp för en dator

## <a name="deploy-ad-non-high-availability-deployment"></a>Distribuera AD (icke-hög-tillgänglighet-distribution)

Använd PowerShell DSC-tillägg för att skapa en hanterarserver för AD-domän som innehåller följande resurser:

* Ett virtuellt nätverk
* Ett lagringskonto
* En extern belastningsutjämnare
* En virtuell dator (VM) som konfigurerats som en domänkontrollant för en ny skog med en enda domän

## <a name="deploy-adsql-non-high-availability-deployment"></a>Distribuera AD/SQL (icke-hög-tillgänglighet-distribution)

Använd PowerShell DSC-tillägg för att skapa en fristående server för SQL Server 2014 som innehåller följande resurser:

* Ett virtuellt nätverk
* Två storage-konton
* En extern belastningsutjämnare
* En virtuell dator (VM) som konfigurerats som en domänkontrollant för en ny skog med en enda domän
* En virtuell dator som konfigurerats som en fristående server för SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Använd PowerShell DSC-tillägg för att konfigurera en befintlig virtuell dator lokala Configuration Manager (MGM) och registrera en Azure Automation-konto DSC Pull-Server.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Skapa en virtuell dator från en användaravbildning av

Skapa en virtuell dator från en egen avbildning. Den här mallen distribuerar också ett virtuellt nätverk (med DNS), offentlig IP-adress och ett nätverksgränssnitt.

## <a name="basic-virtual-machine"></a>Grundläggande virtuell dator

Distribuera en virtuell Windows-dator som innehåller ett virtuellt nätverk (med DNS), offentlig IP-adress och ett nätverksgränssnitt.

## <a name="cancel-a-running-template-deployment"></a>Avbryta en pågående malldistribution

Om du vill avbryta en pågående malldistribution använder den `Stop-AzureRmResourceGroupDeployment` PowerShell-cmdlet.

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med portalen](azure-stack-deploy-template-portal.md)
* [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
