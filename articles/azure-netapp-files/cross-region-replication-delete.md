---
title: Ta bort replikeringar för Azure NetApp Files replikering över flera regioner | Microsoft Docs
description: Beskriver hur du tar bort en replikeringsanslutning som inte längre behövs mellan käll-och mål volymerna.
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
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695715"
---
# <a name="delete-replications"></a>Ta bort replikeringar

Du kan avsluta replikeringen mellan käll-och mål volymerna genom att ta bort volym replikering. Du kan utföra borttagnings åtgärden antingen från käll-eller mål volymen. Borttagnings åtgärden tar bara bort auktorisering för replikering. käll-eller mål volymen tas inte bort. 

## <a name="steps"></a>Steg

1. Kontrol lera att peering-replikeringen har brutits innan du tar bort en volym replikering.    
    Se [Visa hälso status för replikeringsrelation](cross-region-replication-display-health-status.md) och [bryta peering-replikering](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume).  

1. Om du vill ta bort en volym replikering väljer du **replikering** från käll-eller mål volymen.  

2. Klicka på **Ta bort**.    

3. Bekräfta borttagningen genom att skriva **Ja** och klicka på **ta bort**.   

    ![Ta bort replikering](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Nästa steg  

* [Replikering mellan regioner](cross-region-replication-introduction.md)
* [Krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md)
* [Visa hälsostatus för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)

