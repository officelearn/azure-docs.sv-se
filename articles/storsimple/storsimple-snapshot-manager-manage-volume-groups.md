---
title: StorSimple Snapshot Manager volym grupper | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att skapa och hantera grupper för volymen.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 6067a88cd42d29c3d2f4b74580095424de77561e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "23875811"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Använda StorSimple Snapshot Manager för att skapa och hantera grupper av volym
## <a name="overview"></a>Översikt
Du kan använda den **volym grupper** nod på den **omfång** att tilldela volymer volym grupper, visa information om en volym-grupp, schemalägga säkerhetskopieringar och redigera volymen grupper.

Volymen grupper är pooler för relaterade volymer som används för att säkerställa att säkerhetskopieringar programkonsekventa. Mer information finns i [volymer och volymen grupper](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) och [integrering med Windows Volume Shadow Copy-tjänsten](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Alla volymer i en volym-grupp måste komma från en enda molntjänstleverantören.
> * När du konfigurerar volymen grupper Blanda inte klusterdelade volymer (CSV) och icke-CSV: er i gruppen samma volym. StorSimple Snapshot Manager stöder inte en blandning av CSV: er och icke-CSV: er i samma ögonblicksbilden.

![Volymen Gruppnod](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Bild 1: StorSimple Snapshot Manager volym Gruppnod** 

Den här självstudiekursen beskriver hur du kan använda StorSimple Snapshot Manager till:

* Visa information om volym-grupper
* Skapa en volym-grupp
* Säkerhetskopiera en volym-grupp
* Redigera en grupp av volym
* Ta bort en volym-grupp

Alla dessa åtgärder är också tillgängliga på den **åtgärder** fönstret.

## <a name="view-volume-groups"></a>Visa grupper av volym
Om du klickar på den **volym grupper** nod, den **resultat** visar följande information om varje volym-grupp, beroende på kolumnen val du har gjort. (Kolumnerna i den **resultat** rutan kan konfigureras. Högerklicka på den **volymer** väljer **visa**, och välj sedan **Lägg till/ta bort kolumner**.)

| Resultaten kolumn | Beskrivning |
|:--- |:--- |
| Namn |Den **namn** kolumnen innehåller namnet på gruppen volym. |
| Program |Den **program** kolumn visar antalet VSS-skrivare som är installerade och körs på Windows-värd. |
| Vald |Den **valda** kolumn visar antalet volymer som ingår i gruppen volym. Noll (0) anger att inget program är associerat med volymer i gruppen volym. |
| Har importerats |Den **importerade** kolumn visar antalet importerade volymer. Om värdet är **SANT**, denna kolumn indikerar att en volym-grupp har importerats från Azure portal och inte har skapats i StorSimple Snapshot Manager. |

> [!NOTE]
> StorSimple Snapshot Manager volym grupper visas också på den **Säkerhetskopieringsprinciper** fliken i Azure-portalen.
> 
> 

## <a name="create-a-volume-group"></a>Skapa en volym-grupp
Använd följande procedur för att skapa en volym-grupp.

#### <a name="to-create-a-volume-group"></a>Skapa en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på **volym grupper**, och klicka sedan på **skapa volymen grupp**.
   
    ![Skapa volymen grupp](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Den **skapa en volym grupp** dialogrutan visas.
   
    ![Skapa en volym grupp dialog](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Ange följande information:
   
   1. I den **namn** Skriv ett unikt namn för den nya volymen.
   2. I den **program** rutan, Välj program som är associerade med de volymer som du lägger till i gruppen volym.
      
       Den **program** Listar de program som använder StorSimple-volymer och VSS-skrivare har aktiverats för dem. En VSS-skrivare aktiveras bara om alla volymer som skrivaren är medveten om StorSimple-volymer. Om rutan program är tomt, är inga program som använder Azure StorSimple-volymer och har stöd för VSS-skrivare installerade. (För närvarande Azure StorSimple stöder Microsoft Exchange och SQL Server.) Mer information om VSS-skrivare finns [integrering med Windows Volume Shadow Copy-tjänsten](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Om du väljer ett program, väljs automatiskt alla volymer som är kopplade till den. Å andra sidan om du väljer volymer som är associerade med ett visst program, programmet väljs automatiskt i den **program** rutan. 
   3. I den **volymer** väljer StorSimple-volymer för att lägga till i gruppen volym. 
      
      * Du kan inkludera volymer med en eller flera partitioner. (Flera volymer för partitionen kan vara dynamiska diskar eller standarddiskar med flera partitioner.) En volym som innehåller flera partitioner behandlas som en enda enhet. Därför om du lägger till endast en av partitionerna på en volym-grupp läggs alla andra partitioner automatiskt till gruppen volymen på samma gång. När du lägger till en flera partition volym till en volym-grupp fortsätter flera partition volymen ska behandlas som en enda enhet.
      * Du kan skapa grupper med tomma volym genom att inte tilldela dem alla volymer. 
      * Blanda inte klusterdelade volymer (CSV) och icke-CSV: er i gruppen samma volym. StorSimple Snapshot Manager stöder inte en blandning av CSV-volymer och icke-CSV-volymer i samma ögonblicksbilden.
4. Klicka på **OK** att spara gruppen volym.

## <a name="back-up-a-volume-group"></a>Säkerhetskopiera en volym-grupp
Använda följande procedur för att säkerhetskopiera en volym-grupp.

#### <a name="to-back-up-a-volume-group"></a>Säkerhetskopiera en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan Expandera den **volym grupper** , högerklickar du på namnet på en volym grupp och klicka sedan på **ta säkerhetskopia**.
   
    ![Säkerhetskopiera volym grupp omedelbart](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. I den **ta säkerhetskopia** dialogrutan **lokal ögonblicksbild** eller **ögonblicksbild i molnet**, och klicka sedan på **skapa**.
   
    ![Ta säkerhetskopiering](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. För att bekräfta att säkerhetskopieringen körs, expandera den **jobb** noden och klicka sedan på **kör**. Säkerhetskopian ska visas.
5. Om du vill visa slutförda ögonblicksbilden Expandera den **säkerhetskopieringskatalog** , expanderar du gruppnamnet för volymen och klicka sedan på **lokal ögonblicksbild** eller **ögonblicksbild i molnet**. Säkerhetskopieringen visas om den har slutförts.

## <a name="edit-a-volume-group"></a>Redigera en grupp av volym
Använd följande procedur om du vill redigera en volym-grupp.

#### <a name="to-edit-a-volume-group"></a>Så här redigerar du en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan Expandera den **volym grupper** , högerklickar du på namnet på en volym grupp och klicka sedan på **redigera**.
3. Den ** skapa en volym grupp ** dialogrutan visas. Du kan ändra den **namn**, **program**, och **volymer** poster.
4. Spara ändringarna genom att klicka på **OK**.

## <a name="delete-a-volume-group"></a>Ta bort en volym-grupp
Använd följande procedur för att ta bort en volym-grupp. 

> [!WARNING]
> Det här tas även bort alla säkerhetskopior som är associerade med gruppen volym.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Ta bort en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan Expandera den **volym grupper** , högerklickar du på namnet på en volym grupp och klicka sedan på **ta bort**.
3. Den **ta bort volymen grupp** dialogrutan visas. Typen **Bekräfta** i textrutan och klicka sedan på **OK**.
   
    Gruppen borttagna volymen försvinner från listan i den **resultat** rutan och alla säkerhetskopior som är associerade med gruppen volym tas bort från katalogen för säkerhetskopieringen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md).

