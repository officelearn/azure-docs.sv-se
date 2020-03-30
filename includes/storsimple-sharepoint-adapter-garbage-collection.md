---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 00d292b3ba2d1b6c7c425d4c9f89188e660ac80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73182291"
---
I den här proceduren kommer du att:

1. [Förbered körning av den körbara ansvariga ansvarige](#to-prepare-to-run-the-maintainer) .
2. [Förbered innehållsdatabasen och papperskorgen för omedelbar borttagning av överblivna BLOBs](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Kör Maintainer.exe](#to-run-the-maintainer).
4. [Återställ inställningarna för innehållsdatabas och Papperskorgen](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Så här förbereder du dig för att köra ansvarig
1. Öppna SharePoint 2013 Management Shell som administratör på webbservern.
2. Navigera till *mappstartenheten*:\Program\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. Byt namn på **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** till **web.config**.
4. Används `aspnet_regiis -pdf connectionStrings` för att dekryptera filen web.config.
5. Lägg till anslutningssträngen `connectionStrings` för SQL-serverinstansen och namnet på innehållsdatabasen under noden i filen dekrypterad web.config. Se följande exempel.
   
    `<add name="RBSMaintainerConnectionWSSContent" connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Används `aspnet_regiis –pef connectionStrings` för att kryptera om filen web.config. 
7. Byt namn web.config till Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>Så här förbereder du innehållsdatabasen och papperskorgen för att omedelbart ta bort överblivna BLOBs
1. I SQL Management Studio i SQL Management Studio kör du följande uppdateringsfrågor för målinnehållsdatabasen: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Redigera allmänna inställningar för webbprogram för önskad **innehållsdatabas** under **Central administration**på webbservern för att tillfälligt inaktivera Papperskorgen. Den här åtgärden tömmer även Papperskorgen för alla relaterade webbplatssamlingar. Det gör du genom att klicka på **Webbprogram** -> för central**administrationsprogram** -> **(Hantera webbprogram)** -> **SharePoint - 80** -> Allmänna**programinställningar**. Ange **att papperskorgens status** ska **vara AV**.
   
    ![Allmänna inställningar för webbprogram](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Så här kör du ansvarig
* På webbklämmensservern kör du ansvarigren i SharePoint 2013 Management Shell på följande sätt:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Endast `GarbageCollection` åtgärden stöds för StorSimple just nu. Observera också att de parametrar som utfärdas för Microsoft.Data.SqlRemoteBlobs.Maintainer.exe är skiftlägeskänsliga. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Så här återställer du inställningarna för innehållsdatabas och Papperskorgen
1. I SQL Management Studio i SQL Management Studio kör du följande uppdateringsfrågor för målinnehållsdatabasen:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. På webbklämmensservern **redigerar**du allmänna inställningar för **webbprogram** för önskad innehållsdatabas på webbservern för att återaktivera Papperskorgen. Det gör du genom att klicka på **Webbprogram** -> för central**administrationsprogram** -> **(Hantera webbprogram)** -> **SharePoint - 80** -> Allmänna**programinställningar**. Ange papperskorgens status till **PÅ**.

