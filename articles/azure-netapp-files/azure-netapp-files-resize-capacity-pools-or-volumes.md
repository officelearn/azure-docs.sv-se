---
title: Ändra storlek på kapacitetspoolen eller en volym för Azure NetApp-filer | Microsoft-dokument
description: Beskriver hur du ändrar storleken på en kapacitetspool eller en volym.
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
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794618"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ändra storlek på en kapacitetspool eller en volym
Du kan ändra storleken på en kapacitetspool eller en volym efter behov. 

## <a name="resize-the-capacity-pool"></a>Ändra storlek på kapacitetspoolen 

Du kan ändra kapacitetspoolstorleken i steg om 1-TiB eller minskningar. Kapacitetens poolstorlek kan dock inte vara mindre än 4 TiB. Ändra storlek på kapacitetspoolen ändras den köpta Azure NetApp Files-kapaciteten.

1. Klicka på den kapacitetspool som du vill ändra storlek på i bladet Hantera NetApp-konto. 
2. Högerklicka på kapacitetspoolens namn eller klicka på "..." i slutet av kapacitetspoolens rad för att visa snabbmenyn. 
3. Använd alternativ för snabbmeny för att ändra storlek på eller ta bort kapacitetspoolen.

## <a name="resize-a-volume"></a>Ändra storlek på en volym

Du kan ändra storleken på en volym efter behov. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.

1. Klicka på **Volymer**i bladet Hantera NetApp-konto . 
2. Högerklicka på namnet på den volym som du vill ändra storlek på eller klicka på "..." i slutet av volymens rad för att visa snabbmenyn.
3. Använd snabbmenyalternativen för att ändra storlek på eller ta bort volymen.

