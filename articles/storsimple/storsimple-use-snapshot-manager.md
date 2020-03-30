---
title: Användargränssnittet För StorSimple Snapshot Manager | Microsoft-dokument
description: Beskriver användargränssnittet i StorSimple Snapshot Manager och förklarar hur du använder det för att hantera säkerhetskopieringsjobb och säkerhetskopieringskatalogen.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.custom: ''
ms.openlocfilehash: 56771d2e62289485017f34c6a9ab26e1d53610ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933976"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Använd StorSimple Snapshot Manager-användargränssnittet för att hantera säkerhetskopieringsjobb och säkerhetskopieringskatalog

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager har ett intuitivt användargränssnitt som du kan använda för att ta och hantera säkerhetskopior. Den här självstudien ger en introduktion till användargränssnittet och förklarar sedan hur du använder var och en av komponenterna. En detaljerad beskrivning av StorSimple Snapshot Manager finns i [Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Beskrivning av konsolen
Om du vill visa användargränssnittet klickar du på ikonen StorSimple Snapshot Manager på skrivbordet. Konsolfönstret visas, som visas i följande bild.

![Fönsterrutor för StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Konsolfönstret har fem huvudelement. Klicka på lämplig länk för en fullständig beskrivning av varje element.

* [Menyraden](#menu-bar) 
* [Verktygsfält](#tool-bar) 
* [Fönstret Omfattning](#scope-pane) 
* [Resultatfönstret](#results-pane) 
* [Fönstret Åtgärder](#actions-pane) 

Dessutom stöder StorSimple Snapshot Manager [tangentbordsnavigering och ett antal genvägar](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konsoltillgänglighet
Användargränssnittet i StorSimple Snapshot Manager stöder hjälpmedelsfunktionerna i Windows operativsystem och Microsoft Management Console (MMC), samt vissa StorSimple Snapshot Manager-specifika kortkommandon. 

* En beskrivning av hjälpmedelsfunktionerna i Windows finns i [Kortkommandon för Windows](https://support.microsoft.com/kb/126449). 
* En beskrivning av MMC-hjälpmedelsfunktionerna finns i [Hjälpmedel för MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* En beskrivning av hjälpmedelsfunktionerna i StorSimple Snapshot Manager finns i [Tangentbordsnavigering och genvägar](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Menyraden
Menyraden högst upp i konsolfönstret innehåller menyerna [Arkiv,](#file-menu) [Åtgärd](#action-menu), [Visa,](#view-menu) [Favoriter,](#favorites-menu) [Fönster](#window-menu)och [Hjälp.](#help-menu)

Klicka på ett objekt på menyraden om du vill visa en lista över tillgängliga kommandon på den menyn. I följande exempel **View** visas visa-menyn som valts på menyraden.

![Visa-menyn markerad](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Arkivmenyn
**Arkiv-menyn** innehåller mmc-standardkommandon (Standard.

#### <a name="menu-access"></a>Menyåtkomst
Om du vill visa **Arkiv-menyn** klickar du på **Arkiv** på menyraden. Följande meny visas.

![Arkiv-menyn StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menybeskrivning
I följande tabell beskrivs objekt som visas på **Arkiv-menyn.**

| Menyalternativet | Beskrivning |
|:--- |:--- |
| Ny |Klicka på **Ny** om du vill skapa en ny konsol baserat på StorSimple Snapshot Manager. |
| Öppna |Öppna en befintlig konsol genom att klicka på **Öppna.** |
| Spara |Klicka på **Spara** om du vill spara den aktuella konsolen. |
| Spara som |Klicka på **Spara som** om du vill skapa en ny, omdöpt förekomst av den aktuella konsolen. Använd alternativet **Spara som** för att anpassa en vy och spara den för senare hämtning. Du kan till exempel skapa snapin-moduler för StorSimple Snapshot Manager som pekar på specifika servrar. |
| Lägg till/ta bort snapin-modulen |Klicka på **Lägg till/ta bort snapin-modulen** om du vill lägga till eller ta bort snapin-moduler och ordna noder i **scopefönstret.** Mer information finns [i Lägg till, Ta bort och Ordna snapin-moduler och tillägg i MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Alternativ |Klicka på **Alternativ** om du vill ändra konsolikonen, ange användaråtkomstlägen och behörigheter eller ta bort konsolfiler för att öka tillgängligt diskutrymme. |
| Lista över filsökvägar |Klicka på en sökväg i den numrerade listan om du vill öppna en fil som du nyligen har öppnat. |
| Avsluta |Stäng **Arkiv-menyn** genom att klicka på **Avsluta.** |

### <a name="action-menu"></a>Åtgärdsmenyn
Använd **Åtgärdsmenyn** för att välja bland tillgängliga åtgärder. Vilka objekt som är tillgängliga beror på vilken markering du gör i **fönstret Scope** eller **Resultat.**

#### <a name="menu-access"></a>Menyåtkomst
Så här visar du **Åtgärdsmenyn:**

* Högerklicka på ett objekt i **fönstret Scope** eller **Resultatfönstret.**
* Markera ett objekt i **fönstret Scope** eller **Resultat** och klicka sedan på **Åtgärd** på menyraden. 

Om du till exempel markerar den översta noden i **scopefönstret** och sedan högerklickar eller klickar på **Åtgärd** på menyraden visas följande meny.

![Snabb åtgärdsmenyn För ögonblicksbildhanterare](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

**Åtgärdsfönstret** (till höger om konsolen) innehåller samma åtgärdslista som **Åtgärdsmenyn.** Dessutom innehåller **åtgärdsfönstret** alternativ på **Visa-menyn,** där du kan skapa en anpassad vy av **resultatfönstret.**

![Fönstret Åtgärder med Visa-menyn öppen](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menybeskrivning
Följande tabell innehåller en alfabetisk lista över Åtgärder för StorSimple Snapshot Manager. 

* I kolumnen **Åtgärd** visas åtgärder som du kan utföra på noder och resultat. 
* I kolumnen **Navigering** beskrivs hur du visar lämplig **Åtgärdsmeny** så att du kan välja åtgärden. Vissa åtgärder visas **Action** på flera Åtgärdsmenyer. För dessa åtgärder väljer du ett **navigeringsalternativ** i punktlistan. 
* I kolumnen **Beskrivning** beskrivs hur du använder varje åtgärd på **åtgärdsmenyn** eller åtgärdsfönstret och förklarar vad den gör.

> [!NOTE]
> **Åtgärdsfönstret** och Åtgärdsmenyerna innehåller ytterligare alternativ, till exempel **Visa**, **Nytt fönster härifrån**, **Uppdatera,** **Exportera lista**och **hjälp**. **Action** Dessa alternativ är tillgängliga som en del av MMC och är inte specifika för StorSimple Snapshot Manager. Tabellen innehåller beskrivningar av dessa alternativ.
> 
> 

| Åtgärd | Navigering | Beskrivning |
|:--- |:--- |:--- |
| Autentisera |Klicka på noden **Enheter** och högerklicka på en enhet i **fönstret Resultat.** |Klicka på **Autentisera** om du vill ange det lösenord som du har konfigurerat för enheten. |
| Klona |Expandera **säkerhetskopieringskatalog,** expandera ögonblicksbilder av **molnet,** klicka på en daterad säkerhetskopia och välj sedan en volym i **fönstret Resultat.** |Klicka på **Klona** om du vill skapa en kopia av en ögonblicksbild i molnet och lagra den på en plats som du anger. |
| Konfigurera en enhet |Högerklicka på noden **Enheter.** |Klicka på **Konfigurera en enhet** om du vill konfigurera en enhet eller flera enheter för att ansluta till Windows-värden. |
| Skapa princip för säkerhetskopiering |Gör något av följande:<ul><li>Högerklicka på **principer för säkerhetskopiering**.</li><li>Klicka eller expandera **Volymgrupper**och högerklicka sedan på en volymgrupp.</li><li>Klicka eller expandera **Säkerhetskopieringskatalog**och högerklicka sedan på en volymgrupp.</li></ul> |Klicka på **Skapa princip för säkerhetskopiering** om du vill konfigurera en schemalagd säkerhetskopia för en volymgrupp. |
| Skapa volymgrupp |Gör något av följande:<ul><li>Klicka på noden **Volymer** och högerklicka sedan på en volym i **fönstret Resultat.**</li><li>Högerklicka på noden **Volymgrupper.**</li></ul> |Klicka på **Skapa volymgrupp** om du vill tilldela volymer till en volymgrupp. |
| Ta bort |Klicka på en nod eller ett resultat (Det här objektet visas på många **åtgärdsmenyer** och åtgärdsfönster.) **Actions** |Klicka på **Ta bort** om du vill ta bort noden eller resultatet som du har markerat. När bekräftelsedialogrutan visas bekräftar eller avbryter du borttagningen. |
| Information |Klicka på noden **Enheter** och högerklicka sedan på en enhet i **fönstret Resultat.** |Klicka på **Information** om du vill visa konfigurationsinformation för en enhet. |
| Redigera |Klicka på **Principer för säkerhetskopiering**och högerklicka sedan på en princip i **resultatfönstret.** |Klicka på **Redigera** om du vill ändra säkerhetskopieringsschemat för en volymgrupp. |
| Exportlista |Klicka på en nod eller ett resultat (Det här objektet visas på alla **åtgärdsmenyer** och åtgärdsfönster.) **Actions** |Klicka på **Exportera lista** om du vill spara en lista i en CSV-fil (kommaavgränsad värde). Du kan sedan importera den här filen till ett kalkylbladsprogram för analys. |
| Hjälp |Klicka på en nod eller ett resultat. (Det här objektet visas på alla **åtgärdsmenyer** och åtgärdsfönster.) **Actions** |Klicka på **Hjälp** om du vill öppna onlinehjälpen i ett separat webbläsarfönster. |
| Nytt fönster från här |Klicka på en nod eller ett resultat (Det här objektet visas på alla **åtgärdsmenyer** och åtgärdsfönster.) **Actions** |Klicka på **Nytt fönster härifrån** om du vill öppna ett nytt StorSimple Snapshot Manager-fönster. |
| Uppdatera |Klicka på en nod eller ett resultat (Det här objektet visas på alla **åtgärdsmenyer** och åtgärdsfönster.) **Actions** |Klicka på **Uppdatera** om du vill uppdatera det fönster som visas i StorSimple Snapshot Manager. |
| Uppdatera enhet |Klicka på noden **Enheter** och högerklicka på en enhet i **fönstret Resultat.** |Klicka på **Uppdatera enhet** om du vill synkronisera en viss ansluten enhet med StorSimple Snapshot Manager. |
| Uppdatera enheter |Högerklicka på noden **Enheter.** |Klicka på **Uppdatera enheter** om du vill synkronisera listan över anslutna enheter med StorSimple Snapshot Manager. |
| Omsökningsvolymer |Högerklicka på **noden Volymer.** |Klicka på **Omsökningsvolymer** om du vill uppdatera listan över volymer som visas i **resultatfönstret.** |
| Återställ |Expandera **säkerhetskopieringskatalog,** expandera en volymgrupp, expandera **Lokala ögonblicksbilder** eller **ögonblicksbilder av molnet**och högerklicka sedan på en säkerhetskopia. |Klicka på **Återställ** om du vill ersätta de aktuella volymgruppsdata med data från den valda säkerhetskopian. |
| Ta backup |Gör något av följande:<ul><li>Expandera **volymgrupper**och högerklicka sedan på en volymgrupp.</li><li>Expandera **säkerhetskopieringskatalog**och högerklicka sedan på en volymgrupp.</li></ul> |Klicka på **Ta med säkerhetskopiering** om du vill starta ett säkerhetskopieringsjobb omedelbart. |
| Visa växlingsimport |Högerklicka på den övre noden i **scopefönstret** (Noden **StorSimple Snapshot Manager** i exemplen). |Klicka på **Växla importvisning** om du vill visa eller dölja de volymgrupper och associerade säkerhetskopior som importerades från tjänstinstrumentpanelen För StorSimple-enhetshanteraren. |

### <a name="view-menu"></a>Visa-menyn
Använd **Visa-menyn** för att skapa en anpassad vy av **resultatfönstrets** innehåll. **Visa-menyn** innehåller **alternativ för Lägg till/ta bort kolumner** och **anpassa.**

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt **Visa-menyn** på menyraden eller i **åtgärdsfönstret.**

![Visa-menyn StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menybeskrivning
I följande tabell beskrivs objekt som visas på **Visa-menyn.**

| Menyalternativet | Beskrivning |
|:--- |:--- |
| Lägga till/ta bort kolumner |Klicka på **Lägg till/ta bort kolumner** om du vill lägga till eller ta bort kolumner i **resultatfönstret.** |
| Anpassa |Klicka på **Anpassa** om du vill visa eller dölja objekt i konsolfönstret För StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menyn Favoriter
Använd **menyn Favoriter** för att lägga till, ta bort och ordna sidvisningar och uppgifter som du använder ofta. 

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt **menyn Favoriter** i menyraden.

![Menyn StorSimple Snapshot Manager Favoriter](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menybeskrivning
I följande tabell beskrivs objekt som visas på **menyn Favoriter.**

| Menyalternativet | Beskrivning |
|:--- |:--- |
| Lägg till i favoriter |Klicka på **Lägg till i Favoriter** om du vill lägga till den aktuella vyn i listan med favoriter. |
| Ordna favoriter |Klicka på **Ordna favoriter** om du vill ordna innehållet i mappen Favoriter. |

### <a name="window-menu"></a>Fönstermeny
Använd **Fönster-menyn** för att lägga till och ordna om Konsolfönster för StorSimple Snapshot Manager.

#### <a name="menu-access"></a>Menyåtkomst
Du kommer åt **fönstermenyn** i menyraden.

![Fönstermeny för StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Den numrerade listan längst ned på menyn visar de fönster som för närvarande är öppna. Klicka på ett fönster i listan om du vill föra in fönstret i förgrunden. 

#### <a name="menu-description"></a>Menybeskrivning
I följande tabell beskrivs de objekt som visas på Fönster-menyn.

| Menyalternativet | Beskrivning |
|:--- |:--- |
| Nytt fönster |Klicka på **Nytt fönster** om du vill öppna ett nytt konsolfönster (förutom det befintliga fönstret). |
| Cascade |Klicka på **Kaskad** om du vill visa de öppna konsolfönstren i ett övergripande format. |
| Sida vid sida vågrätt |Klicka på **Panel vågrätt** om du vill visa de öppna konsolfönstren i ett panelformat (eller rutnät). |
| Ordna ikoner |Om du har flera konsolfönster öppna och utspridda över skrivbordet **minimerar** du dem och klickar sedan på Ordna ikoner för att ordna dem i en vågrät rad längst ned på skärmen. |

### <a name="help-menu"></a>Hjälp-menyn
Använd **hjälpmenyn** för att visa tillgänglig onlinehjälp för StorSimple Snapshot Manager och MMC. Du kan också visa information om programversionerna för MMC och StorSimple Snapshot Manager som för närvarande är installerade på datorn. 

Du kan komma åt **hjälpmenyn** i menyraden. Du kan också komma åt Hjälpavsnitt för StorSimple Snapshot Manager från **åtgärdsfönstret.**

![Hjälp-menyn För StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menybeskrivning
I följande tabell beskrivs objekt som visas på Hjälp-menyn.

| Menyalternativet | Beskrivning |
|:--- |:--- |
| Hjälp om StorSimple Snapshot Manager |Klicka **på Hjälp på StorSimple Snapshot Manager** för att öppna Hjälpen för StorSimple Snapshot Manager i ett separat fönster. |
| Hjälpavsnitt |Klicka på **Hjälpavsnitt** om du vill öppna MMC:s onlinehjälp i ett separat fönster. |
| TechCenter-webbplats |Klicka på **TechCenters webbplats** om du vill öppna startsidan för Microsoft TechNet Tech Center i ett separat fönster. |
| Om Microsoft Management Console |Klicka på **Om Microsoft Management Console** om du vill se vilken version av Microsoft Management Console som är installerad på datorn. |
| Om StorSimple Snapshot Manager |Klicka på **Om StorSimple Snapshot Manager** för att se vilken version av snapin-modulen som är installerad på datorn. |

## <a name="tool-bar"></a>Verktygsfält
Verktygsfältet, som finns under menyraden, innehåller navigerings- och uppgiftsikoner. Varje ikon är en genväg till en viss uppgift.

### <a name="icon-descriptions"></a>Ikonbeskrivningar
I följande tabell beskrivs ikonerna som visas i verktygsfältet. 

| Ikon | Beskrivning |
|:--- |:--- |
| ![Vänsterpil](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klicka på vänsterpilsikonen för att återgå till föregående sida. |
| ![Högerpil](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klicka på högerpilen för att gå till nästa sida (om pilen är grå är åtgärden inte tillgänglig). |
| ![Ikon för Upp](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klicka på ikonen upp om du vill gå upp en nivå i konsolträdet **(scopefönstret).** |
| ![Visa/dölja konsolträd](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klicka på ikonen visa/dölj konsolträd om du vill visa eller dölja **scopefönstret.** |
| ![Exportlista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klicka på exportlistikonen om du vill exportera en lista till en CSV-fil som du anger. |
| ![Hjälp-ikonen](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klicka på hjälpikonen för att öppna ett mmc-hjälpavsnitt online. |
| ![Fönstret Visa/dölja åtgärder](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klicka på ikonen **För att** visa/dölja åtgärder om du vill visa eller dölja **åtgärdsfönstret.** |

## <a name="scope-pane"></a>Fönstret Omfattning
**Scope-fönstret** är den vänstra rutan i storsimpleögonblickshanterarens användargränssnitt. Den innehåller konsolträdet (eller noden) och är den primära navigeringsmekanismen för StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktur för scopefönster
**Fönstret Scope** innehåller en serie klickbara objekt (noder) ordnade i en trädstruktur. 

![Fönstret Omfattning](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Om du vill expandera eller komprimera en nod klickar du på pilikonen bredvid nodnamnet.
* Om du vill visa status eller innehåll för en nod klickar du på nodnamnet. Informationen visas i **fönstret Resultat.** 

**Fönstret Omfattning** innehåller följande noder: 

* [Nod för enheter](#devices-node) 
* [Nod för volymer](#volumes-node) 
* [Nod för volymgrupper](#volume-groups-node) 
* [Nod för principer för säkerhetskopiering](#backup-policies-node) 
* [Nod för katalog för säkerhetskopiering](#backup-catalog-node) 
* [Jobbnod](#jobs-node) 

### <a name="scope-pane-tasks"></a>Aktiviteter i scopefönstret
Du kan använda **scopefönstret** för att slutföra en åtgärd på en viss nod. Gör något av följande om du vill markera en uppgift:

* Högerklicka på noden och välj sedan uppgiften på menyn som visas.
* Klicka på noden och sedan på **Åtgärd** i menyraden. Markera uppgiften på menyn som visas.
* Klicka på noden och markera sedan åtgärden i **åtgärdsfönstret.**

När du väljer en nod och använder någon av dessa metoder för att se en uppgiftslista visas endast de åtgärder som kan utföras på den noden.

### <a name="devices-node"></a>Nod för enheter
Noden **Enheter** representerar StorSimple-enheter och StorSimple-virtuella enheter som är anslutna till StorSimple Snapshot Manager. Välj den här noden om du vill ansluta och konfigurera en enhet och importera tillhörande volymer, volymer och befintliga säkerhetskopior. Flera enheter kan anslutas till en enda värd.

* Om du vill expandera noden klickar du på pilikonen bredvid **Enheter**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på noden **Enheter** eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Klicka på **Enheter** i **fönstret Scope** om du vill visa en lista över konfigurerade enheter. Listan över enheter, tillsammans med information om varje enhet, visas i **fönstret Resultat.**

### <a name="volumes-node"></a>Nod för volymer
**Noden Volymer** representerar de enheter som motsvarar de volymer som monterats av värden, inklusive de som upptäcks via iSCSI och de som identifieras via en enhet. Använd den här noden om du vill visa listan över tillgängliga volymer och tilldela enskilda volymer till volymgrupper.

* Om du vill expandera noden klickar du på pilikonen **bredvid Volymer**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på **noden Volymer** eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Klicka på **Volymer** i **fönstret Scope** om du vill visa en lista över volymer. Listan över volymer, tillsammans med information om varje volym, visas i **fönstret Resultat.**

### <a name="volume-groups-node"></a>Nod för volymgrupper
Volymgrupper kallas också konsekvensgrupper. Varje volymgrupp är en pool med programrelaterade volymer som hjälper till att säkerställa programkonsekvens under säkerhetskopieringsåtgärder. Använd noden **Volymgrupper** för att konfigurera dessa grupper och för att ta interaktiva säkerhetskopior eller skapa säkerhetskopieringsscheman. 

* Om du vill expandera noden klickar du på pilikonen **bredvid Volymgrupper**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på noden **Volymgrupper** eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Om du vill visa en lista över volymgrupper klickar du på **Volymgrupper** i **fönstret Scope.** Listan över volymgrupper, tillsammans med information om varje volymgrupp, visas i **fönstret Resultat.**

### <a name="backup-policies-node"></a>Nod för principer för säkerhetskopiering
Principer för säkerhetskopiering är jobbscheman för lokala ögonblicksbilder och ögonblicksbilder av molnet. Använd **noden Principer** för säkerhetskopiering för att ange hur ofta en säkerhetskopia skapas och hur länge en säkerhetskopia ska behållas. 

* Om du vill expandera noden klickar du på pilikonen **bredvid Principer för säkerhetskopiering**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på **noden Principer för säkerhetskopiering** eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Om du vill visa en lista över principer för säkerhetskopiering klickar du på **Principer för säkerhetskopiering** i **fönstret Scope.** Listan över principer för säkerhetskopiering, tillsammans med information om varje princip, visas i **fönstret Resultat.**

> [!NOTE]
> Du kan behålla maximalt 64 säkerhetskopior.


### <a name="backup-catalog-node"></a>Nod för katalog för säkerhetskopiering
**Noden Säkerhetskopieringskatalog** innehåller listor över säkerhetskopieringar på plats och säkerhetskopiering på annan plats av Azure StorSimple-volymer. Den här noden är ordnad efter volymgrupp och varje volymgruppbehållare innehåller separata strukturer för lokala ögonblicksbilder **(den lokala ögonblicksbildens**nod) och ögonblicksbilder av molnet (cloud **snapshots-noden).** När den expanderas listar varje volymgruppbehållare alla lyckade säkerhetskopior som har gjorts interaktivt eller av en konfigurerad princip.

* Om du vill expandera noden klickar du på pilikonen **bredvid Säkerhetskopieringskatalogen**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på noden **Säkerhetskopieringskatalog** eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Om du vill visa en lista över ögonblicksbilder av säkerhetskopior klickar du på **Säkerhetskopieringskatalog** i **scopefönstret.** Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild, visas i **fönstret Resultat.**

### <a name="local-snapshots-node"></a>Nod för lokala ögonblicksbilder
Noden **Lokala ögonblicksbilder listar** lokala ögonblicksbilder för en viss volymgrupp. Noden finns under **noden Säkerhetskopieringskatalog** i **fönstret Scope.** Lokala ögonblicksbilder är punkt-i-tid-kopior av volymdata som lagras på Azure StorSimple-enheten. Vanligtvis kan den här typen av säkerhetskopiering skapas och återställas snabbt. Du kan använda en lokal ögonblicksbild på samma sätt som en lokal säkerhetskopia.

* Om du vill expandera noden klickar du på pilikonen **bredvid Lokala ögonblicksbilder**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på noden **Lokala ögonblicksbilder** eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Om du vill visa en lista över lokala ögonblicksbilder klickar du på **Lokala ögonblicksbilder** i **fönstret Scope.** Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild, visas i **fönstret Resultat.**

### <a name="cloud-snapshots-node"></a>Nod för ögonblicksbilder i molnet
Noden **Cloud Snapshots listar** ögonblicksbilder av molnet för en viss volymgrupp. Noden finns under **noden Säkerhetskopieringskatalog** i **fönstret Scope.** Ögonblicksbilder av molnet är punkt-i-tid-kopior av volymdata som lagras i molnet. En ögonblicksbild i molnet motsvarar en ögonblicksbild som replikeras på ett annat lagringssystem utanför platsen. Ögonblicksbilder av molnet är särskilt användbara i katastrofåterställningsscenarier.

* Om du vill expandera noden klickar du på pilikonen bredvid **Ögonblicksbilder av molnet**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på noden **Ögonblicksbilder** i molnet eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Om du vill visa en lista över ögonblicksbilder i molnet klickar du på **Ögonblicksbilder** av molnet i **fönstret Scope.** Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild, visas i **fönstret Resultat.**

### <a name="jobs-node"></a>Jobbnod
Noden **Jobb** innehåller information om schemalagda, körande och nyligen slutförda säkerhetskopieringsjobb. 

* Om du vill expandera noden klickar du på pilikonen bredvid **Jobb**.
* Om du vill visa en meny med tillgängliga åtgärder högerklickar du på noden **Jobb** eller högerklickar på någon av de noder som visas i den expanderade vyn.
* Om du vill visa en lista över schemalagda jobb expanderar du noden **Jobb** och klickar sedan på **Schemalagd**. Listan över tidigare konfigurerade jobb och information om varje jobb visas i **fönstret Resultat.** 
* Om du vill visa en lista över nyligen slutförda jobb expanderar du noden **Jobb** och klickar sedan på **Senaste 24 timmarna**. En lista över jobb som har slutförts under de senaste 24 timmarna visas i **fönstret Resultat.** Fönstret **Resultat** innehåller också information om varje slutfört jobb.
* Om du vill visa en lista över jobb som körs expanderar du noden **Jobb** och klickar sedan på **Kör**. Listan över jobb som körs och information om varje jobb visas i **fönstret Resultat.**

## <a name="results-pane"></a>Resultatfönstret
**Fönstret Resultat** är mittfönstret i storsimpleögonblickshanterarens användargränssnitt. Den innehåller listor och detaljerad statusinformation för den nod som du valde i **fönstret Scope.**

### <a name="example"></a>Exempel
Klicka på noden **Volymgrupper** i fönstret **Scope** om du vill visa följande exempel. I **fönstret Resultat** visas en lista över volymgrupper med information om varje grupp.

![Resultatfönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Du kan konfigurera informationen i **resultatfönstret:** högerklicka på en nod i **fönstret Scope,** klicka på **Visa**och klicka sedan på **Lägg till/ta bort kolumner**.

## <a name="actions-pane"></a>Fönstret Åtgärder
**Åtgärdsfönstret** är den högra rutan i storsimpleögonblickshanterarens användargränssnitt. Den innehåller en meny med åtgärder som du kan utföra på noden, vyn eller data som du väljer i **fönstret Scope** eller **resultatfönstret.** Åtgärdsfönstret innehåller samma kommandon som **åtgärdsmenyerna** som är tillgängliga för objekt i **fönstret Scope** och **resultat.** **Actions** En beskrivning av varje åtgärd finns i tabellen i avsnittet **Åtgärdsmeny.**

### <a name="examples"></a>Exempel
Om du vill visa följande exempel expanderar du noden **Jobb** i fönstret **Omfattning** och klickar på **Schemalagd**. I fönstret **Åtgärder** visas tillgängliga åtgärder för den **schemalagda** noden.

![Exempel på schemalagda jobb i åtgärdsfönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Om du vill se fler alternativ expanderar du noden **Jobb** i **fönstret Scope,** klickar på **Schemalagd**och klickar sedan på ett schemalagt jobb i **resultatfönstret.** I fönstret **Åtgärder** visas tillgängliga åtgärder för det schemalagda jobbet, vilket visas i följande exempel.

![Exempel på åtgärder i åtgärdsfönstrets jobbåtgärder](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Tangentbordsnavigering och kortkommandon
StorSimple Snapshot Manager aktiverar hjälpmedelsfunktionerna i Operativsystemet Windows och Microsoft Management Console (MMC). Den innehåller också vissa funktioner och kortkommandon för tangentbordsnavigering som är specifika för StorSimple Snapshot Manager, enligt beskrivningen i följande avsnitt.

* [Navigeringstangenter för tangentbord](#keyboard-navigation-keys) 
* [Kortkommandon för menyraden](#menu-bar-shortcut-keys) 
* [Kortkommandon i scopefönstret](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigeringstangenter för tangentbord
I följande tabell beskrivs de nycklar som du kan använda för att navigera i användargränssnittet i StorSimple Snapshot Manager. 

| Navigeringsnyckel | Åtgärd |
|:--- |:--- |
| Nedpil |Använd nedpiltangenten för att flytta lodrätt till nästa objekt i en meny eller ett fönster. |
| Ange |Tryck på Retur för att slutföra en åtgärd och fortsätt sedan till nästa steg. Du kan till exempel trycka på Retur för att välja **Nästa**, **OK**eller **Skapa**och sedan gå till nästa steg i en guide. |
| Esc |Tryck på Esc för att stänga en meny eller för att avbryta och stänga en sida. |
| F1 |Tryck på F1 för att visa ett hjälpavsnitt för det aktiva fönstret. |
| F5 |Tryck på F5 för att uppdatera en nod. |
| F6 |Tryck på F6 för att gå från **scopefönstret** till **resultatfönstret.** |
| F10 |Tryck på F10 för att gå till menyraden. |
| Vänsterpil |Använd vänsterpilen för att flytta vågrätt från ett menyradsalternativ till föregående alternativ. När du flyttar till föregående objekt på menyraden visas åtgärdsmenyn (eller snabbmenyn) för föregående objekt. |
| Högerpil |Använd högerpilen för att flytta vågrätt från ett menyradsalternativ till nästa. När du flyttar till nästa objekt på menyraden visas åtgärdsmenyn (eller snabbmenyn) för det nya objektet. |
| Tabbtangent |Använd tabbtangenten för att gå till nästa fönster på konsolen eller till nästa markering eller textruta på en sida. |
| Uppil |Använd uppåtpiltangenten för att flytta lodrätt till föregående objekt på en meny eller ett fönster. |

### <a name="menu-bar-shortcut-keys"></a>Kortkommandon för menyraden
I följande tabell beskrivs kombinationerna av kortkommandon för menyraden. När du har tryckt på kortkommandona och menyn öppnas kan du använda menygenvägar (de understrukna tangenterna på menyn). Mer information om menyraden finns i [Menyraden](#menu-bar).

| Genväg | Resultat | Snabbknapp för meny | Resultat |
|:--- |:--- |:--- |:--- |
| ALT+F |Öppnar **Arkiv-menyn.** |N |Öppnar en ny konsolinstans. |
|  |O |Öppnar sidan **Administrationsverktyg.** | |
|  |S |Sparar StorSimple Snapshot Manager-konsolen. | |
|  |A |Öppnar sidan **Spara som.** | |
|  |M |Öppnar **snapin-modulen Lägg till/ta bort.** | |
|  |P |Öppnar sidan **Alternativ.** | |
|  |H |Öppnar onlinehjälp. | |
| ALT+A |Öppnar **åtgärdsmenyn.** |I |Aktiverar och inaktiverar alternativet för importvisning. |
|  |W |Öppnar en ny StorSimple Snapshot Manager-konsol. | |
|  |F |Uppdaterar StorSimple Snapshot Manager-konsolen. | |
|  |L |Öppnar sidan **Exportera lista.** | |
|  |H |Öppnar onlinehjälp. | |
| ALT+V |Öppnar **Visa-menyn.** |A |Öppnar sidan **Lägg till/ta bort kolumner.** |
|  |U |Öppnar sidan **Anpassa vy.** | |
| ALT+O |Öppnar menyn **Favoriter.** |A |Öppnar sidan **Lägg till i favoriter.** |
|  |O |Öppnar sidan **Ordna favoriter.** | |
| ALT+W |Öppnar **fönstermenyn.** |N |Öppnar ett annat StorSimple Snapshot Manager-fönster. |
|  |C |Visar alla öppna konsolfönster i ett kaskadformat. | |
|  |T |Visar alla öppna konsolfönster i ett rutnätsmönster. | |
|  |I |Ordnar ikoner i en vågrät rad längst ned på skärmen. | |
| ALT+H |Öppnar **hjälpmenyn.** |H |Öppnar onlinehjälp. |
|  |T |Öppnar webbsidan Microsoft TechNet Tech Center. | |
|  |A |Öppnar sidan **Om Microsoft Management Console.** | |

### <a name="scope-pane-shortcut-keys"></a>Kortkommandon i scopefönstret
I följande tabeller visas kombinationerna av kortkommandon för varje nod i **fönstret Scope.** 

* [Kortkommandon för enhetsnod](#devices-node-shortcut-keys)
* [Kortkommandon för volymernod](#volumes-node-shortcut-keys)
* [Kortkommandon för nodknappar för volymgrupper](#volume-groups-node-shortcut-keys)
* [Kortkommandon för nodknappar för säkerhetskopieringsprinciper](#backup-policies-node-shortcut-keys)
* [Kortkommandon för säkerhetskopieringskatalognod](#backup-catalog-node-shortcut-keys)
* [Kortkommandon för jobbnod](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Kortkommandon för enhetsnod
| Menygenväg | Resultat |
|:--- |:--- |
| C |Öppnar sidan **Konfigurera en enhet.** |
| D |Uppdaterar listan över enheter och enhetsinformation. |
| V |Öppnar **Visa-menyn.** |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol med fokus på **informationsnoden.** |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista.** |
| H |Öppnar onlinehjälp. |

#### <a name="volumes-node-shortcut-keys"></a>Kortkommandon för volymernod
| Menygenväg | Resultat |
|:--- |:--- |
| V |Uppdaterar listan över volymer. |
| V (tryck två gånger) |Öppnar **Visa-menyn.** |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol med fokus på **noden Volymer.** |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista.** |
| H |Öppnar onlinehjälp. |

#### <a name="volume-groups-node-shortcut-keys"></a>Kortkommandon för nodknappar för volymgrupper
| Menygenväg | Resultat |
|:--- |:--- |
| G |Öppnar sidan **Skapa en volymgrupp.** |
| V |Öppnar **Visa-menyn.** |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol med fokus på noden **Volymgrupper.** |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista.** |
| H |Öppnar onlinehjälp. |

#### <a name="backup-policies-node-shortcut-keys"></a>Kortkommandon för nodknappar för säkerhetskopieringsprinciper
| Menygenväg | Resultat |
|:--- |:--- |
| B |Öppnar sidan **Skapa en princip.** |
| V |Öppnar **Visa-menyn.** |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol med fokus på noden **Volymgrupper.** |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportlista **. |
| H |Öppnar onlinehjälp. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Kortkommandon för säkerhetskopieringskatalognod
| Menygenväg | Resultat |
|:--- |:--- |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol med fokus på noden **Volymgrupper.** |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| H |Öppnar onlinehjälp. |

#### <a name="jobs-node-shortcut-keys"></a>Kortkommandon för jobbnod
| Menygenväg | Resultat |
|:--- |:--- |
| V |Öppnar **Visa-menyn.** |
| W |Öppnar en ny StorSimple Snapshot **Jobs** Manager-konsol med fokus på jobbnoden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar sidan **Exportera lista.** |
| H |Öppnar onlinehjälp |

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att ansluta och hantera enheter](storsimple-snapshot-manager-manage-devices.md).

