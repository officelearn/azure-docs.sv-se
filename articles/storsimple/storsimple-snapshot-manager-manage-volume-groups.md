---
title: StorSimple Snapshot Manager volymgrupper | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen för att skapa och hantera volymgrupper.
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
ms.openlocfilehash: e84bc790ac577796e91be010deecc8c5cea1b010
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303148"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Använd StorSimple Snapshot Manager för att skapa och hantera volymgrupper
## <a name="overview"></a>Översikt
Du kan använda den **Volymgrupper** nod på den **omfång** fönstret för att tilldela volymer till volymgrupper, visa information om en volym-grupp, schemalägga säkerhetskopieringar och redigera volymgrupper.

Volymgrupper är pooler med relaterade volymer som används för att se till att säkerhetskopieringarna är konsekventa. Mer information finns i [volymer och volymgrupper](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) och [integrering med Windows Volume Shadow Copy-tjänsten](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Alla volymer i en volym-grupp måste komma från en enda molntjänstleverantör.
> * När du konfigurerar volymgrupper Blanda inte klusterdelade volymer (CSV) och icke-CSV: er i gruppen samma volym. StorSimple Snapshot Manager stöder inte en blandning av CSV: er och icke-CSV: er i samma ögonblicksbild.

![Volymen Gruppnod](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Bild 1: Gruppnod för StorSimple Snapshot Manager volym** 

Den här självstudien beskrivs hur du kan använda StorSimple Snapshot Manager till:

* Visa information om din volymgrupper
* Skapa en volym-grupp
* Säkerhetskopiera en volym-grupp
* Redigera en volym-grupp
* Ta bort en volym-grupp

Alla dessa åtgärder är även tillgängliga på den **åtgärder** fönstret.

## <a name="view-volume-groups"></a>Visa volymgrupper
Om du klickar på den **Volymgrupper** noden den **resultat** visar följande information om varje volym-grupp, beroende på kolumnen val du gör. (Kolumnerna i den **resultat** fönstret kan konfigureras. Högerklicka på den **volymer** noden **visa**, och välj sedan **Lägg till/ta bort kolumner**.)

| Resultaten kolumn | Beskrivning |
|:--- |:--- |
| Namn |Den **namn** kolumnen innehåller namnet på gruppen volym. |
| Program |Den **program** kolumn visar antalet VSS-skrivarna är installerade och körs på Windows-värd. |
| Vald |Den **valda** kolumn visar antalet volymer som ingår i gruppen volym. Ett noll (0) anger att inget program är kopplad till volymer i gruppen volym. |
| Har importerats |Den **importerat** kolumn visar antalet importerade volymer. När värdet **SANT**, den här kolumnen anger att en volym-grupp har importerats från Azure-portalen och inte har skapats i StorSimple Snapshot Manager. |

> [!NOTE]
> StorSimple Snapshot Manager volymgrupper visas också på den **principer för säkerhetskopiering** fliken i Azure-portalen.
> 
> 

## <a name="create-a-volume-group"></a>Skapa en volym-grupp
Använd följande procedur för att skapa en volym-grupp.

#### <a name="to-create-a-volume-group"></a>Skapa en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på **Volymgrupper**, och klicka sedan på **Create volym Group**.
   
    ![Skapa volymen grupp](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Den **skapa en volym grupp** dialogrutan visas.
   
    ![Skapa en volym grupp dialog](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Ange följande information:
   
   1. I den **namn** skriver du ett unikt namn för den nya volymen.
   2. I den **program** rutan väljer program som är associerade med de volymer som du kommer att lägga till i gruppen volym.
      
       Den **program** Listar de program som använder StorSimple-volymer och VSS-skrivare som har aktiverats för dem. En VSS-skrivare aktiveras bara om alla volymer som skrivaren är medveten om StorSimple-volymer. Om rutan program är tom, installeras inga program som använder Azure StorSimple-volymer och har stöd för VSS-skrivare. (För närvarande Azure StorSimple stöder Microsoft Exchange och SQL Server.) Läs mer om VSS-skrivare, [integrering med Windows Volume Shadow Copy-tjänsten](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Om du väljer ett program, väljs automatiskt alla volymer som är kopplade till den. Däremot, om du har valt volymer som är associerade med ett visst program kan programmet väljs automatiskt i den **program** box. 
   3. I den **volymer** Välj StorSimple-volymer ska läggas till volymen-gruppen. 
      
      * Du kan inkludera volymer med en eller flera partitioner. (Flera partition volymer kan vara dynamiska diskar eller standarddiskar med flera partitioner.) En volym som innehåller flera partitioner behandlas som en enda enhet. Därför, om du lägger till endast en av partitionerna på en volym-grupp alla andra partitioner läggs automatiskt till volymen gruppen på samma gång. När du lägger till en flera partition volym till en volym-grupp fortsätter flera partition volymen ska behandlas som en enda enhet.
      * Du kan skapa tom volymgrupper genom att inte tilldela dem alla volymer. 
      * Blanda inte klusterdelade volymer (CSV) och icke-CSV: er i gruppen samma volym. StorSimple Snapshot Manager stöder inte en blandning av CSV-volymer och icke-CSV-volymer i samma ögonblicksbild.
4. Klicka på **OK** att spara gruppen volym.

## <a name="back-up-a-volume-group"></a>Säkerhetskopiera en volym-grupp
Använd följande procedur för säkerhetskopiering av en volym-grupp.

#### <a name="to-back-up-a-volume-group"></a>Du säkerhetskopierar en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret expanderar den **Volymgrupper** nod, högerklicka på ett namn på volymen och välj **ta säkerhetskopia**.
   
    ![Säkerhetskopiera volym grupp omedelbart](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. I den **ta säkerhetskopia** dialogrutan **lokal ögonblicksbild** eller **Molnögonblicksbild**, och klicka sedan på **skapa**.
   
    ![Ta säkerhetskopiering](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. För att bekräfta att säkerhetskopieringen körs, expandera den **jobb** noden och klicka sedan på **kör**. Säkerhetskopieringen bör visas.
5. Om du vill visa slutförda ögonblicksbilden, expandera den **säkerhetskopieringskatalog** nod, Expandera namnet på volymen och klicka sedan på **lokal ögonblicksbild** eller **Molnögonblicksbild**. Säkerhetskopieringen kännedom om den har slutförts.

## <a name="edit-a-volume-group"></a>Redigera en volym-grupp
Använd följande procedur om du vill redigera en grupp för volymen.

#### <a name="to-edit-a-volume-group"></a>Redigera en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret expanderar den **Volymgrupper** nod, högerklicka på ett namn på volymen och välj **redigera**.
3. Den ** skapa en volym grupp ** dialogrutan visas. Du kan ändra den **namn**, **program**, och **volymer** poster.
4. Spara ändringarna genom att klicka på **OK**.

## <a name="delete-a-volume-group"></a>Ta bort en volym-grupp
Använd följande procedur för att ta bort en volym-grupp. 

> [!WARNING]
> Detta tar även bort alla säkerhetskopieringar som är associerade med gruppen volym.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Ta bort en volym-grupp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret expanderar den **Volymgrupper** nod, högerklicka på ett namn på volymen och välj **ta bort**.
3. Den **ta bort volymen grupp** dialogrutan visas. Typ **Bekräfta** i textrutan och klicka sedan på **OK**.
   
    Gruppen har tagits bort volym försvinner från listan i den **resultat** fönstret och alla säkerhetskopieringar som är associerade med den volym gruppen tas bort från katalogen för säkerhetskopieringen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md).

