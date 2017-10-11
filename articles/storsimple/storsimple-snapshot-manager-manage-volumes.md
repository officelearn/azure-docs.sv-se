---
title: StorSimple Snapshot Manager och volymer | Microsoft Docs
description: "Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen att visa och hantera volymer och konfigurera säkerhetskopieringar."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 2c0b211bced99d272a73a7b018a22f99d8d58aa9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Använd StorSimple Snapshot Manager för att visa och hantera volymer
## <a name="overview"></a>Översikt
Du kan använda StorSimple Snapshot Manager **volymer** nod (på den **omfång** fönstret) Välj volymer och visa information om dessa. Volymerna visas som enheter som är kopplade till volymer som monterats av värden. Den **volymer** noden visar lokala volymer och volymtyper som stöds av StorSimple, inklusive volymer som identifieras med hjälp av iSCSI- och en enhet. 

Mer information om volymer som stöds går du till [stöd för flera volymtyper av](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista över volymen i resultatfönstret](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Den **volymer** nod kan du skanna om eller ta bort volymer när StorSimple Snapshot Manager identifierar dem också. 

Den här självstudiekursen beskriver hur du kan montera, initiera, och formatera volymer och sedan använda StorSimple Snapshot Manager till:

* Visa information om volymer 
* Ta bort volymer
* Skanna volymer 
* Konfigurera en enkel volym och säkerhetskopiera den
* Konfigurera en dynamisk speglad volym och säkerhetskopiera den

> [!NOTE]
> Alla de **volym** noden åtgärder är också tillgängliga i den **åtgärder** fönstret.
> 
> 

## <a name="mount-volumes"></a>Montera volymer
Använd följande procedur för att montera, initiera och formatera StorSimple-volymer. Den här proceduren använder Diskhantering, ett systemverktyg som används för att hantera hårddiskar och motsvarande volymer eller partitioner. Mer information om Diskhantering, gå till [Diskhantering](https://technet.microsoft.com/library/cc770943.aspx) på webbplatsen Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Montera volymer
1. Starta Microsoft iSCSI-initieraren på din värddator.
2. Ange en av IP-adresser för gränssnitt som målportalen eller identifiering av IP-adress och ansluta till enheten. När enheten är ansluten vara volymer tillgängliga för Windows-systemet. Mer information om hur du använder Microsoft iSCSI-initieraren finns i avsnittet ”ansluta till en iSCSI-målet enhet” i [installera och konfigurera Microsoft iSCSI Initiator][1].
3. Använd någon av följande alternativ för att starta Diskhantering:
   
   * Skriv Diskmgmt.msc i den **kör** rutan.
   * Starta Serverhanteraren, expandera den **lagring** och sedan väljer **Diskhantering**.
   * Starta **Administrationsverktyg**, expandera den **Datorhantering** och sedan väljer **Diskhantering**. 
     
     > [!NOTE]
     > Du måste använda administratörsbehörighet för att köra Diskhantering.
     > 
     > 
4. Ta volymerna online:
   
   1. Högerklicka på den volym som markerats i Diskhantering **Offline**.
   2. Klicka på **reaktivera Disk**. Disken bör markeras **Online** när disken återaktiveras.
5. Initiera volymerna:
   
   1. Högerklicka på de identifierade volymerna.
   2. Välj på menyn **initiera Disk**.
   3. I den **initiera Disk** dialogrutan, Välj de diskar som du vill initiera och klicka sedan på **OK**.
6. Formatera enkla volymer:
   
   1. Högerklicka på en volym som du vill formatera.
   2. Välj på menyn **ny enkel volym**.
   3. Använd guiden Ny enkel volym för att formatera volymen:
      
      * Ange volymens storlek.
      * Ange en enhetsbeteckning.
      * Välj NTFS-filsystemet.
      * Ange en storlek på allokeringsenheterna på 64 KB.
      * Utför en snabbformatering.
7. Formatera partition flera volymer. Anvisningar finns i avsnittet ”partitioner och volymer” [implementera Diskhantering](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Visa information om dina volymer
Använd följande procedur om du vill visa information om lokala och Azure StorSimple-volymer.

#### <a name="to-view-volume-information"></a>Visa information om volume
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** rutan klickar du på den **volymer** nod. En lista över lokala och monterade volymer, inklusive alla Azure StorSimple-volymer, som visas i den **resultat** fönstret. Kolumnerna i den **resultat** rutan kan konfigureras. (Högerklicka på den **volymer** väljer **visa**, och välj sedan **Lägg till/ta bort kolumner**.)
   
    ![Konfigurera kolumnerna](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Resultaten kolumn | Beskrivning |
   |:--- |:--- |
   |  Namn |Den **namn** kolumnen innehåller enhetsbeteckningen som tilldelats till varje identifierad volym. |
   |  Enhet |Den **enhet** kolumnen innehåller IP-adressen för den enhet som är ansluten till värddatorn. |
   |  Enheten volymnamn |Den **volym enhetsnamn** kolumnen innehåller namnet på enheten volymen som tillhör den valda volymen. Detta är volymnamn som definierats i Azure-portalen för den specifika volymen. |
   |  Sökvägar för åtkomst |Den **åtkomst sökvägar** kolumnen visar åtkomstsökvägen till volymen. Detta är den enhet enhetsbeteckning eller monteringspunkt som volymen är tillgänglig på värddatorn. |

## <a name="delete-a-volume"></a>Ta bort en volym
Använd följande procedur för att ta bort en volym från StorSimple Snapshot Manager.

> [!NOTE]
> Du kan inte ta bort en volym om det är en del av en volym-grupp. (Alternativet Ta bort är inte tillgänglig för volymer som är medlemmar i en grupp för volymen.) Du måste ta bort gruppen hela volymen om du vill ta bort volymen.

#### <a name="to-delete-a-volume"></a>Ta bort en volym
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** rutan klickar du på den **volymer** nod. 
3. I den **resultat** fönstret, högerklicka på den volym som du vill ta bort.
4. Klicka på menyn **ta bort**. 
   
    ![Ta bort en volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Den **ta bort volym** dialogrutan visas. Typen **Bekräfta** i textrutan och klicka sedan på **OK**.
6. Som standard säkerhetskopierar StorSimple Snapshot Manager en volym innan den tas bort. Den här försiktighetsåtgärden skydda du förlorar data om borttagningen släpptes avsiktligt. StorSimple Snapshot Manager visar en **automatisk ögonblicksbild** pågående meddelande medan den säkerhetskopierar volymen. 
   
    ![Automatisk ögonblicksbild meddelande](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Skanna volymer
Använd följande procedur för att skanna volymer som är anslutna till StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Att skanna volymer
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på **volymer**, och klicka sedan på **skanna volymer**.
   
    ![Skanna volymer](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Den här proceduren synkroniserar volymlistan med StorSimple Snapshot Manager. Alla ändringar, till exempel nya volymer eller borttagna volymer visas i resultaten.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurera och säkerhetskopiera en enkel volym
Använd följande procedur för att konfigurera en säkerhetskopia av en enkel volym och sedan starta en säkerhetskopiering direkt eller skapa en princip för schemalagda säkerhetskopieringar.

### <a name="prerequisites"></a>Krav
Innan du börjar:

* Kontrollera att datorn StorSimple-enhet och värden är korrekt konfigurerade. Mer information finns på [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information finns på [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Att konfigurera säkerhetskopiering för en enkel volym
1. Skapa en enkel volym på StorSimple-enheten.
2. Montera, initiera och formatera volymen som beskrivs i [montera volymer](#mount-volumes). 
3. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. StorSimple Snapshot Manager öppnas. 
4. I den **omfång** fönstret högerklickar du på den **volymer** och sedan väljer **skanna volymer**. När genomsökningen är klar visas en lista över volymer som ska visas i den **resultat** fönstret. 
5. I den **resultat** fönstret, högerklicka på volymen och välj sedan **skapa volymen grupp**. 
   
    ![Skapa volymen grupp](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. I den **skapa volymen grupp** i dialogrutan anger du ett namn för gruppen volymen, tilldela volymer till den och klicka sedan på **OK**.
7. I den **omfång** rutan Expandera den **volym grupper** nod. Den nya volym gruppen bör visas under den **volym grupper** nod. 
8. Högerklicka på namnet på volymen.
   
   * Starta en säkerhetskopiering för interaktiva (på begäran), klicka på **ta säkerhetskopiering**. 
   * Om du vill schemalägga en automatisk säkerhetskopiering klickar du på **skapa säkerhetskopieringsprincip**. På den **allmänna** sidan, Välj en volym i listan. På den **schema** anger schema-information. När du är klar klickar du på **OK**. 
9. För att bekräfta att jobbet har startats, expandera den **jobb** nod i den **omfång** rutan och klicka sedan på den **kör** nod. Listan över jobb som körs visas i den **resultat** fönstret. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurera och säkerhetskopiera en dynamisk speglad volym
Utför följande steg för att konfigurera säkerhetskopiering av en dynamisk speglad volym:

* Steg 1: Använd Disk Management för att skapa en dynamisk speglad volym. 
* Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhetskopiering.

### <a name="prerequisites"></a>Krav
Innan du börjar:

* Kontrollera att datorn StorSimple-enhet och värden är korrekt konfigurerade. Mer information finns på [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information finns på [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Konfigurera två volymer på StorSimple-enheten. (I exemplen är de tillgängliga volymerna är **Disk 1** och **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Steg 1: Använd Disk Management för att skapa en dynamisk speglad volym
Diskhantering är ett systemverktyg som används för att hantera hårddiskar och volymer och partitioner som de innehåller. Mer information om Diskhantering, gå till [Diskhantering](https://technet.microsoft.com/library/cc770943.aspx) på webbplatsen Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Så här skapar du en dynamisk speglad volym
1. Använd någon av följande alternativ för att starta Diskhantering: 
   
   * Öppna den **kör** skriver **Diskmgmt.msc**, och tryck på RETUR.
   * Starta Serverhanteraren, expandera den **lagring** och sedan väljer **Diskhantering**. 
   * Starta **Administrationsverktyg**, expandera den **Datorhantering** och sedan väljer **Diskhantering**. 
2. Kontrollera att du har två volymer som är tillgängliga på StorSimple-enheten. (I det här exemplet är de tillgängliga volymerna **Disk 1** och **Disk 2**.) 
3. Högerklicka i fönstret Diskhantering i den högra kolumnen i den nedre rutan **Disk 1** och välj **ny speglad volym**. 
   
    ![Ny speglad volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. På den **ny speglad volym** sidan i guiden klickar du på **nästa**.
5. På den **Välj diskar** väljer **Disk 2** i den **valda** rutan klickar du på **Lägg till**, och klicka sedan på **nästa**. 
6. På den **tilldela enhetsbeteckning eller sökväg** , acceptera standardinställningarna och klicka sedan på **nästa**. 
7. På den **Formatera volym** sidan den **storlek på allokeringsenhet** väljer **64 kB**. Välj den **utför en snabbformatering** kryssrutan och klicka sedan på **nästa**. 
8. På den **ny speglad volym slutförs** sidan, granskar du inställningarna och klicka sedan på **Slutför**. 
9. Ett meddelande visas som indikerar att den grundläggande disken kommer att konverteras till en dynamisk disk. Klicka på **Ja**.
   
    ![Meddelandet dynamisk disk](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Se till att Disk 1 och 2 disken visas dynamiska speglade volymer i Diskhantering. (**Dynamiska** ska visas i statuskolumnen och kapacitet fältet färg bör ändras till rött, som anger en speglad volym.) 
    
    ![Disk Management speglad dynamiska diskar](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhetskopiering
Använd följande procedur för att konfigurera en dynamisk speglad volym, och sedan starta en säkerhetskopiering direkt eller skapa en princip för schemalagda säkerhetskopieringar.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Att konfigurera säkerhetskopiering av en dynamisk speglad volym
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. StorSimple Snapshot Manager öppnas. 
2. I den **omfång** fönstret högerklickar du på den **volymer** och välj **skanna volymer**. När genomsökningen är klar visas en lista över volymer som ska visas i den **resultat** fönstret. Dynamisk speglad volym har listats som en enskild volym. 
3. I den **resultat** , högerklicka på den dynamiska volymen, och klickar sedan på **skapa volymen grupp**. 
4. I den **skapa volymen grupp** i dialogrutan anger du ett namn för gruppen volymen, tilldela dynamisk speglad volym till den här gruppen och klicka sedan på **OK**. 
5. I den **omfång** rutan Expandera den **volym grupper** nod. Den nya volym gruppen bör visas under den **volym grupper** nod. 
6. Högerklicka på namnet på volymen. 
   
   * Starta en säkerhetskopiering för interaktiva (på begäran), klicka på **ta säkerhetskopiering**. 
   * Om du vill schemalägga en automatisk säkerhetskopiering klickar du på **skapa säkerhetskopieringsprincip**. På den **allmänna** väljer du gruppen volym i listan. På den **schema** anger schema-information. När du är klar klickar du på **OK**. 
7. Du kan övervaka säkerhetskopieringsjobbet medan den körs. I den **omfång** rutan Expandera den **jobb** noden och klicka sedan på **kör**, Jobbinformationen visas i den **resultat** fönstret. När säkerhetskopieringsjobbet är klart information överförs till den **senaste 24** timmar jobbet lista. 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att skapa och hantera grupper volym](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
