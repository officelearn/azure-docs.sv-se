---
title: StorSimple Snapshot Manager och volymer | Microsoft Docs
description: Beskriver hur du använder StorSimple Snapshot Manager MMC-snapin-modulen att visa och hantera volymer och konfigurera säkerhetskopieringar.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 260dfdd4b8fe7c277358fa5773029ea9a532740a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078363"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Använd StorSimple Snapshot Manager för att visa och hantera volymer
## <a name="overview"></a>Översikt
Du kan använda StorSimple Snapshot Manager **volymer** node (på den **omfång** fönstret) välja volymer och visa information om dem. Volymerna visas som enheter som relaterar till volymer som monterats av värden. Den **volymer** noden visar lokala volymer och volymtyper som stöds av StorSimple, inklusive volymer som identifieras med hjälp av iSCSI- och en enhet. 

Mer information om volymer som stöds går du till [stöd för flera volymtyper av](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Volymlistan i resultatfönstret](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Den **volymer** noden kan du skanna om eller ta bort volymer när StorSimple Snapshot Manager identifierar dem också. 

Den här självstudien beskrivs hur du kan montera, initiera, och formatera volymer och sedan använda StorSimple Snapshot Manager till:

* Visa information om volymer 
* Ta bort volymer
* Genomsök igen volymer 
* Konfigurera en enkel volym och säkerhetskopiera den
* Konfigurera en dynamisk speglad volym och säkerhetskopiera den

> [!NOTE]
> Alla de **volym** noden åtgärder är även tillgängliga i den **åtgärder** fönstret.
> 
> 

## <a name="mount-volumes"></a>Monteringsvolymer
Använd följande procedur för att montera, initiera och formatera StorSimple-volymer. Den här proceduren använder Diskhantering, ett systemverktyg som används för att hantera hårddiskar och motsvarande volymer eller partitioner. Mer information om Diskhantering går du till [Diskhantering](https://technet.microsoft.com/library/cc770943.aspx) på webbplatsen Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Montera volymer
1. Starta Microsoft iSCSI-initieraren på din värddator.
2. Ange en av gränssnittet IP-adresser som målportal eller identifiering av IP-adress och ansluta till enheten. När enheten är ansluten, vara volymerna tillgängliga i Windows-systemet. Mer information om hur du använder Microsofts iSCSI-initierare finns i avsnittet ”ansluta till en iSCSI-målet enhet” i [installera och konfigurera Microsoft iSCSI Initiator][1].
3. Använd någon av följande alternativ för att starta Diskhantering:
   
   * Skriv Diskmgmt.msc i den **kör** box.
   * Starta Serverhanteraren, expandera den **Storage** noden och välj sedan **Diskhantering**.
   * Starta **Administrationsverktyg**, expandera den **Datorhantering** noden och välj sedan **Diskhantering**. 
     
     > [!NOTE]
     > Du måste använda administratörsbehörighet för att köra Diskhantering.
     > 
     > 
4. Ta volymerna online:
   
   1. I Diskhantering högerklickar du på en volym som markerats **Offline**.
   2. Klicka på **reaktivera Disk**. Disken bör markeras **Online** när disken återaktiveras.
5. Initiera den eller de volymen:
   
   1. Högerklicka på de identifierade volymerna.
   2. På menyn, Välj **initiera Disk**.
   3. I den **initiera Disk** dialogrutan, Välj de diskar som du vill initiera och klicka sedan på **OK**.
6. Formatera enkla volymer:
   
   1. Högerklicka på en volym som du vill formatera.
   2. På menyn, Välj **ny enkel volym**.
   3. Använd guiden Ny enkel volym för att formatera volymen:
      
      * Ange volymens storlek.
      * Ange en enhetsbeteckning.
      * Välj NTFS-filsystemet.
      * Ange en storlek på allokeringsenheterna på 64 KB.
      * Utför en snabbformatering.
7. Formatera partition flera volymer. Anvisningar finns i avsnittet ”partitioner och volymer” [implementera Diskhantering](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Visa information om dina volymer
Använd följande procedur om du vill visa information om lokala och Azure StorSimple-volymer.

#### <a name="to-view-volume-information"></a>Visa volyminformation
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** fönstret klickar du på den **volymer** noden. En lista över lokala och monterade volymer, inklusive alla Azure StorSimple-volymer, visas i den **resultat** fönstret. Kolumnerna i den **resultat** fönstret kan konfigureras. (Högerklicka på den **volymer** noden **visa**, och välj sedan **Lägg till/ta bort kolumner**.)
   
    ![Konfigurera kolumner](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Resultaten kolumn | Beskrivning |
   |:--- |:--- |
   |  Namn |Den **namn** kolumnen innehåller enhetsbeteckningen som tilldelats varje identifierad volym. |
   |  Enhet |Den **enhet** kolumnen innehåller IP-adressen för den enhet som är anslutna till värddatorn. |
   |  Enhetsnamn för volym |Den **volym enhetsnamn** kolumnen innehåller namnet på enheten volymen som tillhör den valda volymen. Det här är volymens namn som definierats i Azure-portalen för den specifika volymen. |
   |  Åtkomstsökvägar |Den **Åtkomstsökvägar** kolumnen visar åtkomstsökvägen till volymen. Detta är den enhet enhetsbeteckning eller monteringspunkt där volymen finns tillgänglig på värddatorn. |

## <a name="delete-a-volume"></a>Ta bort en volym
Använd följande procedur för att ta bort en volym från StorSimple Snapshot Manager.

> [!NOTE]
> Du kan inte ta bort en volym om det är en del av en volym-grupp. (Alternativet Ta bort är inte tillgänglig för volymer som är medlemmar i en grupp för volymen.) Du måste ta bort gruppen hela volymen om du vill ta bort volymen.

#### <a name="to-delete-a-volume"></a>Att ta bort en volym
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret klickar du på den **volymer** noden. 
3. I den **resultat** fönstret högerklickar du på den volym som du vill ta bort.
4. På menyn klickar du på **ta bort**. 
   
    ![Ta bort en volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Den **ta bort volym** dialogrutan visas. Typ **Bekräfta** i textrutan och klicka sedan på **OK**.
6. Som standard säkerhetskopierar StorSimple Snapshot Manager en volym innan den tas bort. Den här försiktighetsåtgärden skydda dig mot dataförlust om borttagningen inte släpptes avsiktligt. StorSimple Snapshot Manager visar en **automatiska ögonblicksbilder** förloppsmeddelande medan den säkerhetskopierar volymen. 
   
    ![Automatiska ögonblicksbilder meddelande](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Genomsök igen volymer
Använd följande procedur för att skanna volymer som är anslutna till StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Skanna volymerna
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på **volymer**, och klicka sedan på **Genomsök igen volymer**.
   
    ![Genomsök igen volymer](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Den här proceduren synkroniserar volymlistan med StorSimple Snapshot Manager. Alla ändringar, till exempel nya volymer eller borttagna volymer visas i resultaten.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurera och säkerhetskopiering av en enkel volym
Använd följande procedur för att konfigurera en säkerhetskopia av en enkel volym och sedan starta en säkerhetskopiering omedelbart eller skapa en princip för schemalagda säkerhetskopieringar.

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar:

* Se till att StorSimple-enhets- och datorn är rätt konfigurerade. Mer information går du till [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information går du till [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Att konfigurera säkerhetskopiering för en enkel volym
1. Skapa en enkel volym på StorSimple-enheten.
2. Montera, initiera och formatera volymen som beskrivs i [Monteringsvolymer](#mount-volumes). 
3. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. StorSimple Snapshot Manager-fönster visas. 
4. I den **omfång** fönstret högerklickar du på den **volymer** noden och välj sedan **Genomsök igen volymer**. När genomsökningen är klar visas en lista över volymer ska visas i den **resultat** fönstret. 
5. I den **resultat** , högerklicka på volymen och välj sedan **Create volym Group**. 
   
    ![Skapa volymen grupp](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. I den **Create volym Group** dialogrutan Skriv ett namn för gruppen volym, tilldela volymer till den och klicka sedan på **OK**.
7. I den **omfång** fönstret expanderar den **Volymgrupper** noden. Den nya volym gruppen bör visas under den **Volymgrupper** noden. 
8. Högerklicka på namnet på volymen.
   
   * Starta en säkerhetskopiering för interaktiva (på begäran), klicka på **ta säkerhetskopiering**. 
   * Om du vill schemalägga en automatisk säkerhetskopiering, klickar du på **skapa Säkerhetskopieringsprincipen**. På den **Allmänt** markerar du en volym-grupp i listan. På den **schema** anger du information om Jobbschema. När du är klar klickar du på **OK**. 
9. För att bekräfta att säkerhetskopieringsjobbet har startats, expandera den **jobb** nod i den **omfång** fönstret och klicka sedan på den **kör** noden. Listan över jobb som körs för närvarande visas i den **resultat** fönstret. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurera och säkerhetskopierar en dynamisk speglad volym
Utför följande steg för att konfigurera säkerhetskopiering för en dynamisk speglad volym:

* Steg 1: Använd Diskhantering för att skapa en dynamisk speglad volym. 
* Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhetskopiering.

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar:

* Se till att StorSimple-enhets- och datorn är rätt konfigurerade. Mer information går du till [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information går du till [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Konfigurera två volymer på StorSimple-enheten. (I exemplen är de tillgängliga volymerna är **Disk 1** och **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Steg 1: Använda Diskhantering för att skapa en dynamisk speglad volym
Diskhantering är ett systemverktyg som används för att hantera hårddiskar och volymer eller partitioner som de innehåller. Mer information om Diskhantering går du till [Diskhantering](https://technet.microsoft.com/library/cc770943.aspx) på webbplatsen Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Skapa en dynamisk speglad volym
1. Använd någon av följande alternativ för att starta Diskhantering: 
   
   * Öppna den **kör** skriver **Diskmgmt.msc**, och tryck på RETUR.
   * Starta Serverhanteraren, expandera den **Storage** noden och välj sedan **Diskhantering**. 
   * Starta **Administrationsverktyg**, expandera den **Datorhantering** noden och välj sedan **Diskhantering**. 
2. Se till att du har två volymer som är tillgängliga på StorSimple-enheten. (I det här exemplet är de tillgängliga volymerna **Disk 1** och **Disk 2**.) 
3. I fönstret Diskhantering i den högra kolumnen i den nedre rutan högerklickar du på **Disk 1** och välj **ny speglad volym**. 
   
    ![Ny speglad volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. På den **ny speglad volym** sidan i guiden klickar du på **nästa**.
5. På den **Välj diskar** väljer **Disk 2** i den **valda** fönstret klickar du på **Lägg till**, och klicka sedan på **nästa**. 
6. På den **tilldela enhetsbeteckning eller sökväg** sidan, acceptera standardinställningarna och klicka sedan på **nästa**. 
7. På den **Format volym** sidan den **storlek på allokeringsenhet** väljer **64 kB**. Välj den **utför en snabbformatering** och klicka sedan på **nästa**. 
8. På den **nya speglad volym slutförs** sidan, granskar du inställningarna och klicka sedan på **Slutför**. 
9. Det visas ett meddelande som anger att en grundläggande disk kommer att konverteras till en dynamisk disk. Klicka på **Ja**.
   
    ![Meddelande för konvertering av dynamisk disk](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Kontrollera att Disk 1 och Disk 2 visas dynamiska speglade volymer i Diskhantering. (**Dynamisk** ska visas i statuskolumnen och kapacitet fältet färgen bör ändras till rött, som anger en speglad volym.) 
    
    ![Disk Management speglad dynamiska diskar](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhetskopiering
Använd följande procedur för att konfigurera en dynamisk speglad volym, och sedan starta en säkerhetskopiering omedelbart eller skapa en princip för schemalagda säkerhetskopieringar.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Att konfigurera säkerhetskopiering för en dynamisk speglad volym
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. StorSimple Snapshot Manager-fönster visas. 
2. I den **omfång** fönstret högerklickar du på den **volymer** noden och väljer **Genomsök igen volymer**. När genomsökningen är klar visas en lista över volymer ska visas i den **resultat** fönstret. Den dynamiska speglade volymen har listats som en enskild volym. 
3. I den **resultat** fönstret högerklickar du på den dynamiska speglade volymen och klicka sedan på **Create volym Group**. 
4. I den **Create volym Group** dialogrutan Skriv ett namn för gruppen volym, tilldela den dynamiska speglade volymen till den här gruppen och klicka sedan på **OK**. 
5. I den **omfång** fönstret expanderar den **Volymgrupper** noden. Den nya volym gruppen bör visas under den **Volymgrupper** noden. 
6. Högerklicka på namnet på volymen. 
   
   * Starta en säkerhetskopiering för interaktiva (på begäran), klicka på **ta säkerhetskopiering**. 
   * Om du vill schemalägga en automatisk säkerhetskopiering, klickar du på **skapa Säkerhetskopieringsprincipen**. På den **Allmänt** markerar du den volym-gruppen i listan. På den **schema** anger du information om Jobbschema. När du är klar klickar du på **OK**. 
7. Du kan övervaka säkerhetskopieringen när den körs. I den **omfång** fönstret expanderar den **jobb** noden och klicka sedan på **kör**, Jobbinformationen visas i den **resultat** fönstret. När säkerhetskopieringen är klar information överförs till den **senaste 24** timmar jobb-listan. 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att skapa och hantera volymgrupper](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
