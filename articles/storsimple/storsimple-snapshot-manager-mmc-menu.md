---
title: StorSimple Snapshot Manager MMC menyn Åtgärder | Microsoft Docs
description: Beskriver hur du använder standardåtgärder för Microsoft Management Console (MMC)-menyn i StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 48f439a566a8067e153aab4fb789937d2f91268d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875895"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Använda MMC-menyn åtgärder i StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager visas följande åtgärder visas på alla Åtgärdsmenyer och alla variationer av den **åtgärder** fönstret.

* Visa
* Nytt fönster härifrån 
* Uppdatera 
* Exportera lista 
* Hjälp 

Dessa åtgärder är en del av Microsoft Management Console (MMC) och är inte specifika för StorSimple Snapshot Manager. Den här självstudiekursen beskrivs hur du använder dem i StorSimple Snapshot Manager beskrivs dessa åtgärder.

## <a name="view"></a>Visa
Du kan använda den **visa** alternativet för att ändra den **resultat** vy och ändra fönstret konsolvyn. 

#### <a name="to-change-the-results-pane-view"></a>Ändra resultat vy
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** , högerklickar du på någon nod eller expandera noden och högerklickar du i den **resultat** rutan och klicka sedan på den **visa** alternativet. 
3. Att lägga till eller ta bort de kolumnerna som visas i den **resultat** rutan klickar du på **Lägg till/ta bort kolumner**. Den **Lägg till/ta bort kolumner** dialogrutan visas.
   
    ![Lägg till eller ta bort kolumner från resultatfönstret](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Fyll i formuläret på följande sätt:
   
   * Välj objekt från den **tillgänglig** kolumner och klicka på **Lägg till** lägga till dem i den **visas kolumner** lista. 
   * Klicka på objekten i den **visas kolumner** och på **ta bort** ta bort dem från listan. 
   * Välj ett objekt i den **visas** kolumner och klicka på **Flytta upp** eller **Flytta ned** att flytta objektet uppåt eller nedåt i listan. 
   * Klicka på **Återställ standardvärden** att återgå till standard **resultat** fönstret konfiguration. 
5. När du är klar med dina val klickar du på **OK**. 

#### <a name="to-change-the-console-window-view"></a>Så här ändrar du konsolvyn fönster
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret, högerklicka på en nod, klicka på **visa**, och klicka sedan på **anpassa**. Den **anpassa** dialogrutan visas.
   
    ![Anpassa konsolfönstret.](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Markera eller avmarkera kryssrutorna för att visa eller dölja element i konsolfönstret. När du är klar med dina val klickar du på **OK**.

## <a name="new-window-from-here"></a>Nytt fönster härifrån
Du kan använda den **nytt fönster härifrån** möjligheten att öppna ett nytt konsolfönster.

#### <a name="to-open-a-new-console-window"></a>Öppna ett nytt konsolfönster
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** , högerklicka på en nod, och klickar sedan på **nytt fönster härifrån**. 
   
    Ett nytt fönster visas med endast omfång som du valt. Till exempel om du högerklickar på den **principer för säkerhetskopiering** nod, ett nytt fönster visas bara den **principer för säkerhetskopiering** nod i den **omfång** rutan och en lista över definierade principer för säkerhetskopiering i den **resultat** fönstret. Se följande exempel.
   
    ![Nytt fönster härifrån](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Uppdatera
Du kan använda den **uppdatera** åtgärd för att uppdatera konsolfönstret.

#### <a name="to-update-the-console-window"></a>Uppdatera konsolfönstret.
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** , högerklickar du på någon nod eller expandera noden och högerklickar du i den **resultat** rutan och klicka sedan på **uppdatera**. 

## <a name="export-list"></a>Exportera lista
Du kan använda den **exportera listan** åtgärder för att spara en lista i en fil med kommaavgränsade värden (CSV). Du kan till exempel exportera listan med principer för säkerhetskopiering eller katalogen för säkerhetskopieringen. Du kan sedan importera CSV-filen till ett kalkylbladsprogram för analys.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Spara en lista i en fil med kommaavgränsade värden (CSV)
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** , högerklickar du på någon nod eller expandera noden och högerklickar du i den **resultat** rutan och klicka sedan på **exportera listan**. 
3. Den **exportera listan** dialogrutan visas. Fyll i formuläret på följande sätt: 
   
   1. I den **filnamn** Skriv ett namn för CSV-filen eller klicka på pilen för att välja från den nedrullningsbara listan.
   2. I den **filformat** rutan, klickar du på pilen och väljer en filtyp från den nedrullningsbara listan.
   3. Markera raderna för att spara endast valda objekt, och klicka sedan på den **Spara endast markerade rader** kryssrutan. Om du vill spara alla exporterade listor, avmarkera den **Spara endast markerade rader** kryssrutan.
   4. Klicka på **Spara**.
      
      ![Exportera listan som en CSV-fil](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Hjälp
Du kan använda den **hjälp** menyn för att visa tillgängliga onlinehjälpen för StorSimple Snapshot Manager och MMC.

#### <a name="to-view-available-online-help"></a>Du kan visa tillgänglig online hjälp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** , högerklickar du på någon nod eller expandera noden och högerklickar du i den **resultat** rutan och klicka sedan på **hjälp**. 

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om den [StorSimple Snapshot Manager användargränssnittet](storsimple-use-snapshot-manager.md).
* Lär dig mer om [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

