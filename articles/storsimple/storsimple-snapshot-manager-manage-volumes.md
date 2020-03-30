---
title: StorSimple Snapshot Manager och volymer | Microsoft-dokument
description: Beskriver hur du använder MMC-snapin-modulen StorSimple Snapshot Manager för att visa och hantera volymer och konfigurera säkerhetskopior.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: twooley
ms.openlocfilehash: f09d4dd46a50f1794e51342a939b8919c5c523ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254656"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Använda StorSimple Snapshot Manager för att visa och hantera volymer
## <a name="overview"></a>Översikt
Du kan använda noden StorSimple Snapshot Manager **Volumes** (i **scopefönstret)** för att välja volymer och visa information om dem. Volymerna presenteras som enheter som motsvarar de volymer som monterats av värden. Noden **Volymer** visar lokala volymer och volymtyper som stöds av StorSimple, inklusive volymer som identifieras med hjälp av iSCSI och en enhet. 

Mer information om volymer som stöds finns i [Stöd för flera volymtyper](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Volymlista i fönstret Resultat](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Med noden **Volymer** kan du också söka igenom eller ta bort volymer igen när StorSimple Snapshot Manager upptäcker dem. 

I den här självstudien beskrivs hur du kan montera, initiera och formatera volymer och sedan använda StorSimple Snapshot Manager för att:

* Visa information om volymer 
* Ta bort volymer
* Omsökningsvolymer 
* Konfigurera en enkel volym och säkerhetskopiera den
* Konfigurera en dynamisk speglad volym och säkerhetskopiera den

> [!NOTE]
> Alla **åtgärder för volymnod** är också tillgängliga i fönstret **Åtgärder.**
> 
> 

## <a name="mount-volumes"></a>Montera volymer
Använd följande procedur för att montera, initiera och formatera StorSimple-volymer. Den här proceduren använder Diskhantering, ett systemverktyg för hantering av hårddiskar och motsvarande volymer eller partitioner. Mer information om Diskhantering finns på [Diskhantering](https://technet.microsoft.com/library/cc770943.aspx) på Webbplatsen Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Så här monterar du volymer
1. Starta Microsoft iSCSI-initieraren på värddatorn.
2. Ange en av gränssnitts-IP-adresserna som målportal eller identifierings-IP-adress och anslut till enheten. När enheten är ansluten är volymerna tillgängliga för ditt Windows-system. Mer information om hur du använder Microsoft iSCSI-initieraren finns i avsnittet "Ansluta till en iSCSI-målenhet" i [Installera och konfigurera Microsoft iSCSI Initiator][1].
3. Använd något av följande alternativ för att starta Diskhantering:
   
   * Skriv Diskmgmt.msc i rutan **Kör.**
   * Starta Serverhanteraren, expandera **noden Lagring** och välj sedan **Diskhantering**.
   * Starta **administrationsverktyg,** expandera noden **Datorhantering** och välj sedan **Diskhantering**. 
     
     > [!NOTE]
     > Du måste använda administratörsbehörighet för att köra Diskhantering.
     > 
     > 
4. Ta volymen(erna) online:
   
   1. Högerklicka på valfri volym som är markerad **offline**i Diskhantering .
   2. Klicka på **Återaktivera disk**. Disken ska markeras **online** när disken har återaktiverats.
5. Initiera volymen/volymerna:
   
   1. Högerklicka på de identifierade volymerna.
   2. Välj **Initiera disk på**menyn .
   3. Markera de diskar som du vill initiera i dialogrutan **Initiera disk** och klicka sedan på **OK**.
6. Formatera enkla volymer:
   
   1. Högerklicka på en volym som du vill formatera.
   2. Välj **Ny enkel volym**på menyn .
   3. Använd guiden Ny enkel volym för att formatera volymen:
      
      * Ange volymstorlek.
      * Ange en enhetsbeteckning.
      * Välj NTFS-filsystemet.
      * Ange en storlek på allokeringsenheterna på 64 KB.
      * Utför en snabbformatering.
7. Formatera volymer med flera partitioner. Instruktioner finns i avsnittet "Partitioner och volymer" i [Implementera diskhantering](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Visa information om dina volymer
Använd följande procedur för att visa information om lokala och Azure StorSimple-volymer.

#### <a name="to-view-volume-information"></a>Så här visar du volyminformation
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager. 
2. Klicka på noden **Volymer** i **fönstret Omfattning.** En lista över lokala och monterade volymer, inklusive alla Azure StorSimple-volymer, visas i **fönstret Resultat.** Kolumnerna i **resultatfönstret** kan konfigureras. (Högerklicka på **noden Volymer,** välj **Visa**och välj sedan **Lägg till/ta bort kolumner**.)
   
    ![Konfigurera kolumnerna](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Kolumnen Resultat | Beskrivning |
   |:--- |:--- |
   |  Namn |Kolumnen **Namn** innehåller den enhetsbeteckning som tilldelats varje upptäckt volym. |
   |  Enhet |Kolumnen **Enhet** innehåller IP-adressen för den enhet som är ansluten till värddatorn. |
   |  Namn på enhetsvolym |Kolumnen **Enhetsvolymnamn** innehåller namnet på den enhetsvolym som den valda volymen tillhör. Det här är volymnamnet som definierats i Azure-portalen för den specifika volymen. |
   |  Komma åt sökvägar |I kolumnen **Åtkomstsökvägar** visas åtkomstsökvägen till volymen. Det här är enhetsbeteckningen eller monteringspunkten där volymen är tillgänglig på värddatorn. |

## <a name="delete-a-volume"></a>Ta bort en volym
Använd följande procedur för att ta bort en volym från StorSimple Snapshot Manager.

> [!NOTE]
> Du kan inte ta bort en volym om den ingår i en volymgrupp. (Alternativet ta bort är inte tillgängligt för volymer som ingår i en volymgrupp.) Du måste ta bort hela volymgruppen för att ta bort volymen.

#### <a name="to-delete-a-volume"></a>Så här tar du bort en volym
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Klicka på noden **Volymer** i **fönstret Omfattning.** 
3. Högerklicka på den volym som du vill ta bort i **fönstret Resultat.**
4. Klicka på **Ta bort**på menyn . 
   
    ![Ta bort en volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Dialogrutan **Ta bort volym** visas. Skriv **Bekräfta** i textrutan och klicka sedan på **OK**.
6. Som standard säkerhetskopierar StorSimple Snapshot Manager en volym innan den tas bort. Denna försiktighetsåtgärd kan skydda dig från dataförlust om borttagningen var oavsiktlig. StorSimple Snapshot Manager visar ett **automatiskt förloppsmeddelande för ögonblicksbilder** medan den säkerhetskopierar volymen. 
   
    ![Automatiskt meddelande om ögonblicksbilder](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Omsökningsvolymer
Använd följande procedur för att söka igenom volymerna som är anslutna till StorSimple Snapshot Manager igen.

#### <a name="to-rescan-the-volumes"></a>Så här skannar du om volymerna
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på **Volymer**i **fönstret Omfattning** och klicka sedan på **Omsökningsvolymer.**
   
    ![Omsökningsvolymer](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Den här proceduren synkroniserar volymlistan med StorSimple Snapshot Manager. Alla ändringar, till exempel nya volymer eller borttagna volymer, kommer att återspeglas i resultatet.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurera och säkerhetskopiera en enkel volym
Använd följande procedur för att konfigurera en säkerhetskopia av en grundläggande volym och starta sedan en säkerhetskopia omedelbart eller skapa en princip för schemalagda säkerhetskopior.

### <a name="prerequisites"></a>Krav
Innan du börjar:

* Kontrollera att StorSimple-enheten och värddatorn är korrekt konfigurerade. Mer information finns i [Distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information finns i [Distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Så här konfigurerar du säkerhetskopiering av en enkel volym
1. Skapa en enkel volym på StorSimple-enheten.
2. Montera, initiera och formatera volymen enligt beskrivningen i [Mount-volymer](#mount-volumes). 
3. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. Fönstret StorSimple Snapshot Manager visas. 
4. Högerklicka på noden **Volymer** i **fönstret Omfattning** och välj sedan **Omsökningsvolymer**. När genomsökningen är klar ska en lista med volymer visas i **fönstret Resultat.** 
5. Högerklicka på volymen i fönstret **Resultat** och välj sedan **Skapa volymgrupp**. 
   
    ![Skapa volymgrupp](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. Skriv ett namn på volymgruppen i dialogrutan **Skapa volymgrupp,** tilldela volymer till den och klicka sedan på **OK**.
7. Expandera noden **Volymgrupper** i **fönstret Omfattning.** Den nya volymgruppen ska visas under noden **Volymgrupper.** 
8. Högerklicka på volymgruppsnamnet.
   
   * Om du vill starta ett interaktivt säkerhetskopieringsjobb (på begäran) klickar du på **Ta med säkerhetskopiering**. 
   * Om du vill schemalägga en automatisk säkerhetskopiering klickar du på **Skapa princip för säkerhetskopiering**. Välj en volymgrupp i listan på sidan **Allmänt.** Ange schemainformationen på sidan **Schema.** Klicka på **OK** när du är klar. 
9. Om du vill bekräfta att säkerhetskopieringsjobbet har startat expanderar du noden **Jobb** i **fönstret Scope** och klickar sedan på **noden Kör.** Listan över jobb som körs för närvarande visas i **fönstret Resultat.** 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurera och säkerhetskopiera en dynamisk speglad volym
Gör följande för att konfigurera säkerhetskopiering av en dynamisk speglad volym:

* Steg 1: Använd Diskhantering för att skapa en dynamisk speglad volym. 
* Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhetskopiering.

### <a name="prerequisites"></a>Krav
Innan du börjar:

* Kontrollera att StorSimple-enheten och värddatorn är korrekt konfigurerade. Mer information finns i [Distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).
* Installera och konfigurera StorSimple Snapshot Manager. Mer information finns i [Distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Konfigurera två volymer på StorSimple-enheten. (I exemplen är de tillgängliga volymerna **Disk 1** och **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Steg 1: Använda Diskhantering för att skapa en dynamisk speglad volym
Diskhantering är ett systemverktyg för hantering av hårddiskar och de volymer eller partitioner som de innehåller. Mer information om Diskhantering finns på [Diskhantering](https://technet.microsoft.com/library/cc770943.aspx) på Webbplatsen Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Så här skapar du en dynamisk speglad volym
1. Använd något av följande alternativ för att starta Diskhantering: 
   
   * Öppna rutan **Kör,** skriv **Diskmgmt.msc**och tryck på Retur.
   * Starta Serverhanteraren, expandera **noden Lagring** och välj sedan **Diskhantering**. 
   * Starta **administrationsverktyg,** expandera noden **Datorhantering** och välj sedan **Diskhantering**. 
2. Kontrollera att du har två volymer tillgängliga på StorSimple-enheten. (I exemplet är de tillgängliga volymerna **Disk 1** och **Disk 2**.) 
3. Högerklicka på **Disk 1** i fönstret Diskhantering i den högra kolumnen i det nedre fönstret och välj **Ny speglad volym**. 
   
    ![Ny speglad volym](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Klicka på **Nästa**på guidesidan **Ny speglad volym** .
5. På sidan **Välj diskar** väljer du **Disk 2** i fönstret **Markerad,** klickar på **Lägg till**och klickar sedan på **Nästa**. 
6. På sidan **Tilldela enhetsbeteckning eller sökväg** godkänner du standardinställningarna och klickar sedan på **Nästa**. 
7. Välj **64K**i rutan **Fördelningsenhetsstorlek** på sidan **Formatera volym.** Markera kryssrutan **Utför ett snabbformat** och klicka sedan på **Nästa**. 
8. På sidan **Slutför den nya speglade volymen** granskar du inställningarna och klickar sedan på **Slutför**. 
9. Ett meddelande visas som anger att den grundläggande disken konverteras till en dynamisk disk. Klicka på **Ja**.
   
    ![Meddelande om konvertering av dynamisk disk](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. Kontrollera att Disk 1 och Disk 2 visas som dynamiska speglade volymer i Diskhantering. (**Dynamisk** ska visas i statuskolumnen och kapacitetsstapelfärgen ska ändras till röd, vilket indikerar en speglad volym.) 
    
    ![Diskhantering speglade dynamiska diskar](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Steg 2: Använd StorSimple Snapshot Manager för att konfigurera säkerhetskopiering
Använd följande procedur för att konfigurera en dynamisk speglad volym och starta sedan en säkerhetskopia omedelbart eller skapa en princip för schemalagda säkerhetskopior.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Så här konfigurerar du säkerhetskopiering av en dynamisk speglad volym
1. Klicka på ikonen StorSimple Snapshot Manager på skrivbordet. Fönstret StorSimple Snapshot Manager visas. 
2. Högerklicka på noden **Volymer** i **fönstret Omfattning** och välj **Omsökningsvolymer**. När genomsökningen är klar ska en lista med volymer visas i **fönstret Resultat.** Den dynamiska speglade volymen visas som en enda volym. 
3. Högerklicka på den dynamiska speglade volymen i **fönstret Resultat** och klicka sedan på **Skapa volymgrupp**. 
4. Skriv ett namn på volymgruppen i dialogrutan **Skapa volymgrupp,** tilldela den dynamiska speglade volymen till den här gruppen och klicka sedan på **OK**. 
5. Expandera noden **Volymgrupper** i **fönstret Omfattning.** Den nya volymgruppen ska visas under noden **Volymgrupper.** 
6. Högerklicka på volymgruppsnamnet. 
   
   * Om du vill starta ett interaktivt säkerhetskopieringsjobb (på begäran) klickar du på **Ta med säkerhetskopiering**. 
   * Om du vill schemalägga en automatisk säkerhetskopiering klickar du på **Skapa princip för säkerhetskopiering**. Välj volymgruppen i listan på sidan **Allmänt.** Ange schemainformationen på sidan **Schema.** Klicka på **OK** när du är klar. 
7. Du kan övervaka säkerhetskopieringsjobbet när det körs. Expandera noden **Jobb** i fönstret **Omfattning** och klicka sedan på **Kör**, Jobbinformationen visas i **fönstret Resultat.** När säkerhetskopieringsjobbet är klart överförs informationen till jobblistan **Senaste 24** timmar. 

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att skapa och hantera volymgrupper](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
