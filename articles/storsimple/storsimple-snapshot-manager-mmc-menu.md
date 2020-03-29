---
title: Mmc-menc-menc-åtgärder för StorSimple Snapshot Manager | Microsoft-dokument
description: I artikeln beskrivs hur du anvÃ¤nder mmc-menyåtgärder fÃ¤r standard (Microsoft Management Console) i StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: ced075395c4f2a51308d5d3b0cbe4c0153a17816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931474"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Använda MMC-menyåtgärderna i StorSimple Snapshot Manager

## <a name="overview"></a>Översikt
I StorSimple Snapshot Manager visas följande åtgärder på alla åtgärdsmenyer och alla varianter av **åtgärdsfönstret.**

* Visa
* Nytt fönster från här 
* Uppdatera 
* Exportlista 
* Hjälp 

Dessa åtgärder är en del av Microsoft Management Console (MMC) och är inte specifika för StorSimple Snapshot Manager. Den här självstudien beskriver dessa åtgärder och förklarar hur du använder var och en av dem i StorSimple Snapshot Manager.

## <a name="view"></a>Visa
Du kan använda alternativet **Visa** för att ändra **resultatfönstrets** vy och ändra konsolfönstervyn. 

#### <a name="to-change-the-results-pane-view"></a>Så här ändrar du vyn Resultatruta
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på en nod i **fönstret Omfattning** eller expandera noden och högerklicka på ett objekt i **resultatfönstret** och klicka sedan på alternativet **Visa.** 
3. Om du vill lägga till eller ta bort kolumnerna som visas i **resultatfönstret** klickar du på **Lägg till/ta bort kolumner**. Dialogrutan **Lägg till/ta bort kolumner** visas.
   
    ![Lägga till eller ta bort kolumner från fönstret Resultat](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Fyll i formuläret enligt följande:
   
   * Markera objekt i listan **Tillgängliga** kolumner och klicka på **Lägg till** om du vill lägga till dem i listan **Visade kolumner.** 
   * Klicka på objekt i listan **Visade kolumner** och klicka på **Ta bort** om du vill ta bort dem från listan. 
   * Markera ett objekt i listan **Visade** kolumner och klicka på **Flytta uppåt** eller **Flytta ned** om du vill flytta objektet uppåt eller nedåt i listan. 
   * Klicka på **Återställ standardinställningar** om du vill återgå till standardkonfigurationen **för resultatfönstret.** 
5. När du är klar med dina val klickar du på **OK**. 

#### <a name="to-change-the-console-window-view"></a>Så här ändrar du konsolfönstervyn
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på en nod i **fönstret Omfattning,** klicka på **Visa**och klicka sedan på **Anpassa**. Dialogrutan **Anpassa** visas.
   
    ![Anpassa konsolfönstret](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Markera eller avmarkera kryssrutorna för att visa eller dölja objekt i konsolfönstret. När du är klar med dina val klickar du på **OK**.

## <a name="new-window-from-here"></a>Nytt fönster från här
Du kan använda alternativet **Nytt fönster härifrån** för att öppna ett nytt konsolfönster.

#### <a name="to-open-a-new-console-window"></a>Så här öppnar du ett nytt konsolfönster
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på valfri nod i **fönstret Omfattning** och klicka sedan på Nytt **fönster härifrån**. 
   
    Ett nytt fönster visas som visar bara det scope som du har markerat. Om du till exempel högerklickar på **noden Principer för säkerhetskopiering** visas bara noden **Säkerhetskopieringsprinciper** i **fönstret Omfattning** och en lista över definierade principer för säkerhetskopiering i **resultatfönstret.** Se följande exempel.
   
    ![Nytt fönster från här](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Uppdatera
Du kan använda åtgärden **Uppdatera** för att uppdatera konsolfönstret.

#### <a name="to-update-the-console-window"></a>Så här uppdaterar du konsolfönstret
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på en nod i **fönstret Omfattning** eller expandera noden och högerklicka på ett objekt i **resultatfönstret** och klicka sedan på **Uppdatera**. 

## <a name="export-list"></a>Exportlista
Du kan använda åtgärden **Exportlista** för att spara en lista i en CSV-fil (kommaavgränsad värde). Du kan till exempel exportera listan över principer för säkerhetskopiering eller säkerhetskopieringskatalogen. Du kan sedan importera CSV-filen till ett kalkylbladsprogram för analys.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Så här sparar du en lista i en CSV-fil (kommaavgränsad värde)
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager. 
2. Högerklicka på en nod i **fönstret Omfattning** eller expandera noden och högerklicka på ett objekt i **resultatfönstret** och klicka sedan på **Exportera lista**. 
3. Dialogrutan **Exportera lista** visas. Fyll i formuläret enligt följande: 
   
   1. Skriv ett namn på CSV-filen i rutan **Filnamn** eller klicka på pilen för att välja från listrutan.
   2. Klicka på pilen i rutan **Filformat** och välj en filtyp i listrutan.
   3. Om du bara vill spara markerade objekt markerar du raderna och klickar sedan på kryssrutan **Spara endast markerade rader.** Om du vill spara alla exporterade listor avmarkerar du kryssrutan **Spara endast markerade rader.**
   4. Klicka på **Spara**.
      
      ![Exportera lista som en kommaavgränsad värdefil](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Hjälp
Du kan använda **hjälpmenyn** för att visa tillgänglig onlinehjälp för StorSimple Snapshot Manager och MMC.

#### <a name="to-view-available-online-help"></a>Så här visar du tillgänglig onlinehjälp
1. Klicka på skrivbordsikonen för att starta StorSimple Snapshot Manager.
2. Högerklicka på en nod i **fönstret Omfattning** eller expandera noden och högerklicka på ett objekt i **resultatfönstret** och klicka sedan på **Hjälp**. 

## <a name="next-steps"></a>Nästa steg
* Läs mer om [användargränssnittet i StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Läs mer om [hur du använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).

