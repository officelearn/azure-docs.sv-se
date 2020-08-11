---
title: Vilken lagrings-hierarki är Azure NetApp Files | Microsoft Docs
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
ms.topic: overview
ms.date: 08/10/2020
ms.author: b-juche
ms.openlocfilehash: 91fecbc68efec1adcee9a2c4013dea46f6da86af
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066482"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Vad är lagrings-hierarkin för Azure NetApp Files

Innan du skapar en volym i Azure NetApp Files måste du köpa och konfigurera en pool för etablerad kapacitet.  Du måste ha ett NetApp-konto för att kunna konfigurera en kapacitetspool. När du förstår lagringshierarkin är det lättare att konfigurera och hantera Azure NetApp Files-resurser.

> [!IMPORTANT] 
> Azure NetApp Files stöder för närvarande inte migrering mellan prenumerationer.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>NetApp-konton

- Ett NetApp-konto fungerar som en administrativ gruppering av de ingående kapacitetspoolerna.  
- NetApp-kontot är inte detsamma som ditt allmänna Azure-lagringskonto. 
- Ett NetApp-konto är regionalt i omfång.   
- Du kan ha flera NetApp-konton i en region, men varje NetApp-konto är endast kopplat till en enda region.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Kapacitetspooler

- En kapacitetspool mäts genom dess etablerade kapacitet.  
- Kapaciteten etableras via de fasta SKU:er som du köpte (exempelvis en kapacitet på 4 TiB).
- Kapacitetspoolen kan endast ha en servicenivå.  
- Varje kapacitetspool kan endast tillhör ett NetApp-konto. Du kan dock ha flera kapacitetspooler inom ett NetApp-konto.  
- Det går inte att flytta kapacitetspooler mellan NetApp-konton.   
  I [Konceptdiagram över lagringshierarki](#conceptual_diagram_of_storage_hierarchy) nedan kan exempelvis Capacity Pool 1 inte flyttas från NetApp-kontot USA, östra till NetApp-kontot USA, västra 2.  
- Det går inte att ta bort en kapacitets pool förrän alla volymer i kapacitets gruppen har tagits bort.

## <a name="volumes"></a><a name="volumes"></a>Enheter

- En volym mäts efter logisk kapacitetsförbrukning och är skalbar. 
- En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.
- Varje volym hör till endast en pool, men en pool kan innehålla flera volymer. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptdiagram över lagringshierarki 
I följande exempel visar vi sambanden mellan Azure-prenumeration, NetApp-konton, kapacitetspooler och volymer.   

![Konceptdiagram över lagringshierarki](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Nästa steg

- [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registrera dig för Azure NetApp Files](azure-netapp-files-register.md)
