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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7d1c54d05430600e9fffa2659dbaaf6db0b086b5
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512881"
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

