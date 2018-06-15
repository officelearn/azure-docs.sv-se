---
title: Användargränssnittet för StorSimple Snapshot Manager | Microsoft Docs
description: Beskriver användargränssnittet för StorSimple Snapshot Manager och hur du hantera säkerhetskopieringsjobb och katalogen för säkerhetskopieringen.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: ''
ms.openlocfilehash: b48c507e38eb7cadff56259f617e336e4efe5708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927952"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Använd StorSimple Snapshot Manager-gränssnittet för att hantera säkerhetskopieringsjobb och säkerhetskopieringskatalogen

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager har ett intuitivt användargränssnitt som du kan använda för att ta och hantera säkerhetskopior. Den här självstudiekursen innehåller en introduktion till användargränssnittet och förklarar hur du använder varje komponent. En detaljerad beskrivning av StorSimple Snapshot Manager finns [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konsolen beskrivning
Klicka på ikonen StorSimple Snapshot Manager på skrivbordet för att visa användargränssnittet. Konsolfönstret visas som i följande bild.

![Fönster för StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Konsolfönstret har fem viktiga element. Klicka på länken för en fullständig beskrivning av varje element.

* [Menyraden](#menu-bar) 
* [Verktygsfältet](#tool-bar) 
* [Omfattning](#scope-pane) 
* [Resultatfönstret](#results-pane) 
* [Åtgärdsfönstret](#actions-pane) 

Dessutom har stöd för StorSimple Snapshot Manager [tangentbord navigering och ett antal genvägar](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konsolen hjälpmedel
Användargränssnittet för StorSimple Snapshot Manager stöder dessa funktioner som tillhandahålls av operativsystemet Windows och Microsoft Management Console (MMC), såväl som vissa kortkommandon för StorSimple Snapshot Manager – specifika. 

* Gå till en beskrivning av hjälpmedelsfunktionerna Windows [kortkommandon för Windows](https://support.microsoft.com/kb/126449). 
* Gå till en beskrivning av hjälpmedelsfunktionerna MMC [hjälpmedel för MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* En beskrivning av hjälpmedelsfunktionerna StorSimple Snapshot Manager går du till [tangentbord navigering och genvägar](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Menyraden
Menyraden överst i konsolfönstret innehåller [filen](#file-menu), [åtgärd](#action-menu), [visa](#view-menu), [Favoriter](#favorites-menu), [fönstret](#window-menu), och [hjälp](#help-menu) menyer.

Klicka på ett objekt i menyraden för att visa en lista över tillgängliga kommandon på menyn. I följande exempel visas den **visa** menyn som valts på menyraden.

![Visa-menyn som valts](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Arkivmenyn
Den **filen** menyn innehåller standardkommandon för Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Menyåtkomst
Visa den **filen** -menyn klickar du på **filen** på menyraden. Följande meny visas.

![StorSimple Snapshot Manager Arkiv-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs objekt som visas på den **filen** menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Ny |Klicka på **ny** att skapa en ny konsol baserat på StorSimple Snapshot Manager. |
| Öppet |Klicka på **öppna** att öppna en befintlig konsol. |
| Spara |Klicka på **spara** att spara den aktuella konsolen. |
| Spara som |Klicka på **Spara som** att skapa en ny, bytt namn till instans av den aktuella konsolen. Använd den **Spara som** möjlighet att anpassa en vy och spara den för senare hämtning. Du kan till exempel skapa StorSimple Snapshot Manager snapin-moduler som pekar på specifika servrar. |
| Lägg till/ta bort snapin-modul |Klicka på **Lägg till/ta bort snapin-modulen** att lägga till eller ta bort snapin-moduler och för att ordna noder i den **omfång** fönstret. Mer information finns på [Lägg till, ta bort och ordna snapin-moduler och tillägg i MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Alternativ |Klicka på **alternativ** ange åtkomstlägen för användare och behörigheter om du vill ändra ikonen konsolen, eller ta bort konsolfiler för att öka mängden tillgängligt diskutrymme. |
| Lista över sökvägar |Klicka på en sökväg i numrerade listan för att öppna en fil som du nyligen öppnat. |
| Avsluta |Klicka på **avsluta** att stänga den **filen** menyn. |

### <a name="action-menu"></a>Åtgärd-menyn
Använd den **åtgärd** menyn för att välja från tillgängliga åtgärder. Objekt som är tillgängliga beror på vad du gör i den **omfång** rutan eller **resultat** fönstret.

#### <a name="menu-access"></a>Menyåtkomst
Visa den **åtgärd** -menyn, gör du något av följande:

* Högerklicka på ett objekt i den **omfång** rutan eller **resultat** fönstret.
* Välj ett objekt i den **omfång** rutan eller **resultat** rutan och klicka sedan på **åtgärd** på menyraden. 

Om du väljer den översta noden i till exempel den **omfång** fönstret och sedan högerklicka eller klicka **åtgärd** följande meny visas i menyraden.

![StorSimple Snapshot Manager Åtgärd-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Den **åtgärder** rutan (till höger i konsolen) innehåller samma lista med åtgärder som den **åtgärd** menyn. Dessutom den **åtgärder** rutan innehåller den **visa** menyalternativ som gör att du kan skapa en anpassad vy av den **resultat** fönstret.

![Öppna i åtgärdsfönstret med Visa-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menyn Beskrivning
Följande tabell innehåller en alfabetisk lista över åtgärder för StorSimple Snapshot Manager. 

* Den **åtgärd** kolumnen visas åtgärder som du kan utföra på noder och resultat. 
* Den **navigering** kolumnen beskrivs hur du visar rätt **åtgärd** menyn så att du kan välja åtgärden. Vissa åtgärder visas i flera **åtgärd** menyer. För dessa åtgärder, väljer du ett **navigering** alternativ från listan. 
* Den **beskrivning** kolumnen beskriver hur du använder varje åtgärd på den **åtgärd** -menyn eller åtgärdsfönstret och förklarar hur det fungerar.

> [!NOTE]
> Den **åtgärder** rutan och **åtgärd** menyer innehåller fler alternativ, till exempel **visa**, **nytt fönster härifrån**,  **Uppdatera**, **Exportera lista**, och **hjälp**. Dessa alternativ är tillgängliga som en del av MMC och är inte specifika för StorSimple Snapshot Manager. Tabellen innehåller beskrivningar av dessa alternativ.
> 
> 

| Åtgärd | Navigering | Beskrivning |
|:--- |:--- |:--- |
| Autentisera |Klicka på den **enheter** nod, och högerklicka på en enhet i den **resultat** fönstret. |Klicka på **autentisera** så att lösenordet som du konfigurerade för enheten. |
| Klona |Expandera **säkerhetskopieringskatalogen**, expandera **Molnögonblicksbilder**, klicka på en säkerhetskopia av datum och välj sedan en volym i den **resultat** fönstret. |Klicka på **klona** att skapa en kopia av en ögonblicksbild i molnet och lagra den på en plats som du har angett. |
| Konfigurera en enhet |Högerklicka på den **enheter** nod. |Klicka på **konfigurera en enhet** så här konfigurerar du en enstaka enhet eller flera enheter att ansluta till Windows-värd. |
| Skapa princip för säkerhetskopiering |Gör något av följande:<ul><li>Högerklicka på **Säkerhetskopieringsprinciper**.</li><li>Klicka eller expandera **volym grupper**, högerklicka på en volym-grupp.</li><li>Klicka på eller expandera **säkerhetskopieringskatalog**, högerklicka på en volym-grupp.</li></ul> |Klicka på **skapa säkerhetskopieringsprincip** så här konfigurerar du en schemalagd säkerhetskopiering för en grupp för volymen. |
| Skapa volymen grupp |Gör något av följande:<ul><li>Klicka på den **volymer** nod, och högerklicka sedan på en volym i den **resultat** fönstret.</li><li>Högerklicka på den **volym grupper** nod.</li></ul> |Klicka på **skapa volymen grupp** tilldela volymer till en volym-grupp. |
| Ta bort |Klicka på en nod eller ett resultat (det här objektet visas i många **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **ta bort** att ta bort nod eller resultat som du har valt. När dialogrutan visas bekräfta eller avbryta borttagningen. |
| Information |Klicka på den **enheter** nod, och högerklicka sedan på en enhet i den **resultat** fönstret. |Klicka på **information** att visa konfigurationsinformation för en enhet. |
| Redigera |Klicka på **Säkerhetskopieringsprinciper**, och högerklicka sedan på en princip i den **resultat** fönstret. |Klicka på **redigera** att ändra schemat för säkerhetskopiering för en grupp för volymen. |
| Exportera lista |Klicka på en nod eller resultatet (det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **exportera listan** att spara en lista i en fil med kommaavgränsade värden (CSV). Du kan sedan importera den här filen i ett kalkylbladsprogram för analys. |
| Hjälp |Klicka på en nod eller ett resultat. (Det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **hjälp** öppna onlinehjälpen i ett nytt webbläsarfönster. |
| Nytt fönster härifrån |Klicka på en nod eller resultatet (det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **nytt fönster härifrån** att öppna ett nytt fönster för StorSimple Snapshot Manager. |
| Uppdatera |Klicka på en nod eller resultatet (det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **uppdatera** att uppdatera fönstret som visas StorSimple Snapshot Manager. |
| Uppdatera enheten |Klicka på den **enheter** nod, och högerklicka på en enhet i den **resultat** fönstret. |Klicka på **uppdatera enheten** att synkronisera en specifik ansluten enhet med StorSimple Snapshot Manager. |
| Uppdatera enheter |Högerklicka på den **enheter** nod. |Klicka på **uppdatera enheter** att synkronisera listan med anslutna enheter med StorSimple Snapshot Manager. |
| Skanna volymer |Högerklicka på den **volymer** nod. |Klicka på **skanna volymer** att uppdatera listan över volymer som visas i den **resultat** fönstret. |
| Återställ |Expandera **säkerhetskopieringskatalogen**, expandera en volym-grupp, expandera **lokala ögonblicksbilder** eller **Molnögonblicksbilder**, högerklicka på en säkerhetskopia. |Klicka på **återställa** att ersätta den aktuella volymen gruppera data med data från den valda säkerhetskopian. |
| Säkerhetskopiera |Gör något av följande:<ul><li>Expandera **volym grupper**, högerklicka på en volym-grupp.</li><li>Expandera **säkerhetskopieringskatalog**, högerklicka på en volym-grupp.</li></ul> |Klicka på **ta säkerhetskopiering** att starta en säkerhetskopiering direkt. |
| Växla import visning |Högerklicka på den översta noden i den **omfång** fönstret (den **StorSimple Snapshot Manager** nod i exempel). |Klicka på **växla import visa** att visa eller dölja volym grupper och tillhörande säkerhetskopior som har importerats från StorSimple Enhetshanteraren service instrumentpanelen. |

### <a name="view-menu"></a>Visa-menyn
Använd den **visa** menyn för att skapa en anpassad vy av den **resultat** fönstret innehållet. Den **visa** menyn innehåller **Lägg till/ta bort kolumner** och **anpassa** alternativ.

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt den **visa** på menyn liggande eller i den **åtgärder** fönstret.

![StorSimple Snapshot Manager Visa-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs objekt som visas på den **visa** menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Lägg till/ta bort kolumner |Klicka på **Lägg till/ta bort kolumner** att lägga till eller ta bort kolumner i den **resultat** fönstret. |
| Anpassa |Klicka på **anpassa** att visa eller dölja objekt i konsolfönstret för StorSimple Snapshot Manager-. |

### <a name="favorites-menu"></a>Favoriter-menyn
Använd den **Favoriter** menyn för att lägga till, ta bort och ordna sidan vyer och uppgifter som du använder ofta. 

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt den **Favoriter** menyn på menyraden.

![StorSimple Snapshot Manager Favoriter-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs objekt som visas på den **Favoriter** menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Lägg till |Klicka på **Lägg till** att lägga till den aktuella vyn i Favoriter. |
| Ordna Favoriter |Klicka på **Ordna Favoriter** att organisera innehållet i mappen Favoriter. |

### <a name="window-menu"></a>Fönster-menyn
Använd den **fönstret** menyn för att lägga till och ändra StorSimple Snapshot Manager konsolfönster.

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt den **fönstret** menyn på menyraden.

![StorSimple Snapshot Manager fönster-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Numrerad lista längst ned i menyn som visas i windows som för tillfället är öppna. Klicka på ett fönster i listan att sätta fönstret i förgrunden. 

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs de objekt som visas på menyn fönster.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Nytt fönster |Klicka på **nytt fönster** att öppna ett nytt konsolfönster (förutom befintliga fönstret). |
| Överlappande |Klicka på **Cascade** ska visas i öppna konsolfönstret i en övergripande formatmall. |
| Sida vid sida |Klicka på **panelen vågrätt** ska visas i öppna konsolfönstret i formatet sida vid sida (eller rutnät). |
| Ordna ikoner |Om du har flera konsolen windows öppna och utspridda över skrivbordet, minimera dem och klickar sedan på **Ordna ikoner** att ordna dem i en vågrät rad längst ned på skärmen. |

### <a name="help-menu"></a>Hjälp-menyn
Använd den **hjälp** menyn för att visa tillgängliga onlinehjälpen för StorSimple Snapshot Manager och MMC. Du kan också visa information om MMC och StorSimple Snapshot Manager programvaruversioner som är installerade på datorn. 

Du kan komma åt den **hjälp** menyn på menyraden. Du kan också komma åt hjälpavsnitt för StorSimple Snapshot Manager från den **åtgärder** fönstret.

![StorSimple Snapshot Manager hjälp-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs objekt som visas på Hjälp-menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Hjälp om StorSimple Snapshot Manager |Klicka på **hjälp på StorSimple Snapshot Manager** öppna StorSimple Snapshot Manager hjälp i ett separat fönster. |
| Hjälpavsnitt |Klicka på **hjälpavsnitt** öppna MMC onlinehjälpen i ett separat fönster. |
| TechCenter-webbplats |Klicka på **TechCenter webbplats** att öppna startsidan för Microsoft TechNet Tech Center i ett separat fönster. |
| Om Microsoft Management Console |Klicka på **om Microsoft Management Console** att se vilken version av Microsoft Management Console har installerats på datorn. |
| Om StorSimple Snapshot Manager |Klicka på **om StorSimple Snapshot Manager** att se vilken version av snapin-modulen är installerad på datorn. |

## <a name="tool-bar"></a>Verktygsfältet
Verktygsfältet under menyraden innehåller navigerings- och ikoner. Varje ikon är en genväg till en viss uppgift.

### <a name="icon-descriptions"></a>Ikonen beskrivningar
I följande tabell beskrivs ikonerna som visas i verktygsfältet. 

| Ikon | Beskrivning |
|:--- |:--- |
| ![VÄNSTERPIL](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klicka på vänsterpilen för att återgå till föregående sida. |
| ![HÖGERPIL](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klicka på högerpilen för att gå till nästa sida (om pilen grå åtgärden är inte tillgänglig). |
| ![Upp-ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klicka på ikonen dig gå upp en nivå i konsolträdet (den **omfång** fönstret). |
| ![Visa/dölj konsolträd](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klicka på ikonen Visa/Dölj konsolen trädet om du vill visa eller Dölj den **omfång** fönstret. |
| ![Exportera lista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klicka på listexporteringsikonen om du vill exportera en lista till en CSV-fil som du anger. |
| ![Hjälpikonen](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klicka på hjälpikonen för att öppna en MMC-hjälpavsnittet. |
| ![Visa/Dölj åtgärdsfönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klicka på Visa/dölj **åtgärder** fönstret ikon för att visa eller Dölj den **åtgärder** fönstret. |

## <a name="scope-pane"></a>Omfattning
Den **omfång** är längst till vänster i StorSimple Snapshot Manager UI. Det innehåller trädet konsolen (eller noden) och är den primära navigering mekanismen för StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktur för scope-fönstret
Den **omfång** rutan innehåller en serie klickbara objekt (noder) i en trädstruktur. 

![Omfattning](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Om du vill expandera eller komprimera en nod, klickar du på ikonen pilen bredvid namnet på noden.
* Om du vill visa status eller innehållet i en nod, klickar du på noden namnet. Informationen visas i den **resultat** fönstret. 

Den **omfång** rutan innehåller följande noder: 

* [Enhetsnoden](#devices-node) 
* [Volymer nod](#volumes-node) 
* [Volymen Gruppnod](#volume-groups-node) 
* [Säkerhetskopiera noden policyer](#backup-policies-node) 
* [Säkerhetskopiera katalogen nod](#backup-catalog-node) 
* [Jobb nod](#jobs-node) 

### <a name="scope-pane-tasks"></a>Omfång rutan uppgifter
Du kan använda den **omfång** fönstret för att slutföra en åtgärd på en viss nod. Om du vill markera en aktivitet, gör du något av följande:

* Högerklicka på noden och välj sedan aktiviteten från menyn som visas.
* Klicka på noden och klicka sedan på **åtgärd** på menyraden. Välj aktiviteten från menyn som visas.
* Klicka på noden och sedan väljer du åtgärden i den **åtgärder** fönstret.

När du väljer en nod och använda någon av dessa metoder för att se en uppgiftslista visas bara de åtgärder som kan utföras på noden.

### <a name="devices-node"></a>Enhetsnoden
Den **enheter** nod representerar StorSimple-enheter och virtuella StorSimple-enheter som är anslutna till StorSimple Snapshot Manager. Välj den här noden för att ansluta och konfigurera en enhet och importera dess associerade volymer, volymer grupper och befintliga säkerhetskopior. Flera enheter kan anslutas till en enda värd.

* Om du vill expandera noden, klicka på pilikonen bredvid **enheter**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **enheter** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Klicka för att visa en lista över konfigurerade enheter **enheter** i den **omfång** fönstret. Lista över enheter, tillsammans med information om varje enhet visas i den **resultat** fönstret.

### <a name="volumes-node"></a>Volymer nod
Den **volymer** noden representerar enheter som är kopplade till volymer som monterats av värden, inklusive de som identifierats via iSCSI och de identifieras via en enhet. Använd den här noden för att visa listan över tillgängliga volymer och tilldela enskilda volymer till volymen grupper.

* Om du vill expandera noden, klicka på pilikonen bredvid **volymer**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **volymer** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Klicka för att visa en lista över volymer **volymer** i den **omfång** fönstret. Lista över volymer, tillsammans med information om varje volym visas i den **resultat** fönstret.

### <a name="volume-groups-node"></a>Volymen Gruppnod
Volymen grupper kallas även konsekvenskontroll grupper. Varje volym är en pool med programrelaterad volymer som hjälper till att garantera programmets konsekvens under säkerhetskopieringsåtgärder. Använd den **volym grupper** nod för att konfigurera dessa grupper och för att göra säkerhetskopior av interaktiva eller skapa scheman för säkerhetskopiering. 

* Om du vill expandera noden, klicka på pilikonen bredvid **volym grupper**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **volym grupper** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Klicka för att visa en lista över volymen grupper **volym grupper** i den **omfång** fönstret. Listan över grupper som volymen, tillsammans med information om varje volym gruppen visas i den **resultat** fönstret.

### <a name="backup-policies-node"></a>Säkerhetskopiera noden policyer
Principer för säkerhetskopiering är jobbscheman för lokala och molnbaserade ögonblicksbilder. Använd den **Säkerhetskopieringsprinciper** noden för att ange hur ofta en säkerhetskopia skapas och hur länge en säkerhetskopiering ska behållas. 

* Om du vill expandera noden, klicka på pilikonen bredvid **Säkerhetskopieringsprinciper**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **Säkerhetskopieringsprinciper** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Klicka för att visa en lista över principer för säkerhetskopiering **Säkerhetskopieringsprinciper** i den **omfång** fönstret. Lista över principer för säkerhetskopiering, tillsammans med information om varje princip visas i den **resultat** fönstret.

> [!NOTE]
> Du kan lagra högst 64 säkerhetskopieringar.


### <a name="backup-catalog-node"></a>Säkerhetskopiera katalogen nod
Den **säkerhetskopieringskatalog** nod innehåller listor över på plats och extern säkerhetskopieringar av Azure StorSimple-volymer. Den här noden är ordnad efter volym grupp, och varje grupp volymbehållare innehåller separata strukturer för lokala ögonblicksbilder (den **lokal ögonblicksbild**s nod) och molnbaserade ögonblicksbilder (den **Molnögonblicksbilder** nod). När expanderats visas varje grupp volymbehållare alla lyckade säkerhetskopior som gjorts interaktivt eller av en konfigurerade principen.

* Om du vill expandera noden, klicka på pilikonen bredvid **säkerhetskopieringskatalog**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **säkerhetskopieringskatalog** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Klicka för att visa en lista över ögonblicksbilder av säkerhetskopior **säkerhetskopieringskatalogen** i den **omfång** fönstret. Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild som visas i den **resultat** fönstret.

### <a name="local-snapshots-node"></a>Lokala ögonblicksbilder noden
Den **lokala ögonblicksbilder** innehåller en lista med lokala ögonblicksbilder för en viss volym-grupp. Noden finns under den **säkerhetskopieringskatalog** nod i den **omfång** fönstret. Lokala ögonblicksbilder är i tidpunkt kopior av volymdata som är lagrade på Azure StorSimple-enheten. Normalt kan den här typen av säkerhetskopiering skapas och återställs snabbt. Du kan använda en lokal ögonblicksbild som en lokal säkerhetskopia.

* Om du vill expandera noden, klicka på pilikonen bredvid **lokala ögonblicksbilder**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **lokala ögonblicksbilder** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Klicka för att visa en lista över lokala ögonblicksbilder **lokala ögonblicksbilder** i den **omfång** fönstret. Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild som visas i den **resultat** fönstret.

### <a name="cloud-snapshots-node"></a>Molnet ögonblicksbilder nod
Den **Molnögonblicksbilder** innehåller en lista med molnögonblicksbilder för en viss volym-grupp. Noden finns under den **säkerhetskopieringskatalog** nod i den **omfång** fönstret. Molnögonblicksbilder är i tidpunkt kopior av volymens data som lagras i molnet. En ögonblicksbild i molnet är detsamma som en ögonblicksbild replikeras på ett annat, externt lagringssystem. Molnögonblicksbilder är särskilt användbart i katastrofåterställning.

* Om du vill expandera noden, klicka på pilikonen bredvid **Molnögonblicksbilder**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **Molnögonblicksbilder** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Klicka för att visa en lista över molnögonblicksbilder **Molnögonblicksbilder** i den **omfång** fönstret. Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild som visas i den **resultat** fönstret.

### <a name="jobs-node"></a>Jobb nod
Den **jobb** nod innehåller information om schemalagda körs och nyligen utförda säkerhetskopieringsjobb. 

* Om du vill expandera noden, klicka på pilikonen bredvid **jobb**.
* Om du vill se en meny med tillgängliga åtgärder, högerklicka på den **jobb** nod eller högerklicka på något av de noder som visas i den utökade vyn.
* Om du vill se en lista över schemalagda jobb, expandera den **jobb** noden och klicka sedan på **schemalagda**. Lista över tidigare konfigurerade jobb och information om varje jobb visas i den **resultat** fönstret. 
* Om du vill se en lista över nyligen slutförda jobb, expandera den **jobb** noden och klicka sedan på **senaste 24 timmarna**. En lista över jobb som har slutförts under de senaste 24 timmarna visas i den **resultat** fönstret. Den **resultat** fönstret innehåller även information om varje slutförda jobb.
* Om du vill se en lista över jobb som körs för närvarande expanderar den **jobb** noden och klicka sedan på **kör**. Listan över jobb och information om varje jobb som körs visas i den **resultat** fönstret.

## <a name="results-pane"></a>Resultatfönstret
Den **resultat** är den mittersta rutan i StorSimple Snapshot Manager UI. Den innehåller listor och detaljerad statusinformation för noden som du har valt i den **omfång** fönstret.

### <a name="example"></a>Exempel
I följande exempel visas klickar du på den **volym grupper** nod i den **omfång** fönstret. Den **resultat** rutan visar en lista över volymen grupper med information om varje grupp.

![Resultatfönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Du kan konfigurera information som visas i den **resultat** rutan: högerklickar du på en nod i den **Scope** rutan klickar du på **visa**, och klicka sedan på **Lägg till/ta bort kolumner** .

## <a name="actions-pane"></a>Åtgärdsfönstret
Den **åtgärder** är den högra rutan i StorSimple Snapshot Manager UI. Den innehåller en meny med åtgärder som du kan utföra på noden, visa eller data som du väljer i den **omfång** rutan eller **resultat** fönstret. Den **åtgärder** rutan innehåller såväl samma kommandon som den **åtgärd** menyer som är tillgängliga för objekt i den **omfång** rutan och **resultat** fönstret. Se tabellen i en beskrivning av varje åtgärd i **åtgärd** menyn avsnitt.

### <a name="examples"></a>Exempel
Se följande exempel i den **omfång** rutan Expandera den **jobb** och klickar på **schemalagda**. Den **åtgärder** fönstret tillgängliga åtgärder för den **schemalagda** nod.

![Åtgärder exempel i schemalagda jobb](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Se fler alternativ i den **omfång** rutan Expandera den **jobb** nod, klickar du på **schemalagda**, och klicka sedan på ett schemalagt jobb i den **resultat** fönstret. Den **åtgärder** tillgängliga åtgärder för det schemalagda jobbet fönstret som visas i följande exempel.

![Åtgärdsfönstret jobbåtgärder exempel](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Tangentbordsnavigering och genvägar
StorSimple Snapshot Manager gör hjälpmedelsfunktionerna i Windows-operativsystemet och Microsoft Management Console (MMC). Den omfattar också vissa tangentbord navigeringsfunktionerna och genvägar som är specifika för StorSimple Snapshot Manager enligt beskrivningen i följande avsnitt.

* [Tangenter för navigering](#keyboard-navigation-keys) 
* [Menyraden kortkommandon](#menu-bar-shortcut-keys) 
* [Kortkommandon för scope-fönstret](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Tangenter för navigering
I följande tabell beskrivs de nycklar som du kan använda för att navigera StorSimple Snapshot Manager-användargränssnittet. 

| Navigering nyckel | Åtgärd |
|:--- |:--- |
| Nedåtpil |Använd nedpilen för att flytta lodrätt till nästa element i en meny eller fönstret. |
| Ange |Tryck på RETUR för att slutföra en åtgärd och sedan fortsätta till nästa steg. Du kan till exempel trycka på RETUR för att välja **nästa**, **OK**, eller **skapa**, och gå sedan till nästa steg i guiden. |
| ESC |Tryck på Esc för att stänga en meny eller för att avbryta och stänga en sida. |
| F1 |Tryck på F1 för att visa ett hjälpavsnitt för det aktiva fönstret. |
| F5 |Tryck på F5 för att uppdatera en nod. |
| F6 |Tryck på F6 för att flytta från den **omfång** att den **resultat** fönstret. |
| F10 |Tryck på F10 för att gå till menyraden. |
| VÄNSTERPIL |Använd vänsterpil om du vill flytta vågrätt från ett menyalternativ för fältet till föregående alternativ. Menyn åtgärd (eller kontext) för föregående objekt visas när du flyttar till föregående objekt på menyraden. |
| HÖGERPIL |Använd högerpilen för att flytta vågrätt från-fältet för ett menyalternativ till nästa. När du flyttar till nästa element på menyraden visas menyn åtgärd (eller kontext) för det nya objektet. |
| TABB-tangenten |Använda tabbtangenten för att flytta till nästa fönsterruta på konsolen eller till nästa val eller text i en sida. |
| UPPIL |Använd dig för om du vill flytta lodrätt till föregående objekt på en meny eller fönstret. |

### <a name="menu-bar-shortcut-keys"></a>Menyraden kortkommandon
I följande tabell beskrivs kortkommandon för menyraden. När du trycker på genvägen och menyn öppnas, kan du använda menyn kortkommandon (understrukna nycklarna på menyn). Mer information om menyraden går du till [menyraden](#menu-bar).

| Genväg | Resultat | Menyn kortkommando | Resultat |
|:--- |:--- |:--- |:--- |
| ALT + F |Öppnar den **filen** menyn. |N |Öppnar en ny instans i konsolen. |
|  |O |Öppnar den **Administrationsverktyg** sidan. | |
|  |S |Sparar StorSimple Snapshot Manager-konsolen. | |
|  |A |Öppnar den **Spara som** sidan. | |
|  |M |Öppnar den **Lägg till/ta bort snapin-modulen** sidan. | |
|  |P |Öppnar den **alternativ** sidan. | |
|  |H |Öppnar Hjälp online. | |
| ALT + A |Öppnar den **åtgärd** menyn. |I |Aktiverar alternativet Importera visas på och stänga av. |
|  |W |Öppnar en ny StorSimple Snapshot Manager-konsolen. | |
|  |F |Uppdaterar StorSimple Snapshot Manager-konsolen. | |
|  |L |Öppnar den **exportera listan** sidan. | |
|  |H |Öppnar Hjälp online. | |
| ALT + V |Öppnar den **visa** menyn. |A |Öppnar den **Lägg till/ta bort kolumner** sidan. |
|  |U |Öppnar den **Anpassa vy** sidan. | |
| ALT + O |Öppnar den **Favoriter** menyn. |A |Öppnar den **Lägg till** sidan. |
|  |O |Öppnar den **Ordna Favoriter** sidan. | |
| ALT + W |Öppnar den **fönstret** menyn. |N |Öppnar ett annat fönster för StorSimple Snapshot Manager. |
|  |C |Visar alla öppna konsolfönstret i en övergripande formatmall. | |
|  |T |Visar alla öppna konsolfönstret i ett rutnät. | |
|  |I |Ordnar ikonerna i en vågrät rad längst ned på skärmen. | |
| ALT + H |Öppnar den **hjälp** menyn. |H |Öppnar Hjälp online. |
|  |T |Öppnar sidan Microsoft TechNet Tech Center. | |
|  |A |Öppnar den **om Microsoft Management Console** sidan. | |

### <a name="scope-pane-shortcut-keys"></a>Kortkommandon för scope-fönstret
Följande tabeller visar genvägen tangentkombinationer för varje nod i den **omfång** fönstret. 

* [Kortkommandon för enheter nod](#devices-node-shortcut-keys)
* [Kortkommandon för volymer nod](#volumes-node-shortcut-keys)
* [Kortkommandon för volymen grupper nod](#volume-groups-node-shortcut-keys)
* [Kortkommandon för säkerhetskopiering principer nod](#backup-policies-node-shortcut-keys)
* [Kortkommandon för säkerhetskopiering katalog nod](#backup-catalog-node-shortcut-keys)
* [Kortkommandon för jobb nod](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Kortkommandon för enheter nod
| Genväg till meny | Resultat |
|:--- |:--- |
| C |Öppnar den **konfigurera en enhet** sidan. |
| D |Uppdaterar listan över enheter och enhetsinformation. |
| V |Öppnar den **visa** menyn. |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol som fokuserar på de **information** nod. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **exportera listan** sidan. |
| H |Öppnar Hjälp online. |

#### <a name="volumes-node-shortcut-keys"></a>Kortkommandon för volymer nod
| Genväg till meny | Resultat |
|:--- |:--- |
| V |Uppdaterar listan över volymer. |
| V (tryck två gånger på) |Öppnar den **visa** menyn. |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol som fokuserar på de **volymer** nod. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **exportera listan** sidan. |
| H |Öppnar Hjälp online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Kortkommandon för volymen grupper nod
| Genväg till meny | Resultat |
|:--- |:--- |
| G |Öppnar den **skapa en volym grupp** sidan. |
| V |Öppnar den **visa** menyn. |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol som fokuserar på de **volym grupper** nod. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **exportera listan** sidan. |
| H |Öppnar Hjälp online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Kortkommandon för säkerhetskopiering principer nod
| Genväg till meny | Resultat |
|:--- |:--- |
| B |Öppnar den **skapa en princip** sidan. |
| V |Öppnar den **visa** menyn. |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol som fokuserar på de **volym grupper** nod. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den ** exportera listan ** sidan. |
| H |Öppnar Hjälp online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Kortkommandon för säkerhetskopiering katalog nod
| Genväg till meny | Resultat |
|:--- |:--- |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol som fokuserar på de **volym grupper** nod. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| H |Öppnar Hjälp online. |

#### <a name="jobs-node-shortcut-keys"></a>Kortkommandon för jobb nod
| Genväg till meny | Resultat |
|:--- |:--- |
| V |Öppnar den **visa** menyn. |
| W |Öppnar en ny StorSimple Snapshot Manager-konsol som fokuserar på de **jobb** nod. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **exportera listan** sidan. |
| H |Öppnar Hjälp online |

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använder StorSimple Snapshot Manager för att ansluta och hantera enheter](storsimple-snapshot-manager-manage-devices.md).

