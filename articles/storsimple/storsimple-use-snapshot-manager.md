---
title: Användargränssnitt för StorSimple Snapshot Manager | Microsoft Docs
description: Beskriver StorSimple Snapshot Manager-användargränssnittet och förklarar hur du använder den för att hantera säkerhetskopieringsjobb och säkerhetskopieringskatalogen.
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
ms.openlocfilehash: 46225e5a332e035e4d1cc256e71c4b5d8686fd47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845331"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Använd StorSimple Snapshot Manager-gränssnittet för att hantera säkerhetskopieringsjobb och säkerhetskopieringskatalogen

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager har ett intuitivt användargränssnitt som du kan använda för att ta och hantera säkerhetskopior. Den här självstudien innehåller en introduktion till användargränssnittet och förklarar hur du använder varje komponent. En detaljerad beskrivning av StorSimple Snapshot Manager finns i [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konsolen beskrivning
Klicka på ikonen StorSimple Snapshot Manager på skrivbordet för att visa användargränssnittet. Konsolfönstret visas enligt följande bild.

![Fönster för StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Konsolfönstret har fem viktiga element. Klicka på länken för en fullständig beskrivning av varje element.

* [Menyraden](#menu-bar) 
* [Verktygsfältet](#tool-bar) 
* [Omfattning](#scope-pane) 
* [Resultatfönstret](#results-pane) 
* [Åtgärdsfönstret](#actions-pane) 

Dessutom har stöd för StorSimple Snapshot Manager [tangentbord navigering och ett antal genvägar](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konsolen hjälpmedel
StorSimple Snapshot Manager-användargränssnittet stöder hjälpmedelsfunktionerna som tillhandahålls av operativsystemet Windows och Microsoft Management Console (MMC), samt vissa StorSimple Snapshot Manager-specifika kortkommandon. 

* En beskrivning av hjälpmedelsfunktionerna Windows går du till [kortkommandon för Windows](https://support.microsoft.com/kb/126449). 
* Gå till en beskrivning av hjälpmedelsfunktionerna MMC [hjälpmedel för MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* En beskrivning av hjälpmedelsfunktionerna StorSimple Snapshot Manager går du till [tangentbord navigering och genvägar](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Menyraden
Menyraden överst i konsolfönstret innehåller [filen](#file-menu), [åtgärd](#action-menu), [visa](#view-menu), [Favoriter](#favorites-menu), [fönstret](#window-menu), och [hjälpa](#help-menu) menyer.

Klicka på ett objekt i menyraden för att se en lista över tillgängliga kommandon på menyn. I följande exempel visas den **visa** menyn som valts på menyraden.

![Visa-menyn som valts](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Arkivmenyn
Den **filen** menyn innehåller standardkommandon för Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Menyåtkomst
Visa den **filen** -menyn klickar du på **filen** på menyraden. Följande meny visas.

![StorSimple Snapshot Manager Arkiv-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs vilka objekt som visas på den **filen** menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Ny |Klicka på **New** att skapa en ny konsol baserat på StorSimple Snapshot Manager. |
| Öppet |Klicka på **öppna** att öppna en befintlig konsol. |
| Spara |Klicka på **spara** att spara den aktuella konsolen. |
| Spara som |Klicka på **Spara som** att skapa en ny, omdöpt instans av den aktuella konsolen. Använd den **Spara som** möjlighet att anpassa en vy och spara den för senare hämtning. Du kan till exempel skapa StorSimple Snapshot Manager snapin-moduler som pekar på specifika servrar. |
| Lägg till/ta bort snapin-modulen |Klicka på **Lägg till/ta bort snapin-modulen** att lägga till eller ta bort snapin-moduler och organisera noder i den **omfång** fönstret. Mer information går du till [Lägg till, ta bort, och ordna snapin-moduler och tillägg i MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Alternativ |Klicka på **alternativ** om du vill ändra ikonen konsolen ange Åtkomstlägen som användare och behörigheter eller ta bort konsolfiler för att öka ledigt diskutrymme. |
| Lista över sökvägar |Klicka på en sökväg i numrerade listan för att öppna en fil som du nyligen öppnat. |
| Avsluta |Klicka på **avsluta** att Stäng den **filen** menyn. |

### <a name="action-menu"></a>Åtgärd-menyn
Använd den **åtgärd** menyn för att välja från tillgängliga åtgärder. Vilka objekt som är tillgängliga beror på det val du gör i den **omfång** fönstret eller **resultat** fönstret.

#### <a name="menu-access"></a>Menyåtkomst
Visa den **åtgärd** menyn, Välj en av följande:

* Högerklicka på ett objekt i den **omfång** fönstret eller **resultat** fönstret.
* Markera ett objekt i den **omfång** fönstret eller **resultat** fönstret och klicka sedan på **åtgärd** på menyraden. 

Exempel: Om du väljer den översta noden i den **omfång** fönstret och sedan högerklicka eller klicka **åtgärd** följande meny visas i menyraden.

![StorSimple Snapshot Manager Åtgärd-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Den **åtgärder** (till höger i konsolen) innehåller samma lista med åtgärder som den **åtgärd** menyn. Dessutom den **åtgärder** fönstret innehåller den **visa** menyalternativ, vilket gör det möjligt att skapa en anpassad vy av den **resultat** fönstret.

![Öppna i åtgärdsfönstret med Visa-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell innehåller en alfabetisk lista med StorSimple Snapshot Manager-åtgärder. 

* Den **åtgärd** kolumnen visas åtgärder som du kan utföra på noder och resultat. 
* Den **navigering** kolumnen beskrivs hur du visar rätt **åtgärd** menyn så att du kan välja åtgärden. Vissa åtgärder visas i flera **åtgärd** menyer. För dessa åtgärder, Välj en **navigering** alternativ från listan. 
* Den **beskrivning** kolumnen beskriver hur du använder varje åtgärd på den **åtgärd** menyn eller åtgärdsfönstret och som förklarar vad det gör.

> [!NOTE]
> Den **åtgärder** fönstret och **åtgärd** menyer innehåller ytterligare alternativ såsom **visa**, **nytt fönster härifrån**,  **Uppdatera**, **Exportera lista**, och **hjälpa**. Dessa alternativ är tillgängliga som en del i MMC och är inte specifika för StorSimple Snapshot Manager. Tabellen innehåller beskrivningar av dessa alternativ.
> 
> 

| Åtgärd | Navigering | Beskrivning |
|:--- |:--- |:--- |
| Autentisera |Klicka på den **enheter** noden och högerklicka på en enhet i den **resultat** fönstret. |Klicka på **autentisera** att ange lösenordet som du har konfigurerat för enheten. |
| Klona |Expandera **säkerhetskopieringskatalogen**, expandera **Molnögonblicksbilder**, klickar du på en daterat säkerhetskopia och välj sedan en volym i den **resultat** fönstret. |Klicka på **klona** att skapa en kopia av en ögonblicksbild i molnet och lagra den på en plats som du har angett. |
| Konfigurera en enhet |Högerklicka på den **enheter** noden. |Klicka på **konfigurera en enhet** att konfigurera en enskild enhet eller flera enheter att ansluta till Windows-värden. |
| Skapa säkerhetskopieringsprincip |Gör något av följande:<ul><li>Högerklicka på **principer för säkerhetskopiering**.</li><li>Klicka på eller expandera **Volymgrupper**, högerklicka på en volym-grupp.</li><li>Klicka på eller expandera **säkerhetskopieringskatalog**, högerklicka på en volym-grupp.</li></ul> |Klicka på **skapa Säkerhetskopieringsprincipen** att konfigurera en schemalagd säkerhetskopiering för en volym-grupp. |
| Skapa volymen grupp |Gör något av följande:<ul><li>Klicka på den **volymer** noden och högerklicka på en volym i den **resultat** fönstret.</li><li>Högerklicka på den **Volymgrupper** noden.</li></ul> |Klicka på **Create volym Group** tilldela volymer till en volym-grupp. |
| Ta bort |Klicka på en nod eller ett resultat (det här objektet visas på många **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **ta bort** att ta bort noden eller resultat som du har valt. När den bekräftande dialogrutan visas, bekräfta eller avbryta borttagningen. |
| Information |Klicka på den **enheter** noden och högerklicka på en enhet i den **resultat** fönstret. |Klicka på **information** kan se vilka konfigurationen för en enhet. |
| Redigera |Klicka på **principer för säkerhetskopiering**, högerklicka på en princip i den **resultat** fönstret. |Klicka på **redigera** att ändra schemat för säkerhetskopiering för en volym-grupp. |
| Exportera lista |Klicka på en nod eller resultat (det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **Exportera lista** att spara en lista i en fil med kommaavgränsade värden (CSV). Du kan sedan importera den här filen till ett kalkylbladsprogram för analys. |
| Hjälp |Klicka på en nod eller resultat. (Det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **hjälper** att öppna onlinehjälpen i ett separat webbläsarfönster. |
| Nytt fönster härifrån |Klicka på en nod eller resultat (det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **nytt fönster härifrån** att öppna ett nytt fönster för StorSimple Snapshot Manager. |
| Uppdatera |Klicka på en nod eller resultat (det här objektet visas på alla **åtgärd** menyer och **åtgärder** fönster.) |Klicka på **uppdatera** att uppdatera fönstret visas nu StorSimple Snapshot Manager. |
| Uppdatera enhet |Klicka på den **enheter** noden och högerklicka på en enhet i den **resultat** fönstret. |Klicka på **uppdatera enheten** att synkronisera en viss ansluten enhet med StorSimple Snapshot Manager. |
| Uppdatera enheter |Högerklicka på den **enheter** noden. |Klicka på **uppdatera enheter** att synkronisera din lista över anslutna enheter med StorSimple Snapshot Manager. |
| Genomsök igen volymer |Högerklicka på den **volymer** noden. |Klicka på **Genomsök igen volymer** att uppdatera listan över volymer som visas i den **resultat** fönstret. |
| Återställ |Expandera **säkerhetskopieringskatalogen**, expandera en volym-grupp, expandera **lokala ögonblicksbilder** eller **Molnögonblicksbilder**, högerklicka på en säkerhetskopia. |Klicka på **återställa** att ersätta den aktuella volymen gruppera data med data från den valda säkerhetskopian. |
| Gör säkerhetskopia |Gör något av följande:<ul><li>Expandera **Volymgrupper**, högerklicka på en volym-grupp.</li><li>Expandera **säkerhetskopieringskatalog**, högerklicka på en volym-grupp.</li></ul> |Klicka på **ta säkerhetskopiering** att starta ett säkerhetskopieringsjobb direkt. |
| Visa/Dölj import visning |Högerklicka på den översta noden i den **omfång** fönstret (den **StorSimple Snapshot Manager** nod i exemplen). |Klicka på **växla import visa** att visa eller dölja volymgrupper och tillhörande säkerhetskopior som har importerats från instrumentpanelen för StorSimple Device Manager-tjänsten. |

### <a name="view-menu"></a>Visa-menyn
Använd den **visa** menyn för att skapa en anpassad vy av den **resultat** fönstret innehållet. Den **visa** menyn innehåller **Lägg till/ta bort kolumner** och **anpassa** alternativ.

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt den **visa** på menyn fältet eller i den **åtgärder** fönstret.

![StorSimple Snapshot Manager Visa-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs vilka objekt som visas på den **visa** menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Lägg till/ta bort kolumner |Klicka på **Lägg till/ta bort kolumner** att lägga till eller ta bort kolumner i den **resultat** fönstret. |
| Anpassa |Klicka på **anpassa** att visa eller dölja objekt i StorSimple Snapshot Manager-konsolfönstret. |

### <a name="favorites-menu"></a>Favoriter-menyn
Använd den **Favoriter** menyn för att lägga till, ta bort och ordna sidvisningar och uppgifter som du använder ofta. 

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt den **Favoriter** menyn på menyraden.

![StorSimple Snapshot Manager Favoriter-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs vilka objekt som visas på den **Favoriter** menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Lägg till i Favoriter |Klicka på **Lägg till i Favoriter** att lägga till den aktuella vyn i listan över favoriter. |
| Ordna Favoriter |Klicka på **Ordna Favoriter** att ordna innehållet i mappen Favoriter. |

### <a name="window-menu"></a>Fönster-menyn
Använd den **fönstret** menyn för att lägga till och ordna om StorSimple Snapshot Manager-konsolfönstret.

#### <a name="menu-access"></a>Menyåtkomst
Du kan komma åt den **fönstret** menyn på menyraden.

![Fönster för StorSimple Snapshot Manager-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Numrerad lista längst ned i menyn visar det som för närvarande är öppna. Klicka på ett fönster i listan för att sätta i fönstret i förgrunden. 

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs de objekt som visas på menyn fönster.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Nytt fönster |Klicka på **nytt fönster** att öppna ett nytt konsolfönster (utöver det befintliga fönstret). |
| CASCADE |Klicka på **Cascade** att visa öppna konsolfönstret i ett sammanhängande format. |
| Sida vid sida |Klicka på **panelen vågrätt** att visa öppna konsolfönstret i en panel (eller grid)-format. |
| Ordna ikoner |Om du har flera konsolen windows öppna och utspridda över skrivbordet, minimera dem och klicka sedan på **Ordna ikoner** att ordna dem i en vågrät rad längst ned på skärmen. |

### <a name="help-menu"></a>Hjälp-menyn
Använd den **hjälpa** menyn för att visa tillgängliga onlinehjälpen för StorSimple Snapshot Manager och MMC. Du kan också visa information om MMC och StorSimple Snapshot Manager programvaruversioner som är installerade på datorn. 

Du kan komma åt den **hjälpa** menyn på menyraden. Du kan även använda StorSimple Snapshot Manager hjälpavsnitt från den **åtgärder** fönstret.

![StorSimple Snapshot Manager hjälp-menyn](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menyn Beskrivning
I följande tabell beskrivs vilka objekt som visas på Hjälp-menyn.

| Menyalternativ | Beskrivning |
|:--- |:--- |
| Hjälp om StorSimple Snapshot Manager |Klicka på **hjälp på StorSimple Snapshot Manager** att öppna StorSimple Snapshot Manager hjälp i ett separat fönster. |
| Hjälpavsnitt |Klicka på **hjälpavsnitt** att öppna MMC onlinehjälpen i ett separat fönster. |
| TechCenter Web Site |Klicka på **TechCenter webbplats** att öppna startsidan för Microsoft TechNet Tech Center i ett separat fönster. |
| Om Microsoft Management Console |Klicka på **om Microsoft Management Console** att se vilken version av Microsoft Management Console är installerad på datorn. |
| Om StorSimple Snapshot Manager |Klicka på **om StorSimple Snapshot Manager** att se vilken version av snapin-modulen är installerad på datorn. |

## <a name="tool-bar"></a>Verktygsfältet
Verktygsfältet under menyraden innehåller navigerings- och ikoner. Varje ikon är en genväg till en viss uppgift.

### <a name="icon-descriptions"></a>Ikonen beskrivningar
I följande tabell beskrivs de ikoner som visas i verktygsfältet. 

| Ikon | Beskrivning |
|:--- |:--- |
| ![VÄNSTERPIL](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klicka på ikonen pilen till vänster för att återgå till föregående sida. |
| ![HÖGERPIL](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klicka på högerpilen för att gå till nästa sida (om pilen grå åtgärden är inte tillgänglig). |
| ![Upp-ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klicka på ikonen för dig att gå upp en nivå i konsolträdet (den **omfång** fönstret). |
| ![Visa/dölj konsolträd](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klicka på ikonen Visa/Dölj konsolen trädet om du vill visa eller dölja den **omfång** fönstret. |
| ![Exportera lista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klicka på ikonen Exportera listan om du vill exportera en lista till en CSV-fil som du anger. |
| ![Hjälp-ikonen](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klicka på hjälpikonen för att öppna en MMC-onlinehjälpavsnittet. |
| ![Visa/Dölj åtgärdsfönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klicka på Visa/dölj **åtgärder** fönstret ikonen om du vill visa eller dölja den **åtgärder** fönstret. |

## <a name="scope-pane"></a>Omfattning
Den **omfång** är längst till vänster i StorSimple Snapshot Manager-UI. Det innehåller trädet konsolen (eller noden) och är den primära navigering mekanismen för StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Omfång fönstret struktur
Den **omfång** fönstret innehåller en serie klickbara objekt (noder) ordnade i en trädstruktur. 

![Omfattning](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Om du vill visa eller dölja en nod, klicka på pilikonen intill namnet på noden.
* Om du vill visa status eller innehållet i en nod, klickar du på namnet på noden. Informationen visas på den **resultat** fönstret. 

Den **omfång** fönstret innehåller följande noder: 

* [Enhetsnoden](#devices-node) 
* [Volymer nod](#volumes-node) 
* [Volymen Gruppnod](#volume-groups-node) 
* [Noden för principer för säkerhetskopiering](#backup-policies-node) 
* [Katalogen säkerhetskopieringsnod](#backup-catalog-node) 
* [Jobb-nod](#jobs-node) 

### <a name="scope-pane-tasks"></a>Omfång fönstret aktiviteter
Du kan använda den **omfång** fönstret för att slutföra en åtgärd på en viss nod. Om du vill markera en aktivitet, gör du något av följande:

* Högerklicka på noden och sedan väljer du uppgiften från menyn som visas.
* Klicka på noden och klicka sedan på **åtgärd** på menyraden. Välj aktiviteten från menyn som visas.
* Klicka på noden och välj sedan åtgärden i den **åtgärder** fönstret.

När du väljer en nod och använda någon av dessa metoder för att se en uppgiftslista, visas endast för de åtgärder som kan utföras på noden.

### <a name="devices-node"></a>Enhetsnoden
Den **enheter** noden representerar StorSimple-enheter och virtuella StorSimple-enheter som är anslutna till StorSimple Snapshot Manager. Välj den här noden för att ansluta och konfigurera en enhet och importera dess associerade volymer, volymer grupper och befintliga säkerhetskopior. Flera enheter kan anslutas till en enda värd.

* Om du vill expandera noden, klicka på pilikonen vid **enheter**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **enheter** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Klicka för att visa en lista över konfigurerade enheter **enheter** i den **omfång** fönstret. I listan med enheter, tillsammans med information om varje enhet visas i den **resultat** fönstret.

### <a name="volumes-node"></a>Volymer nod
Den **volymer** noden representerar de enheter som relaterar till volymer som monterats av värden, inklusive de som identifierats via iSCSI och de som identifierats via en enhet. Använd den här noden för att visa en lista över tillgängliga volymer och tilldela enskilda volymerna till volymgrupper.

* Om du vill expandera noden, klicka på pilikonen vid **volymer**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **volymer** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Klicka för att visa en lista över volymer **volymer** i den **omfång** fönstret. Lista över volymer, tillsammans med information om varje volym visas i den **resultat** fönstret.

### <a name="volume-groups-node"></a>Volymen Gruppnod
Volymgrupper kallas även konsekvensgrupper. Varje volym-gruppen är en pool med programrelaterade volymer som hjälper dig för att kontrollera programkonsekvens under säkerhetskopiering. Använd den **Volymgrupper** nod för att konfigurera dessa grupper och för att göra interaktiva säkerhetskopior eller skapa scheman för säkerhetskopiering. 

* Om du vill expandera noden, klicka på pilikonen vid **Volymgrupper**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **Volymgrupper** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Klicka för att visa en lista över volymgrupper **Volymgrupper** i den **omfång** fönstret. Listan över volymgrupper, tillsammans med information om varje volym-gruppen visas i den **resultat** fönstret.

### <a name="backup-policies-node"></a>Noden för principer för säkerhetskopiering
Principer för säkerhetskopiering är jobbscheman för lokala och molnbaserade ögonblicksbilder. Använd den **Säkerhetskopieringsprinciper** noden för att ange hur ofta en säkerhetskopia skapas och hur länge en säkerhetskopia ska behållas. 

* Om du vill expandera noden, klicka på pilikonen vid **principer för säkerhetskopiering**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **principer för säkerhetskopiering** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över principer för säkerhetskopiering klickar du på **Säkerhetskopieringsprinciper** i den **omfång** fönstret. Listan med principer för säkerhetskopiering, tillsammans med information om varje princip visas i den **resultat** fönstret.

> [!NOTE]
> Du kan lagra högst 64 säkerhetskopieringar.


### <a name="backup-catalog-node"></a>Katalogen säkerhetskopieringsnod
Den **säkerhetskopieringskatalog** noden innehåller en lista över på plats och annan säkerhetskopior av Azure StorSimple-volymer. Den här noden är ordnad efter volym grupp och varje grupp volymcontainern innehåller separata strukturer för lokala ögonblicksbilder (den **lokal ögonblicksbild**s nod) och molnbaserade ögonblicksbilder (den **Molnögonblicksbilder** nod). När expanderats visas varje grupp volymbehållaren alla lyckade säkerhetskopior som gjorts interaktivt eller genom en konfigurerade principen.

* Om du vill expandera noden, klicka på pilikonen vid **säkerhetskopieringskatalog**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **säkerhetskopieringskatalog** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Klicka för att visa en lista över ögonblicksbilder av säkerhetskopior **säkerhetskopieringskatalogen** i den **omfång** fönstret. Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild som visas i den **resultat** fönstret.

### <a name="local-snapshots-node"></a>Lokala ögonblicksbilder noden
Den **lokala ögonblicksbilder** nod visar en lista över lokala ögonblicksbilder för en specifik volym-grupp. Noden finns under den **säkerhetskopieringskatalog** nod i den **omfång** fönstret. Lokala ögonblicksbilder är point-in-time-kopior av volymdata som är lagrade på Azure StorSimple-enheten. Normalt kan den här typen av säkerhetskopiering skapas och återställa snabbt. Du kan använda en lokal ögonblicksbild precis som en lokal säkerhetskopia.

* Om du vill expandera noden, klicka på pilikonen vid **lokala ögonblicksbilder**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **lokala ögonblicksbilder** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Klicka för att visa en lista över lokala ögonblicksbilder **lokala ögonblicksbilder** i den **omfång** fönstret. Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild som visas i den **resultat** fönstret.

### <a name="cloud-snapshots-node"></a>Nod för ögonblicksbilder av molnet
Den **Molnögonblicksbilder** nod innehåller ögonblicksbilder av molnet för en specifik volym-grupp. Noden finns under den **säkerhetskopieringskatalog** nod i den **omfång** fönstret. Ögonblicksbilder av molnet är point-in-time-kopior av volymdata som lagras i molnet. En ögonblicksbild i molnet motsvarar en ögonblicksbild som replikerats på ett annat, externa lagringssystem. Ögonblicksbilder av molnet som är särskilt användbart i scenarier för haveriberedskap.

* Om du vill expandera noden, klicka på pilikonen vid **Molnögonblicksbilder**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **Molnögonblicksbilder** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Klicka för att visa en lista över ögonblicksbilder av molndata **Molnögonblicksbilder** i den **omfång** fönstret. Listan över ögonblicksbilder, tillsammans med information om varje ögonblicksbild som visas i den **resultat** fönstret.

### <a name="jobs-node"></a>Jobb-nod
Den **jobb** noden innehåller information om schemalagda, körs och nyligen utförda säkerhetskopieringsjobb. 

* Om du vill expandera noden, klicka på pilikonen vid **jobb**.
* Om du vill se en meny med tillgängliga åtgärder, högerklickar du på den **jobb** noden eller högerklicka på någon av noderna som visas i den utökade vyn.
* Om du vill se en lista över schemalagda jobb, expandera den **jobb** noden och klicka sedan på **schemalagd**. Lista över tidigare konfigurerade jobb och information om varje jobb visas i den **resultat** fönstret. 
* Om du vill se en lista över nyligen slutförda jobb, expandera den **jobb** noden och klicka sedan på **senaste 24 timmarna**. En lista över jobb som har slutförts under de senaste 24 timmarna visas i den **resultat** fönstret. Den **resultat** fönstret innehåller även information om varje slutförda jobb.
* Om du vill se en lista över jobb som körs för närvarande, expandera den **jobb** noden och klicka sedan på **kör**. Listan över jobb och information om varje jobb som körs visas i den **resultat** fönstret.

## <a name="results-pane"></a>Resultatfönstret
Den **resultat** är den mittersta rutan i StorSimple Snapshot Manager-UI. Den innehåller listor och detaljerad statusinformation för den nod som du har valt i den **omfång** fönstret.

### <a name="example"></a>Exempel
I följande exempel visas klickar du på den **Volymgrupper** nod i den **omfång** fönstret. Den **resultat** fönstret visar en lista över volymgrupper med information om varje grupp.

![Resultatfönstret](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Du kan konfigurera informationen som visas i den **resultat** fönstret: Högerklicka på en nod i den **omfång** fönstret klickar du på **visa**, och klicka sedan på **Lägg till/ta bort kolumner** .

## <a name="actions-pane"></a>Åtgärdsfönstret
Den **åtgärder** är den högra rutan i StorSimple Snapshot Manager-UI. Den innehåller en meny med åtgärder som du kan utföra på noden, vyn eller data som du väljer i den **omfång** fönstret eller **resultat** fönstret. Den **åtgärder** fönstret innehåller samma kommandon som den **åtgärd** menyer som är tillgängliga för objekt i den **omfång** fönstret och **resultat** fönstret. En beskrivning av varje åtgärd finns i tabellen i den **åtgärd** menyn avsnittet.

### <a name="examples"></a>Exempel
Se följande exempel i den **omfång** fönstret expanderar den **jobb** noden och klicka på **schemalagd**. Den **åtgärder** fönstret visar tillgängliga åtgärder för den **schemalagd** noden.

![Exempel för åtgärdsfönstret schemalagda jobb](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Se fler alternativ i den **omfång** fönstret expanderar den **jobb** nod, klickar du på **schemalagd**, och klicka sedan på ett schemalagt jobb i den **resultat** fönstret. Den **åtgärder** fönstret visar tillgängliga åtgärder för det schemalagda jobbet som visas i följande exempel.

![Åtgärdsfönstret jobbåtgärder exempel](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Tangentbordsnavigering och genvägar
StorSimple Snapshot Manager kan hjälpmedelsfunktionerna i Windows-operativsystemet och Microsoft Management Console (MMC). Den innehåller också vissa tangentbord funktioner och alla kortkommandon som är specifika för StorSimple Snapshot Manager enligt beskrivningen i följande avsnitt.

* [Tangenterna för navigering](#keyboard-navigation-keys) 
* [Menyraden kortkommandon](#menu-bar-shortcut-keys) 
* [Kortkommandon för scope-fönstret](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Tangenterna för navigering
I följande tabell beskrivs de nycklar som du kan använda för att navigera StorSimple Snapshot Manager-användargränssnittet. 

| Navigering nyckel | Åtgärd |
|:--- |:--- |
| NEDPIL |Använd nedåtpilen för att flytta lodrätt till nästa objekt i en meny eller fönstret. |
| Retur |Tryck på RETUR för att slutföra en åtgärd och sedan fortsätta till nästa steg. Du kan till exempel trycka på RETUR för att välja **nästa**, **OK**, eller **skapa**, och gå sedan till nästa steg i en guide. |
| Esc |Tryck på ESC för att stänga en meny, eller för att avbryta och stänga en sida. |
| F1 |Tryck på F1 för att visa ett hjälpavsnitt för det aktiva fönstret. |
| F5 |Tryck på F5 för att uppdatera en nod. |
| F6 |Tryck på F6 för att flytta från den **omfång** fönstret för att den **resultat** fönstret. |
| F10 |Tryck på F10 för att gå till verktygsfältet. |
| VÄNSTERPIL |Använd VÄNSTERPIL för att flytta vågrätt från ett menyalternativ för fältet till föregående alternativ. Om du flyttar till föregående objekt på menyraden, visas menyn åtgärd (eller kontext) för föregående objekt. |
| HÖGERPIL |Använd högerpilen för att flytta vågrätt från fältet för ett menyalternativ till nästa. När du flyttar till nästa objekt på menyraden, visas menyn åtgärd (eller kontext) för det nya objektet. |
| TABB-tangenten |Använd TABB-tangenten för att flytta till nästa ruta på konsolen eller till nästa val eller text i en sida. |
| Pil upp |Använd uppåtpilen lodrätt flytta till föregående objekt på en meny eller fönstret. |

### <a name="menu-bar-shortcut-keys"></a>Menyraden kortkommandon
I följande tabell beskrivs kortkommandon för menyraden. När du trycker på genvägen och menyn öppnas, kan du använda menyn kortkommandon (understrukna nycklar i menyn). Mer information om menyraden går du till [menyraden](#menu-bar).

| Genväg | Resultat | Menyn kortkommando | Resultat |
|:--- |:--- |:--- |:--- |
| ALT + A |Öppnar den **filen** menyn. |N |Öppnar en ny instans av konsolen. |
|  |O |Öppnar den **Administrationsverktyg** sidan. | |
|  |S |Sparar StorSimple Snapshot Manager-konsolen. | |
|  |A |Öppnar den **Spara som** sidan. | |
|  |M |Öppnar den **Lägg till/ta bort snapin-modulen** sidan. | |
|  |P |Öppnar den **alternativ** sidan. | |
|  |H |Öppnas onlinehjälp. | |
| ALT + A |Öppnar den **åtgärd** menyn. |I |Aktiverar Visningsalternativ för import och inaktivera. |
|  |W |Öppna en ny StorSimple Snapshot Manager-konsol. | |
|  |F |Uppdaterar StorSimple Snapshot Manager-konsolen. | |
|  |L |Öppnar den **Exportera lista** sidan. | |
|  |H |Öppnas onlinehjälp. | |
| ALT + V |Öppnar den **visa** menyn. |A |Öppnar den **Lägg till/ta bort kolumner** sidan. |
|  |U |Öppnar den **Anpassa vy** sidan. | |
| ALT + O |Öppnar den **Favoriter** menyn. |A |Öppnar den **Lägg till i Favoriter** sidan. |
|  |O |Öppnar den **Ordna Favoriter** sidan. | |
| ALT + W |Öppnar den **fönstret** menyn. |N |Öppnas ett annat fönster i StorSimple Snapshot Manager. |
|  |C |Visar alla öppna konsolfönstret i ett sammanhängande format. | |
|  |T |Visar alla öppna konsolfönstret i ett rutnät. | |
|  |I |Ordnar ikonerna i en vågrät rad längst ned på skärmen. | |
| ALT + H |Öppnar den **hjälpa** menyn. |H |Öppnas onlinehjälp. |
|  |T |Öppnar sidan Microsoft TechNet Tech Center. | |
|  |A |Öppnar den **om Microsoft Management Console** sidan. | |

### <a name="scope-pane-shortcut-keys"></a>Kortkommandon för scope-fönstret
Följande tabeller visar genvägen tangentkombinationer för varje nod i den **omfång** fönstret. 

* [Kortkommandon för enheter nod](#devices-node-shortcut-keys)
* [Kortkommandon för volymer nod](#volumes-node-shortcut-keys)
* [Kortkommandon för volymen grupper nod](#volume-groups-node-shortcut-keys)
* [Kortkommandon för principer för noden för säkerhetskopiering](#backup-policies-node-shortcut-keys)
* [Säkerhetskopiera Catalog noden kortkommandon](#backup-catalog-node-shortcut-keys)
* [Kortkommandon för jobb-nod](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Kortkommandon för enheter nod
| Menyn genväg | Resultat |
|:--- |:--- |
| C |Öppnar den **konfigurera en enhet** sidan. |
| D |Uppdaterar listan över enheter och information om enheten. |
| V |Öppnar den **visa** menyn. |
| W |Öppna en ny StorSimple Snapshot Manager-konsol som fokuserar på de **information** noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **Exportera lista** sidan. |
| H |Öppnas onlinehjälp. |

#### <a name="volumes-node-shortcut-keys"></a>Kortkommandon för volymer nod
| Menyn genväg | Resultat |
|:--- |:--- |
| V |Uppdaterar listan över volymer. |
| V (två gånger tryckning) |Öppnar den **visa** menyn. |
| W |Öppna en ny StorSimple Snapshot Manager-konsol som fokuserar på de **volymer** noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **Exportera lista** sidan. |
| H |Öppnas onlinehjälp. |

#### <a name="volume-groups-node-shortcut-keys"></a>Kortkommandon för volymen grupper nod
| Menyn genväg | Resultat |
|:--- |:--- |
| G |Öppnar den **skapa en volym grupp** sidan. |
| V |Öppnar den **visa** menyn. |
| W |Öppna en ny StorSimple Snapshot Manager-konsol som fokuserar på de **Volymgrupper** noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **Exportera lista** sidan. |
| H |Öppnas onlinehjälp. |

#### <a name="backup-policies-node-shortcut-keys"></a>Kortkommandon för principer för noden för säkerhetskopiering
| Menyn genväg | Resultat |
|:--- |:--- |
| B |Öppnar den **skapa en princip** sidan. |
| V |Öppnar den **visa** menyn. |
| W |Öppna en ny StorSimple Snapshot Manager-konsol som fokuserar på de **Volymgrupper** noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den ** Exportera lista ** sidan. |
| H |Öppnas onlinehjälp. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Säkerhetskopiera Catalog noden kortkommandon
| Menyn genväg | Resultat |
|:--- |:--- |
| W |Öppna en ny StorSimple Snapshot Manager-konsol som fokuserar på de **Volymgrupper** noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| H |Öppnas onlinehjälp. |

#### <a name="jobs-node-shortcut-keys"></a>Kortkommandon för jobb-nod
| Menyn genväg | Resultat |
|:--- |:--- |
| V |Öppnar den **visa** menyn. |
| W |Öppna en ny StorSimple Snapshot Manager-konsol som fokuserar på de **jobb** noden. |
| F |Uppdaterar StorSimple Snapshot Manager-konsolen. |
| L |Öppnar den **Exportera lista** sidan. |
| H |Öppnar onlinehjälp |

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda StorSimple Snapshot Manager för att administrera din StorSimple-lösning](storsimple-snapshot-manager-admin.md).
* Lär dig hur du [använda StorSimple Snapshot Manager för att ansluta och hantera enheter](storsimple-snapshot-manager-manage-devices.md).

