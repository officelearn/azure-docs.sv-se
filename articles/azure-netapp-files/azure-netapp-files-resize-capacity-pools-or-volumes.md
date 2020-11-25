---
title: Ändra storlek på kapacitets gruppen eller en volym för Azure NetApp Files | Microsoft Docs
description: Lär dig hur du ändrar storleken på en kapacitets uppsättning eller en volym. Om du ändrar storlek på kapacitetsutnyttjandet ändras den köpta Azure NetApp Files kapaciteten.
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 62bf154c1dbf1a0d3f12e2cef916b37059ce985b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012484"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ändra storlek på en kapacitetspool eller en volym
Du kan ändra storleken på en kapacitets uppsättning eller en volym om det behövs. 

## <a name="resize-the-capacity-pool"></a>Ändra storlek på kapacitets gruppen 

Du kan ändra storleken på TiB ökningar eller minskningar i en pool. Kapacitets bassängens storlek kan dock inte vara mindre än 4 TiB. Om du ändrar storlek på kapacitetsutnyttjandet ändras den köpta Azure NetApp Files kapaciteten.

1. Från bladet hantera NetApp-konton klickar du på den kapacitets uppsättning som du vill ändra storlek på. 
2. Högerklicka på namnet på mediepoolen eller klicka på "..." i slutet av poolens rad för att Visa snabb menyn. 
3. Använd snabb meny alternativen för att ändra storlek på eller ta bort kapacitets gruppen.

## <a name="resize-a-volume"></a>Ändra storlek på en volym

Du kan ändra storlek på en volym om det behövs. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.

1. Från bladet hantera NetApp-konton klickar du på **volymer**. 
2. Högerklicka på namnet på den volym som du vill ändra storlek på eller klicka på "..." ikonen i slutet av volymens rad för att Visa snabb menyn.
3. Använd snabb meny alternativen för att ändra storlek på eller ta bort volymen.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera en kapacitetspool](azure-netapp-files-set-up-capacity-pool.md)
- [Hantera en manuell QoS-kapacitetspool](manage-manual-qos-capacity-pool.md)
- [Ändra tjänstnivå för en volym dynamiskt](dynamic-change-volume-service-level.md) 