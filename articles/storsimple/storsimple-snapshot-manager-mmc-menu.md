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
ms.openlocfilehash: be761f0a2e59878139ae54dba62634a53b172711
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60303034"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Använd MMC menyn aktiviteter i StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
I StorSimple Snapshot Manager ser du följande åtgärder visas på alla Åtgärdsmenyer och alla varianter av den **åtgärder** fönstret.

* Visa
* Nytt fönster härifrån 
* Uppdatera 
* Exportera lista 
* Hjälp 

De här åtgärderna är en del av Microsoft Management Console (MMC) och är inte specifika för StorSimple Snapshot Manager. Den här självstudien beskrivs de här åtgärderna och hur du använder dem i StorSimple Snapshot Manager.

## <a name="view"></a>Visa
Du kan använda den **visa** alternativet för att ändra den **resultat** fönstret Visa och ändra konsolvyn för fönstret. 

#### <a name="to-change-the-results-pane-view"></a>Ändra vy resultat
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** högerklickar du på valfri nod eller expanderar du noden och högerklicka på ett objekt i den **resultat** fönstret och klicka sedan på den **visa** alternativet. 
3. Att lägga till eller ta bort kolumner som visas i den **resultat** fönstret klickar du på **Lägg till/ta bort kolumner**. Den **Lägg till/ta bort kolumner** dialogrutan visas.
   
    ![Lägga till eller ta bort kolumner från resultatfönstret](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Fyll i formuläret på följande sätt:
   
   * Välj objekt från den **tillgänglig** kolumner och klicka på **Lägg till** lägga till dem i den **kolumner som visas** lista. 
   * Klicka på objekten i den **kolumner som visas** och på **ta bort** ta bort dem från listan. 
   * Markera ett objekt i den **visade** kolumner och klicka på **Flytta upp** eller **Flytta ned** att flytta objektet uppåt eller nedåt i listan. 
   * Klicka på **Återställ standardvärden** att återgå till standard **resultat** fönstret konfiguration. 
5. När du är klar med dina val klickar du på **OK**. 

#### <a name="to-change-the-console-window-view"></a>Ändra konsolvyn för fönster
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på varje nod, klickar du på **visa**, och klicka sedan på **anpassa**. Den **anpassa** dialogrutan visas.
   
    ![Anpassa konsolfönstret](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Markera eller avmarkera kryssrutorna för att visa eller dölja objekt i konsolfönstret. När du är klar med dina val klickar du på **OK**.

## <a name="new-window-from-here"></a>Nytt fönster härifrån
Du kan använda den **nytt fönster härifrån** möjligheten att öppna ett nytt konsolfönster.

#### <a name="to-open-a-new-console-window"></a>Öppna ett nytt konsolfönster
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** fönstret högerklickar du på valfri nod och klicka sedan på **nytt fönster härifrån**. 
   
    Ett nytt fönster visas med endast omfång som du valde. Exempel: Om du högerklickar på den **principer för säkerhetskopiering** nod, det nya fönstret visas endast den **Säkerhetskopieringsprinciper** nod i den **omfång** fönstret och en lista över definierade säkerhetskopiering principer i den **resultat** fönstret. Se följande exempel.
   
    ![Nytt fönster härifrån](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Uppdatera
Du kan använda den **uppdatera** att uppdatera konsolfönstret.

#### <a name="to-update-the-console-window"></a>Uppdatera konsolfönstret
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** högerklickar du på valfri nod eller expanderar du noden och högerklicka på ett objekt i den **resultat** fönstret och klicka sedan på **uppdatera**. 

## <a name="export-list"></a>Exportera lista
Du kan använda den **Exportera lista** åtgärder för att spara en lista i en fil med kommaavgränsade värden (CSV). Exempelvis kan du exportera en lista över säkerhetskopieringsprinciper och säkerhetskopieringskatalogen. Du kan sedan importera CSV-filen till ett kalkylbladsprogram för analys.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Spara en lista i en fil med kommaavgränsade värden (CSV)
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager. 
2. I den **omfång** högerklickar du på valfri nod eller expanderar du noden och högerklicka på ett objekt i den **resultat** fönstret och klicka sedan på **Exportera lista**. 
3. Den **Exportera lista** dialogrutan visas. Fyll i formuläret på följande sätt: 
   
   1. I den **filnamn** skriver ett namn för CSV-filen eller klicka på pilen för att välja från den nedrullningsbara listan.
   2. I den **filformat** rutan klickar du på pilen och väljer en filtyp från den nedrullningsbara listan.
   3. Markera raderna för att spara enbart valda objekt, och klicka sedan på den **Spara endast markerade rader** markerar du kryssrutan. Om du vill spara alla exporterade listor, avmarkera de **Spara endast markerade rader** markerar du kryssrutan.
   4. Klicka på **Spara**.
      
      ![Exportera lista som en CSV-fil](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Hjälp
Du kan använda den **hjälpa** menyn för att visa tillgängliga onlinehjälpen för StorSimple Snapshot Manager och MMC.

#### <a name="to-view-available-online-help"></a>Visa tillgängliga onlinehjälp
1. Klicka på ikonen skrivbord för att starta StorSimple Snapshot Manager.
2. I den **omfång** högerklickar du på valfri nod eller expanderar du noden och högerklicka på ett objekt i den **resultat** fönstret och klicka sedan på **hjälpa**. 

## <a name="next-steps"></a>Nästa steg
* Läs mer om den [StorSimple Snapshot Manager-användargränssnittet](storsimple-use-snapshot-manager.md).
* Läs mer om [med StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

