---
title: ta med fil
description: ta med fil
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "67187390"
---
Om du vill ta bort en volym behållare måste du
 - ta bort volymer i volym containern. Om volym containern har associerade volymer ska du koppla från dessa volymer först. Följ stegen i koppla från [en volym](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). När volymerna är offline kan du ta bort dem. 
 - ta bort tillhör ande säkerhets kopierings principer och moln ögonblicks bilder. Kontrol lera om volym containern har tillhör ande säkerhets kopierings principer och moln ögonblicks bilder. I så fall [tar du bort säkerhets kopierings principerna](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Detta tar även bort moln ögonblicks bilderna. 
 
När volym containern inte har några associerade volymer, säkerhets kopierings principer och moln ögonblicks bilder kan du ta bort den. Utför följande procedur för att ta bort en volym behållare.

#### <a name="to-delete-a-volume-container"></a>Ta bort en volym behållare
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Markera och klicka på enheten och gå sedan till **inställningar > hantera > volym behållare**.

    ![Bladet volym behållare](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Välj den volym behållare som du vill ta bort i tabell listan över volym behållare, högerklicka på **...** och välj sedan **ta bort**.

    ![Ta bort volym behållare](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Om en volym behållare saknar associerade volymer, säkerhets kopierings principer och moln ögonblicks bilder kan den tas bort. När du uppmanas att bekräfta, granskar och markerar du kryss rutan som visar effekten av att ta bort volym containern. Klicka på **ta bort** för att ta bort volym containern.

    ![Bekräfta borttagning](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Listan över volym behållare uppdateras för att avspegla den borttagna volym containern.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


