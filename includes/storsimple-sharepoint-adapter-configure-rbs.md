---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: f84fe995e65d2b67aaaf4ff9acc4a6a44ce607dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187383"
---
> [!NOTE]
> När du gör ändringar i StorSimple-kortet för SharePoint RBS-konfiguration måste du vara inloggad med ett användarkonto som tillhör gruppen Domänadministratörer. Dessutom måste du komma åt konfigurationssidan från en webbläsare som körs på samma värd som Central administration.
> 
> 

#### <a name="to-configure-rbs"></a>Så här konfigurerar du RBS
1. Öppna sidan Central administration i SharePoint och bläddra till **Systeminställningar**. 
2. Klicka på **Konfigurera StorSimple-kort**i avsnittet **Azure StorSimple** .
   
    ![Konfigurera StorSimple-kortet](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. På sidan **Konfigurera StorSimple-kort:**
   
   1. Kontrollera att kryssrutan **Aktivera redigeringssökväg** är markerad.
   2. Skriv unc-sökvägen (Universal Naming Convention) i BLOB-arkivet i textrutan.
      
      > [!NOTE]
      > BLOB-arkivets volym måste vara värd för en iSCSI-volym som konfigurerats på StorSimple-enheten.

   3. Klicka på knappen **Aktivera** under var och en av de innehållsdatabaser som du vill konfigurera för fjärrlagring.
      
      > [!NOTE]
      > BLOB-arkivet måste delas och nås av alla WFE-servrar (Web Front-End) och användarkontot som är konfigurerat för SharePoint-servergruppen måste ha åtkomst till resursen.
      
      ![Aktivera RBS-providern](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      När du aktiverar eller inaktiverar RBS visas även följande meddelande.
      
      ![Konfigurera StorSimple-kort aktivera inaktivera](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klicka på knappen **Uppdatera** om du vill använda konfigurationen. När du klickar på knappen **Uppdatera** uppdateras RBS-konfigurationsstatusen på alla WFE-servrar och hela servergruppen kommer att vara RBS-aktiverad. Följande meddelande visas.
      
      ![Meddelande om konfiguration av kort](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Om du konfigurerar RBS för en SharePoint-servergrupp med ett mycket stort antal databaser (större än 200) kan sharepoint-sidan för central administration time out. Om det inträffar uppdaterar du sidan. Detta påverkar inte konfigurationsprocessen.

4. Verifiera konfigurationen:
   
   1. Logga in på SharePoint Central Administration-webbplatsen och bläddra till sidan **Konfigurera StorSimple-kort.**
   2. Kontrollera konfigurationsinformationen för att se till att de matchar de inställningar som du angav. 
5. Kontrollera att RBS fungerar korrekt:
   
   1. Ladda upp ett dokument till SharePoint. 
   2. Bläddra till UNC-sökvägen som du har konfigurerat. Kontrollera att RBS-katalogstrukturen har skapats och att den innehåller det uppladdade objektet.
6. (Valfritt) Du kan använda microsoft `Migrate()` RBS PowerShell-cmdleten som medföljer SharePoint för att migrera befintligt BLOB-innehåll till StorSimple-enheten. Mer information finns [i Migrera innehåll till eller från RBS i SharePoint 2013][6] eller Migrera innehåll till eller från [RBS (SharePoint Foundation 2010)][7].
7. (Valfritt) Vid testinstallationer kan du kontrollera att BLOB:erna har flyttats ut ur innehållsdatabasen på följande sätt: 
   
   1. Starta SQL Management Studio.
   2. Kör frågan ListBlobsInDB_2010.sql eller ListBlobsInDB_2013.sql enligt följande.
      
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
      
      Om RBS har konfigurerats korrekt ska ett NULL-värde visas i kolumnen SizeOfContentInDB för alla objekt som har överförts och har externaliserats med RBS.
8. (Valfritt) När du har konfigurerat RBS och flyttat allt BLOB-innehåll till StorSimple-enheten kan du flytta innehållsdatabasen till enheten. Om du väljer att flytta innehållsdatabasen rekommenderar vi att du konfigurerar innehållsdatabaslagringen på enheten som en primär volym. Använd sedan etablerade metodtips för SQL Server för att migrera innehållsdatabasen till StorSimple-enheten. 
   
   > [!NOTE]
   > Det går bara att flytta innehållsdatabasen till enheten för StorSimple 8000-serien (den stöds inte för 5000- eller 7000-serien).
   
   Om du lagrar BLOBs och innehållsdatabasen i separata volymer på StorSimple-enheten rekommenderar vi att du konfigurerar dem i samma volymbehållare. Detta säkerställer att de kommer att backas upp tillsammans.
   
   > [!WARNING]
   > Om du inte har aktiverat RBS rekommenderar vi inte att du flyttar innehållsdatabasen till StorSimple-enheten. Detta är en oprövad konfiguration.
   
9. Gå till nästa steg: [Konfigurera skräpinsamling](#configure-garbage-collection).

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
