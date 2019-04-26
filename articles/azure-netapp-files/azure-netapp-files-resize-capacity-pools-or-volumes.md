---
title: Ändra storlek på poolen kapacitet eller en volym för Azure NetApp Files | Microsoft Docs
description: Beskriver hur du ändrar storleken på poolen kapacitet eller en volym.
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
ms.date: 04/03/2018
ms.author: b-juche
ms.openlocfilehash: f08eaee038ad18c600826dea6fe0fd85935de59a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452916"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ändra storlek på en kapacitetspool eller en volym
Du kan ändra storleken på poolen kapacitet eller en volym som behövs. 

## <a name="resize-the-capacity-pool"></a>Ändra storlek på poolen kapacitet 

Du kan ändra kapacitetsstorlek för poolen i 4 TiB ökar eller minskar. Ändrar storlek på poolen kapacitet ändras den köpta Azure NetApp Files-kapaciteten.

1. Från bladet hantera NetApp-konto klickar du på poolen kapacitet som du vill ändra storlek på. 
2. Högerklicka på kapacitetsnamnet för pool eller klicka på ikonen ”...” i slutet av kapacitet poolens rad för att öppna snabbmenyn. 
3. Använda menyalternativen kontext för att ändra storlek på eller ta bort poolen kapacitet.

## <a name="resize-a-volume"></a>Ändra storlek på en volym

Du kan ändra storleken på en volym som behövs. En volyms kapacitetsförbrukning mäts mot dess pools etablerade kapacitet.

1. Klicka på Hantera NetApp-kontobladet på **volymer**. 
2. Högerklicka på namnet på den volym som du vill ändra storlek på eller klicka på ikonen ”...” i slutet av volymens rad för att öppna snabbmenyn.
3. Använda menyalternativen kontext för att ändra storlek på eller ta bort volymen.

