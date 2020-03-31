---
title: Volymgrupper för StorSimple Snapshot Manager | Microsoft-dokument
description: Beskriver hur du använder MMC-snapin-modulen StorSimple Snapshot Manager för att skapa och hantera volymgrupper.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 5198729cf96fb48c5dcd05096c04ea4d77c26de5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931497"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Använda StorSimple Snapshot Manager för att skapa och hantera volymgrupper
## <a name="overview"></a>Översikt
Du kan använda noden **Volymgrupper** i **fönstret Omfattning** för att tilldela volymer till volymgrupper, visa information om en volymgrupp, schemalägga säkerhetskopior och redigera volymgrupper.

Volymgrupper är pooler med relaterade volymer som används för att säkerställa att säkerhetskopior är programkonsekventa. Mer information finns i [Volymer och volymgrupper](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) och [Integration med Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Alla volymer i en volymgrupp måste komma från en enda molntjänstleverantör.
> * När du konfigurerar volymgrupper ska du inte blanda klusterdelade volymer (CSV: ar) och icke-CSV:er i samma volymgrupp. StorSimple Snapshot Manager stöder inte en blandning av CSV:er och icke-CSV:er i samma ögonblicksbild.

![Nod för volymgrupper](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Bild 1: StorSimple-volymgruppsnod för ögonblicksbildhanterare** 

I den här självstudien beskrivs hur du kan använda StorSimple Snapshot Manager för att:

* Visa information om dina volymgrupper
* Skapa en volymgrupp
* Säkerhetskopiera en volymgrupp
* Redigera en volymgrupp
* Ta bort en volymgrupp

Alla dessa åtgärder är också tillgängliga i fönstret **Åtgärder.**

## <a name="view-volume-groups"></a>Visa volymgrupper
Om du klickar på noden **Volymgrupper** visas följande information om varje volymgrupp i fönstret **Resultat,** beroende på vilka kolumnval du gör. (Kolumnerna i **resultatfönstret** kan konfigureras. Högerklicka på **noden Volymer,** välj **Visa**och välj sedan **Lägg till/ta bort kolumner**.)

| Kolumnen Resultat | Beskrivning |
|:--- |:--- |
| Namn |Kolumnen **Namn** innehåller namnet på volymgruppen. |
| Program |Kolumnen **Program** visar antalet VSS-författare som för närvarande är installerade och körs på Windows-värden. |
| Vald |I kolumnen **Markerad** visas antalet volymer som finns i volymgruppen. En nolla (0) anger att inget program är associerat med volymerna i volymgruppen. |
| Importerade |Kolumnen **Importerad** visar antalet importerade volymer. När den här kolumnen är **true**anger den att en volymgrupp har importerats från Azure-portalen och inte skapades i StorSimple Snapshot Manager. |

> [!NOTE]
> Volymgrupper för StorSimple Snapshot Manager visas också på fliken **Principer för säkerhetskopiering** i Azure-portalen.
> 
> 

## <a name="create-a-volume-group"></a>Skapa en volymgrupp
Använd följande procedur för att skapa en volymgrupp.

#### <a name="to-create-a-volume-group"></a>Så här skapar du en volymgrupp
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på **Volymgrupper**i **fönstret Omfattning** och klicka sedan på **Skapa volymgrupp**.
   
    ![Skapa volymgrupp](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Dialogrutan **Skapa en volymgrupp** visas.
   
    ![Skapa en dialogruta för en volymgrupp](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Ange följande information:
   
   1. Skriv ett unikt namn för den nya volymgruppen i rutan **Namn.**
   2. I rutan **Program** väljer du program som är associerade med de volymer som du ska lägga till i volymgruppen.
      
       I rutan **Program** visas endast de program som använder StorSimple-volymer och har VSS-skribenter aktiverade för dem. En VSS-skrivare aktiveras endast om alla volymer som författaren känner till är StorSimple-volymer. Om rutan Program är tom installeras inga program som använder Azure StorSimple-volymer och som stöds av VSS-skribenter. (Azure StorSimple stöder för närvarande Microsoft Exchange och SQL Server.) Mer information om VSS-författare finns i [Integration med Windows Volume Shadow Copy Service](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Om du väljer ett program markeras alla associerade volymer automatiskt. Om du däremot väljer volymer som är associerade med ett visst program markeras programmet automatiskt i rutan **Program.** 
   3. I rutan **Volymer** väljer du StorSimple-volymer att lägga till i volymgruppen. 
      
      * Du kan inkludera volymer med enstaka eller flera partitioner. (Flera partitionsvolymer kan vara dynamiska diskar eller grundläggande diskar med flera partitioner.) En volym som innehåller flera partitioner behandlas som en enda enhet. Om du bara lägger till en av partitionerna i en volymgrupp läggs därför alla andra partitioner automatiskt till i den volymgruppen samtidigt. När du har lagt till en multipel partitionsvolym i en volymgrupp fortsätter volymen för flera partitioner att behandlas som en enda enhet.
      * Du kan skapa tomma volymgrupper genom att inte tilldela några volymer till dem. 
      * Blanda inte klusterdelade volymer (CSV) och icke-CSV:er i samma volymgrupp. StorSimple Snapshot Manager stöder inte en blandning av CSV-volymer och icke-CSV-volymer i samma ögonblicksbild.
4. Spara volymgruppen genom att klicka på **OK.**

## <a name="back-up-a-volume-group"></a>Säkerhetskopiera en volymgrupp
Använd följande procedur för att säkerhetskopiera en volymgrupp.

#### <a name="to-back-up-a-volume-group"></a>Så här säkerhetskopierar du en volymgrupp
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **Volymgrupper** i **fönstret Omfattning,** högerklicka på ett volymgruppsnamn och klicka sedan på **Ta upp säkerhetskopiering**.
   
    ![Säkerhetskopiera volymgruppen omedelbart](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. I dialogrutan **Ta säkerhetskopiering** väljer du **Lokal ögonblicksbild** eller Ögonblicksbild **i molnet**och klickar sedan på **Skapa**.
   
    ![Dialogrutan Ta med säkerhetskopiering](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Om du vill bekräfta att säkerhetskopian körs expanderar du noden **Jobb** och klickar sedan på **Kör**. Säkerhetskopian bör anges.
5. Om du vill visa den slutförda ögonblicksbilden expanderar du **noden Säkerhetskopieringskatalog,** expanderar volymgruppnamnet och klickar sedan på **Lokal ögonblicksbild** eller **Ögonblicksbild av molnet**. Säkerhetskopian visas om den har slutförts.

## <a name="edit-a-volume-group"></a>Redigera en volymgrupp
Använd följande procedur för att redigera en volymgrupp.

#### <a name="to-edit-a-volume-group"></a>Så här redigerar du en volymgrupp
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **Volymgrupper** i **fönstret Omfattning,** högerklicka på ett volymgruppsnamn och klicka sedan på **Redigera**.
3. Dialogrutan **Skapa en volymgrupp **visas. Du kan ändra posterna **Namn,** **Program**och **Volymer.**
4. Spara ändringarna genom att klicka på **OK**.

## <a name="delete-a-volume-group"></a>Ta bort en volymgrupp
Använd följande procedur för att ta bort en volymgrupp. 

> [!WARNING]
> Detta tar också bort alla säkerhetskopior som är associerade med volymgruppen.
> 
> 

#### <a name="to-delete-a-volume-group"></a>Så här tar du bort en volymgrupp
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Expandera noden **Volymgrupper** i **fönstret Omfattning,** högerklicka på ett volymgruppsnamn och klicka sedan på **Ta bort**.
3. Dialogrutan **Ta bort volymgrupp** visas. Skriv **Bekräfta** i textrutan och klicka sedan på **OK**.
   
    Den borttagna volymgruppen försvinner från listan i **resultatfönstret** och alla säkerhetskopior som är associerade med den volymgruppen tas bort från säkerhetskopieringskatalogen.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md).

