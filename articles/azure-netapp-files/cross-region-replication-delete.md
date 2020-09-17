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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709055"
---
# <a name="delete-replications"></a>Ta bort replikeringar

Du kan avsluta replikeringen mellan käll-och mål volymerna genom att ta bort volym replikering. Du kan utföra borttagnings åtgärden antingen från käll-eller mål volymen. Borttagnings åtgärden tar bara bort auktorisering för replikering. käll-eller mål volymen tas inte bort. 

## <a name="steps"></a>Steg

1. Om du vill ta bort en volym replikering väljer du **replikering** från käll-eller mål volymen.  

2. Klicka på **Ta bort**.    

3. Bekräfta borttagningen genom att skriva **Ja** och klicka på **ta bort**.   

    ![Ta bort replikering](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Nästa steg  

* [Replikering mellan regioner](cross-region-replication-introduction.md)
* [Krav och överväganden för att använda replikering över flera regioner](cross-region-replication-requirements-considerations.md)
* [Visa hälso status för replikeringsrelation](cross-region-replication-display-health-status.md)
* [Felsöka replikering mellan regioner](troubleshoot-cross-region-replication.md)

