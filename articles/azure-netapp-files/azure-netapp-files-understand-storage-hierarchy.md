---
title: Vad är Azure NetApp Files storage hierarki | Microsoft Docs
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
ms.date: 04/16/2019
ms.author: b-juche
ms.openlocfilehash: c2984e012ae83a8bc17d72ed4eac0c5c469c2694
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522871"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Vad är Azure NetApp Files storage hierarki

Innan du skapar en volym i Azure NetApp Files måste du köpa och konfigurera en pool för etablerad kapacitet.  Du måste ha ett NetApp-konto för att kunna konfigurera en kapacitetspool. När du förstår lagringshierarkin är det lättare att konfigurera och hantera Azure NetApp Files-resurser.

## <a name="azure_netapp_files_account"></a>NetApp-konton

- Ett NetApp-konto fungerar som en administrativ gruppering av de ingående kapacitetspoolerna.  
- NetApp-kontot är inte detsamma som ditt allmänna Azure-lagringskonto. 
- Ett NetApp-konto är regionalt i omfång.   
- Du kan ha flera NetApp-konton i en region, men varje NetApp-konto är endast kopplat till en enda region.

## <a name="capacity_pools"></a>Kapacitetspooler

- En kapacitetspool mäts genom dess etablerade kapacitet.  
- Kapaciteten etableras via de fasta SKU:er som du köpte (exempelvis en kapacitet på 4 TiB).
- Kapacitetspoolen kan endast ha en servicenivå.  
- Varje kapacitetspool kan endast tillhör ett NetApp-konto. Du kan dock ha flera kapacitetspooler inom ett NetApp-konto.  
- Det går inte att flytta kapacitetspooler mellan NetApp-konton.   
  I [Konceptdiagram över lagringshierarki](#conceptual_diagram_of_storage_hierarchy) nedan kan exempelvis Capacity Pool 1 inte flyttas från NetApp-kontot USA, östra till NetApp-kontot USA, västra 2.  
- Poolen kapacitet kan inte tas bort förrän alla volymer i kapacitet poolen har tagits bort.

## <a name="volumes"></a>Volymer

- En volym mäts efter logisk kapacitetsförbrukning och är skalbar. 
- En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.
- Varje volym hör till endast en pool, men en pool kan innehålla flera volymer. 
- En volym kan inte flyttas över kapacitet pooler. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Till exempel i den [begreppsmässiga diagram över storage hierarkin](#conceptual_diagram_of_storage_hierarchy) nedan, du kan inte flytta volymerna från kapacitet Pool 1 till Pool 2 för kapacitet.
- En volym kan inte tas bort förrän alla ögonblicksbilder har tagits bort.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Konceptdiagram över lagringshierarki 
I följande exempel visar vi sambanden mellan Azure-prenumeration, NetApp-konton, kapacitetspooler och volymer.   

![Konceptdiagram över lagringshierarki](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Nästa steg

- [Resursbegränsningar för Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registrera dig för Azure NetApp Files](azure-netapp-files-register.md)
