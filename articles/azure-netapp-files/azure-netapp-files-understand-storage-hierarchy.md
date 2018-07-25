---
title: Förstå lagringshierarkin för Azure NetApp Files | Microsoft Docs
description: Beskriver lagringshierarkin, inklusive Azure NetApp Files-konton, kapacitetspooler och volymer.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 6f5ed4e7ede9a098d69b7a40f44dd60f9b400472
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010998"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Förstå lagringshierarkin för Azure NetApp Files

Innan du skapar en volym i Azure NetApp Files måste du köpa och konfigurera en pool för etablerad kapacitet.  Du måste ha ett NetApp-konto för att kunna konfigurera en kapacitetspool. När du förstår lagringshierarkin är det lättare att konfigurera och hantera Azure NetApp Files-resurser.

## <a name="azure_netapp_files_account"></a>NetApp-konton

- Ett NetApp-konto fungerar som en administrativ gruppering av de ingående kapacitetspoolerna.  
- NetApp-kontot är inte detsamma som ditt allmänna Azure-lagringskonto. 
- Ett NetApp-konto är regionalt i omfång.   
- Du kan ha flera NetApp-konton i en region, men varje NetApp-konto är endast kopplat till en enda region.

## <a name="capacity_pools"></a>Kapacitetspooler

- En kapacitetspool mäts genom dess etablerade kapacitet.  
- Kapaciteten etableras via de fasta SKU:er som du köpte (exempelvis en kapacitet på 4 TB).
- Kapacitetspoolen kan endast ha en servicenivå.  
  För närvarande är endast servicenivån Premium tillgänglig.
- Varje kapacitetspool tillhör endast ett NetApp-konto.  
- Det går inte att flytta kapacitetspooler mellan NetApp-konton.   
  I [Konceptdiagram över lagringshierarki](#conceptual_diagram_of_storage_hierarchy) nedan kan exempelvis Capacity Pool 1 inte flyttas från NetApp-kontot USA, östra till NetApp-kontot USA, västra 2.  

## <a name="volumes"></a>Volymer

- En volym mäts efter logisk kapacitetsförbrukning och är skalbar upp till 100 TB per volym.
- En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.
- Varje volym hör till endast en pool, men en pool kan innehålla flera volymer. 
- Du kan flytta en volym mellan pooler inom samma NetApp-konto.    
  I [Konceptdiagram över lagringshierarki](#conceptual_diagram_of_storage_hierarchy) nedan kan du till exempel flytta volymerna från Kapacitetspool 1 till Kapacitetspool 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptdiagram över lagringshierarki 
I följande exempel visar vi sambanden mellan Azure-prenumeration, NetApp-konton, kapacitetspooler och volymer.   

![Konceptdiagram över lagringshierarki](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Nästa steg

1. [Skapa ett NetApp-konto](azure-netapp-files-create-netapp-account.md)
2. [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
3. [Skapa en volym för Azure NetApp Files](azure-netapp-files-create-volumes.md)
4. [Konfigurera exportprincipen för en volym (valfritt)](azure-netapp-files-configure-export-policy.md)