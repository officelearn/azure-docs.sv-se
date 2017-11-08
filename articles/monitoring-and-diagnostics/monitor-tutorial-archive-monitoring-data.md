---
title: "Arkivera Azure övervakningsdata | Microsoft Docs"
description: "Arkivera loggen och det mått data som genereras i Azure till ett lagringskonto."
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: f19cf8fddd9ffcf08b8ce18db070a7482ce012df
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="archive-azure-monitoring-data"></a>Arkivera Azure övervakningsdata

Flera lager i Azure-miljön ge logg- och mått som kan arkiveras till ett Azure Storage-konto. Du kanske vill göra detta för att bevara en historik över övervakningsdata med tiden i en kostnadseffektiv och icke-sökbart store efter att data har gått dess Bevarandeperiod i Log Analytics eller Azure-Monitor. Den här självstudiekursen steg genom processen att konfigurera Azure-miljön för att arkivera data till ett lagringskonto.

> [!div class="checklist"]
> * Skapa ett lagringskonto för att lagra övervakningsdata
> * Väg prenumerationen loggar till den 
> * Väg resursdata till den 
> * Väg virtuella datorn (gäst-OS) data till den 
> * Visa övervakningsdata i den 
> * Rensa dina resurser 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Du måste först skapa ett lagringskonto som övervakningsdata arkiveras. Gör [följer du stegen här](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Väg prenumerationen loggar till lagringskontot

Du är nu redo att börja ställa in Azure-miljön för att skicka övervakningsdata till ett lagringskonto. Första konfigurera vi prenumerationsnivån data (i Azure-aktivitetsloggen) dirigeras till lagringskontot. Den [ **Azure-aktivitetsloggen** ](monitoring-overview-activity-logs.md) innehåller en historik över prenumerationsnivån händelser i Azure. Du kan bläddra i Azure portal för att fastställa *som* skapas, uppdateras eller tas bort *vad* resurser och *när* de gjorde den.

1. Klicka på den **övervakaren** knapp hittades i listan vänstra navigeringsfönstret och sedan på **aktivitetsloggen**.

   ![Aktiviteten loggen avsnitt](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. I avsnittet aktivitetsloggen som visas, klickar du på den **exportera** knappen.

3. I den **Export aktivitetsloggen** avsnitt som visas, markera kryssrutan för **exportera till ett lagringskonto** och klicka på **Välj ett lagringskonto.**

   ![Aktivitetsloggexporter](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. I avsnittet som visas, använder den **lagringskonto** listrutan och välj namnet på lagringskontot som du skapade i föregående **skapa ett lagringskonto** steg och klicka sedan på **OK**.

   ![Välj ett lagringskonto](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Ange den **bevarande (dagar)** skjutreglaget till 30. Den här reglaget ställer ett antal dagar att behålla övervakningsdata i storage-konto. Azure-Monitor tas automatiskt bort data som är äldre än antalet dagar angivna. En kvarhållning av noll dagar lagrar data på obestämd tid.

6. Klicka på **spara** och Stäng det här avsnittet.

Övervakningsdata från din prenumeration nu flödar till lagringskontot.

## <a name="route-resource-data-to-the-storage-account"></a>Väg resursdata till storage-konto

Nu vi konfigurera resursnivå data (resurs mått och diagnostikloggar) dirigeras till lagringskontot genom att ställa in **diagnostiska resursinställningar**.

1. Klicka på den **övervakaren** knapp hittades i listan vänstra navigeringsfönstret och sedan på **diagnostikinställningar**. Här kan du visa en lista över alla resurser i din prenumeration som producerar övervakningsdata via Azure-Monitor. Om du inte har några resurser i den här listan kan du [skapa en logikapp](../logic-apps/logic-apps-create-a-logic-app.md) innan du fortsätter så att du har en resurs som du kan konfigurera en diagnostikinställningen på.

2. Klicka på en resurs i listan och klicka sedan på **aktivera diagnostiken**.
   
   ![Aktivera diagnostik](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Om det redan finns en inställning som konfigurerats i stället ser du de befintliga inställningarna och en knapp för att **lägga till diagnostikinställningen**. Klicka här.

   En diagnostikinställningen för resursen är en definition av *vad* övervakningsdata ska vidarebefordras från en viss resurs och *där* övervakningsdata ska gå att.

3. Ge dina inställningar i avsnittet visas en **namn** och markera kryssrutan för **arkivet till ett lagringskonto**.

   ![Diagnostikinställningar för avsnittet](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Klicka på den **konfigurera** knappen **arkivet till ett lagringskonto** och välj lagringskontot som du skapade i föregående avsnitt. Klicka på **OK**.

   ![Diagnostikinställningar för storage-konto](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Markera alla kryssrutorna under **loggen** och **mått**. Beroende på resurstypen du kan inte ha något av följande alternativ. De här kryssrutorna styra vilka kategorier av loggen och det mått data som är tillgängliga för att resurstypen skickas till mål som du har valt, i det här fallet ett lagringskonto.

   ![Diagnostikinställningar för kategorier](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. Ange den **bevarande (dagar)** skjutreglaget till 30. Den här reglaget ställer ett antal dagar att behålla övervakningsdata i storage-konto. Azure-Monitor tas automatiskt bort data som är äldre än antalet dagar angivna. En kvarhållning av noll dagar lagrar data på obestämd tid.

7. Klicka på **Spara**.

Övervakningsdata från din resurs nu flödar till lagringskontot.

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Vidarebefordra virtuella datorn (gäst-OS) data till lagringskontot

1. Om du inte redan har en virtuell dator i din prenumeration [skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md).

2. I listan vänstra navigeringsfönstret i portalen klickar du på **virtuella datorer**.

3. Klicka på den virtuella datorn som du skapade i listan över virtuella datorer.

4. I avsnittet som visas, klickar du på **diagnostikinställningar** i navigeringen till vänster. Det här avsnittet kan du konfigurera övervakning out-of-box-tillägget från Azure-Monitor på den virtuella datorn och vidarebefordra data som produceras av Windows eller Linux till ett lagringskonto.

   ![Navigera till diagnostikinställningar](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Klicka på **aktivera övervakning av gästnivå** i avsnittet som visas.

   ![Aktivera diagnostikinställningar](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. När du diagnostikinställningen korrekt har sparat den **översikt** fliken visas en lista över de data som samlas in och där den lagras. Klicka på den **prestandaräknare** avsnittet för att granska uppsättningen Windows prestanda prestandaräknare som samlas in.

   ![Inställningar för prestandaräknare](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. Klicka på den **loggar** fliken och markera kryssrutorna för **Information** nivå loggar in programmet och loggar systemet.

   ![Inställningarna för webbprogramloggar](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Klicka på den **Agent** fliken och under **lagringskonto** klickar du på namnet på det lagringskonto som visas.

   ![Uppdatera lagringskontot](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. I avsnittet som visas väljer du det lagringskonto som du skapade i föregående **skapa ett lagringskonto** steg.

10. Klicka på **Spara**.

Övervakningsdata från de virtuella datorerna nu flödar till lagringskontot.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Visa övervakningsdata i storage-konto

Om du har följt de föregående stegen har börjat data flödar till ditt lagringskonto.

1. För vissa datatyper av, till exempel aktivitetsloggen, måste det finnas en aktivitet som genererar en händelse i lagringskontot. Så skapar du aktiviteten i aktivitetsloggen [instruktionerna](./monitor-quick-audit-notify-action-in-subscription.md). Du kan behöva vänta upp till fem minuter innan händelsen visas i lagringskontot.

2. I portalen, går du till den **Lagringskonton** avsnitt genom att söka efter i det vänstra navigeringsfältet.

3. Identifiera storage-konto som du skapade i föregående avsnitt och klicka på den.

4. Klicka på **Blobbar**, sedan på behållaren med etiketten **insikter operativa loggar** och slutligen på behållaren **namn = standard**. Detta är en behållare som innehåller din aktivitetsloggen. Övervakningsdata bryts i behållare efter resurs-ID (bara prenumerations-ID för aktivitetsloggen) och sedan datum och tid. Fullständig formatet för dessa BLOB är:

   insikter-operativa-loggar/name = standard/resourceId = / PRENUMERATIONER / {prenumerations-ID} / y = {numeriskt årtal} / m = {tvåsiffrig numeriska month} / d = {tvåsiffrig kalenderdag} / tim = {tvåsiffrig 24-timmarsklocka hour}/m=00/PT1H.json

5. Navigera till filen PT1H.json genom att klicka på behållarna för resurs-ID, datum och tid. Klicka på filen PT1H.json och på **hämta**. Varje PT1H.json blobb innehåller en JSON-blob av händelser som inträffade inom en timme som anges i blob-URL (till exempel h = 12). Under den aktuella timman läggs händelser till filen PT1H.json när de inträffar. Minuten (m = 00) är alltid 00, eftersom logghändelser delas upp i enskilda blobbar per timme.

   Du kan nu visa JSON-händelsen som lagrats i lagringskontot. Formatet för blobbarna är för resursen diagnostiska loggar:

   insikter - loggar-{kategori loggnamn} / resourceId = / {resurs-ID} / y = {numeriskt årtal} / m = {tvåsiffrig numeriska month} / d = {tvåsiffrig kalenderdag} / tim = {tvåsiffrig 24-timmarsklocka hour}/m=00/PT1H.json

6. Gästoperativsystem övervakningsdata lagras i tabeller. Gå tillbaka till lagringskontot hem och klickar på **tabeller**. Det finns tabeller för mått, prestandaräknare och händelseloggar.

Du har nu konfigurerat övervakningsdata arkiveras till ett lagringskonto.

## <a name="clean-up-resources"></a>Rensa resurser

1. Gå tillbaka till den **exportera aktivitetsloggen** avsnittet från den föregående **vidarebefordra prenumerationen loggar till lagringskontot** steg och på **återställa**.

2. Navigera till den **diagnostikinställningar** klickar du på den resurs som du skapat en diagnostikinställningen i den föregående **vidarebefordra resursdata till lagringskontot** steg och inställning du skapat, klickar du på den **redigera inställningen** och klicka sedan på **ta bort**.

3. Navigera till den **diagnostikinställningar** avsnitt på den virtuella datorn som du konfigurerade i föregående **dirigerar virtuella datorn (gäst-OS) data till lagringskontot** steg, och under den  **Agenten** klickar du på **ta bort** (under den **ta bort Azure-diagnostik agent** avsnitt).

4. Navigera till storage-konto som du skapade i föregående **skapa ett lagringskonto** steg och på **ta bort lagringskontot**. Skriv namnet på lagringskontot och klicka sedan på **ta bort**.

5. Om du har skapat en virtuell dator eller Logikapp i föregående steg, tar du bort dem också.

## <a name="next-steps"></a>Nästa steg

I kursen får du har lärt dig hur du ställer in övervakningsdata från Azure-miljön (prenumeration resurs och Gäst-OS) arkiveras till ett lagringskonto. 


> [!div class="checklist"]
> * Skapa ett lagringskonto för att lagra övervakningsdata
> * Väg prenumerationen loggar till den 
> * Väg resursdata till den 
> * Väg virtuella datorn (gäst-OS) data till den 
> * Visa övervakningsdata i den 
> * Rensa dina resurser 

Om du vill få ut mer av dina data och härleda insikter som ytterligare, även skicka data till logganalys.

> [!div class="nextstepaction"]
> [Kom igång med logganalys](../log-analytics/log-analytics-get-started.md)
