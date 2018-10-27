---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0d2fde4ce80e09db19f65134697ba0ba379e1824
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164402"
---
<!--author=SharS last changed: 9/17/15-->

I den här proceduren kommer du att:

1. [Förbereda för att köra programfilen Underhållaren](#to-prepare-to-run-the-maintainer) .
2. [Förbereda innehållsdatabasen och Papperskorgen för omedelbar borttagning av överblivna Blobbar](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs).
3. [Kör Maintainer.exe](#to-run-the-maintainer).
4. [Återställa innehållsdatabasen och inställningar för Papperskorgen](#to-revert-the-content-database-and-recycle-bin-settings).

#### <a name="to-prepare-to-run-the-maintainer"></a>Förbereda att köra Underhållaren
1. Öppna hanteringsgränssnittet för SharePoint 2013 som administratör på den webb-frontendserver.
2. Navigera till mappen *starta enheten*: \Program\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. Byt namn på **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** till **web.config**.
4. Använd `aspnet_regiis -pdf connectionStrings` att dekryptera filen web.config.
5. I dekrypterade web.config-filen under den `connectionStrings` nod, lägga till anslutningssträngen för din SQL server-instans och innehållsdatabasens namn. Se följande exempel.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Använd `aspnet_regiis –pef connectionStrings` att kryptera filen web.config. 
7. Ändra web.config till Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>För att förbereda innehållet överblivna databasen och Papperskorgen för att omedelbart ta bort Blobar
1. Kör följande uppdateringsfrågor för innehåll måldatabasen i SQL Management Studio på SQL-Server: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. På den frontend-webbservern under **Central Administration**, redigera den **Web Application allmänna inställningar för** för önskad innehållsdatabasen att tillfälligt inaktivera Papperskorgen. Den här åtgärden kommer även att tömma Papperskorgen för relaterade webbplatssamlingar. Gör detta genom att klicka på **Central Administration** -> **programhantering** -> **webbprogram (hantera webbprogram)**  ->  **SharePoint – 80** -> **Allmänt programinställningar**. Ange den **Status för Papperskorgen** till **OFF**.
   
    ![Web Application allmänna inställningar](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>Att köra Underhållaren
* På den frontend-webbservern SharePoint 2013 Management Shell och kör Underhållaren enligt följande:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Endast den `GarbageCollection` åtgärden stöds för StorSimple just nu. Observera också att de parametrar som utfärdats för Microsoft.Data.SqlRemoteBlobs.Maintainer.exe är skiftlägeskänsliga. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>Att återställa innehållsdatabasen och inställningar för Papperskorgen
1. Kör följande uppdateringsfrågor för innehåll måldatabasen i SQL Management Studio på SQL-Server:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. På den frontend-webbservern i **Central Administration**, redigera den **Web Application allmänna inställningar för** för önskad innehållsdatabasen återaktivera Papperskorgen. Gör detta genom att klicka på **Central Administration** -> **programhantering** -> **webbprogram (hantera webbprogram)**  ->  **SharePoint – 80** -> **Allmänt programinställningar**. Ange återvinning Bin Status till **på**.

