---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 3888242f0379cc97bbe511e49a31a0f7eb8c5372
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166160"
---
<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> När du ändrar StorSimple Adapter för SharePoint RBS konfigurationen måste du vara inloggad med ett konto som tillhör gruppen Domänadministratörer. Dessutom måste du komma åt konfigurationssidan från en webbläsare som körs på samma värddator som Central Administration.
> 
> 

#### <a name="to-configure-rbs"></a>Konfigurera RBS
1. Öppna sidan Central Administration av SharePoint och bläddra till **systeminställningar**. 
2. I den **Azure StorSimple** klickar du på **konfigurera StorSimple-Adapter**.
   
    ![Konfigurera StorSimple-Adapter](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. På den **konfigurera StorSimple-Adapter** sidan:
   
   1. Se till att den **Aktivera redigering sökväg** kryssrutan är markerad.
   2. I textrutan skriver du Universal Naming Convention (UNC)-sökvägen till BLOB store.
      
      > [!NOTE]
      > BLOB store volymen måste finnas på en iSCSI-volymen som konfigurerats på StorSimple-enheten.

   3. Klicka på den **aktivera** knappen under varje innehåll som du vill konfigurera för Fjärrlagring.
      
      > [!NOTE]
      > BLOB store måste vara delad och kan nås av alla frontend (WFE) webbservrar och det användarkonto som är konfigurerad för SharePoint-servergruppen måste ha åtkomst till resursen.
      
      ![Aktivera RBS-provider](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      När du aktiverar eller inaktiverar RBS, kan du även se följande meddelande.
      
      ![Konfigurera StorSimple Adapter aktivera inaktivera](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klicka på den **uppdatering** knappen för att tillämpa konfigurationen. När du klickar på den **uppdatering** knappen Konfigurationsstatus RBS kommer att uppdateras på alla WFE-servrar och hela servergruppen kommer att RBS-aktiverade. Följande meddelande visas.
      
      ![Adapter configuration meddelande](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Om du konfigurerar RBS för en SharePoint-servergrupp med ett stort antal databaser (större än 200), kan sidan Central Administration av SharePoint web timeout. Om det inträffar kan du uppdatera sidan. Detta påverkar inte konfigurationsprocessen.

4. Verifiera konfigurationen:
   
   1. Logga in på Central Administration av SharePoint-webbplats och bläddra till den **konfigurera StorSimple-Adapter** sidan.
   2. Kontrollera konfigurationsinformation för att se till att de matchar de inställningar som du har angett. 
5. Kontrollera att RBS fungerar korrekt:
   
   1. Överför ett dokument i SharePoint. 
   2. Bläddra till UNC-sökvägen som du har konfigurerat. Se till att katalogstrukturen RBS har skapats och att den innehåller överförda objektet.
6. (Valfritt) Du kan använda Microsoft RBS `Migrate()` PowerShell-cmdlet som ingår i SharePoint för att migrera befintliga BLOB-innehåll till StorSimple-enheten. Mer information finns i [migrerar du innehåll till eller från RBS i SharePoint 2013] [ 6] eller [migrerar du innehåll till eller från RBS (SharePoint Foundation 2010)] [7].
7. (Valfritt) På test-installationer kan du kontrollera att Blobarna har flyttats från innehållsdatabasen på följande sätt: 
   
   1. Starta SQL Management Studio.
   2. Kör frågan ListBlobsInDB_2010.sql eller ListBlobsInDB_2013.sql på följande sätt.
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Om RBS har konfigurerats korrekt, bör ett NULL-värde visas i kolumnen SizeOfContentInDB för alla objekt som har laddats upp och har externalized med RBS.
8. (Valfritt) När du konfigurerar RBS och flytta alla BLOB-innehåll till StorSimple-enheten, kan du flytta innehållsdatabasen till enheten. Om du väljer att flytta innehållsdatabasen, rekommenderar vi att du konfigurerar lagringen innehållsdatabas på enheten som en primär volym. Använd etablerade sedan SQL Server bästa praxis för att migrera innehållsdatabasen till StorSimple-enheten. 
   
   > [!NOTE]
   > Flytta innehållsdatabasen till enheten har endast stöd för StorSimple 8000-serien (det inte stöds för 5000 och 7000-serien).
   
   Om du sparar BLOB-objekt och innehållsdatabasen i separata volymer på StorSimple-enheten, rekommenderar vi att du konfigurerar dem i samma volymbehållaren. Detta säkerställer att de kommer att säkerhetskopieras tillsammans.
   
   > [!WARNING]
   > Om du inte har aktiverat RBS rekommenderar vi inte flytta innehållsdatabasen till StorSimple-enheten. Det här är en otestade konfiguration.
   
9. Gå till nästa steg: [konfigurera skräpinsamling](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
