---
title: Översikt över funktionen Microsoft Threat Modeling Tool - Azure
description: Läs mer om alla funktioner som finns i verktyget för hotmodellering
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 88eea0eb2bceb0f6322381a94d4339cbdfa662c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552125"
---
# <a name="threat-modeling-tool-feature-overview"></a>Översikt över funktionen Threat Modeling Tool

Hot modellering tool kan hjälpa dig med ditt hot modellering behov. En grundläggande introduktion till verktyget finns i [Komma igång med verktyget för hotmodellering](threat-modeling-tool-getting-started.md).

> [!NOTE]
>Hot modellering tool uppdateras ofta, så kontrollera den här guiden ofta för att se våra senaste funktioner och förbättringar.

Om du vill öppna en tom sida väljer du **Skapa en modell**.

![Tom sida](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Om du vill se de funktioner som för närvarande är tillgängliga i verktyget använder du den hotmodell som skapats av vårt team i exemplet [Kom igång.](threat-modeling-tool-getting-started.md)

![Grundläggande hotmodell](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigering

Innan vi diskuterar de inbyggda funktionerna ska vi granska huvudkomponenterna i verktyget.

### <a name="menu-items"></a>Menyalternativ

Upplevelsen liknar andra Microsoft-produkter. Låt oss granska de översta menyalternativen.

![Menyalternativ](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Label (Etikett)                               | Information      |
| --------------------------------------- | ------------ |
| **Fil** | <ul><li>Öppna, spara och stänga filer</li><li>Logga in och logga ut från OneDrive-konton.</li><li>Dela länkar (visa och redigera).</li><li>Visa filinformation.</li><li>Använd en ny mall på befintliga modeller.</li></ul> |
| **Redigera** | Ångra och gör om åtgärder samt kopiera, klistra in och ta bort. |
| **Vy** | <ul><li>Växla mellan **analys-** och designvyer. **Analysis**</li><li>Öppna stängda fönster (till exempel stenciler, elementegenskaper och meddelanden).</li><li>Återställ layouten till standardinställningarna.</li></ul> |
| **Diagram** | Lägg till och ta bort diagram och gå igenom flikar med diagram. |
| **Rapporter** | Skapa HTML-rapporter att dela med andra. |
| **Hjälp** | Hitta guider som hjälper dig att använda verktyget. |

Symbolerna är genvägar för menyerna på den översta nivån:

| Symbol                               | Information      |
| --------------------------------------- | ------------ |
| **Öppna** | Öppnar en ny fil. |
| **Spara** | Sparar den aktuella filen. |
| **Design** | Öppnar **designvyn,** där du kan skapa modeller. |
| **Analysera** | Visar genererade hot och deras egenskaper. |
| **Lägg till diagram** | Lägger till ett nytt diagram (liknar nya flikar i Excel). |
| **Ta bort diagram** | Tar bort det aktuella diagrammet. |
| **Kopiera/klippa ut/klistra in** | Kopierar, klipper och klistrar in element. |
| **Ångra/gör om** | Ångrar och gör om åtgärder. |
| **Zooma in/zooma ut** | Zoomar in och ut ur diagrammet för en bättre vy. |
| **Feedback** | Öppnar MSDN Forum. |

### <a name="canvas"></a>Arbetsyta

Arbetsytan är det utrymme där du drar och släpper element. Dra och släpp är det snabbaste och mest effektiva sättet att bygga modeller. Du kan också högerklicka och välja objekt på menyn för att lägga till generiska versioner av element, som visas:

#### <a name="drop-the-stencil-on-the-canvas"></a>Släpp stencilen på arbetsytan

![Släpp arbetsyta](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Markera stencilen

![Egenskaper för element](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stenciler

Baserat på den mall du väljer kan du hitta alla stenciler som är tillgängliga att använda. Om du inte hittar rätt element använder du en annan mall. Eller så kan du ändra en mall som passar dina behov. I allmänhet kan du hitta en kombination av kategorier som dessa:

| Stencilnamn                               | Information      |
| --------------------------------------- | ------------ |
| **Process** | Program, plugin-program för webbläsare, trådar, virtuella maskiner |
| **Extern interactor** | Autentiseringsleverantörer, webbläsare, användare, webbapplikationer |
| **Datalager** | Cache, lagring, konfigurationsfiler, databaser, register |
| **Dataflöde** | Binär, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, namngiven pipe, RPC/DCOM, SMB, UDP |
| **Gräns för förtroendelinje/kantlinje** | Företagsnätverk, internet, maskin, sandlåda, användar-/kernel-läge |

### <a name="notesmessages"></a>Anteckningar/meddelanden

| Komponent                               | Information      |
| --------------------------------------- | ------------ |
| **Meddelanden** | Intern verktygslogik som varnar användare när det finns ett fel, till exempel inga dataflöden mellan element. |
| **Obs!** | Manuella anteckningar läggs till i filen av ingenjörsteam under hela design- och granskningsprocessen. |

### <a name="element-properties"></a>Egenskaper för element

Elementegenskaperna varierar beroende på vilka element du väljer. Förutom förtroendegränser innehåller alla andra element tre allmänna val:

| Egenskapen Element                               | Information      |
| --------------------------------------- | ------------ |
| **Namn** | Användbart för att namnge dina processer, butiker, interactorer och flöden så att de är lätta att känna igen. |
| **Utanför räckvidden** | Om du väljer det här alternativet tas elementet ut ur hotgenereringsmatrisen (rekommenderas inte). |
| **Orsak till att det inte är så** | Fältet Motivering för att låta användarna veta varför utanför omfånget har valts. |

Egenskaper ändras under varje elementkategori. Markera varje element om du vill granska de tillgängliga alternativen. Eller så kan du öppna mallen för att lära dig mer. Låt oss granska funktionerna.

## <a name="welcome-screen"></a>Välkomstskärmen

När du öppnar appen visas **Welcome** välkomstskärmen.

### <a name="open-a-model"></a>Öppna en modell

Hovra över **Öppna en modell** för att visa två alternativ: Öppna från den här **datorn** och öppna **från OneDrive**. Det första alternativet öppnar skärmen **Öppna fil.** Det andra alternativet tar dig igenom inloggningsprocessen för OneDrive. När autentiseringen har slutförts kan du välja mappar och filer.

![Öppen modell](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Öppna från dator eller OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, förslag och problem

När du väljer **Feedback, förslag och ärenden**går du till MSDN Forum för SDL-verktyg. Du kan läsa vad andra säger om verktyget, inklusive lösningar och nya idéer.

![Feedback](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Designvy

När du öppnar eller skapar en ny modell öppnas **designvyn.**

### <a name="add-elements"></a>Lägg till element

Du kan lägga till element i rutnätet på två sätt:

- **Dra och släpp**: Dra önskat element till rutnätet. Använd sedan elementegenskaperna för att ge ytterligare information.
- **Högerklicka:** Högerklicka var som helst i rutnätet och välj objekt på den nedrullningsbara menyn. En allmän representation av det element du väljer visas på skärmen.

### <a name="connect-elements"></a>Ansluta element

Du kan ansluta element på två sätt:

- **Dra och släpp**: Dra önskat dataflöde till rutnätet och anslut båda ändarna till lämpliga element.
- **Klicka på + Skift:** Klicka på det första elementet (skicka data), tryck och håll ned Skift-tangenten och markera sedan det andra elementet (ta emot data). Högerklicka och välj **Anslut**. Om du använder ett dubbelriktad dataflöde är ordern inte lika viktig.

### <a name="properties"></a>Egenskaper

 Om du vill se vilka egenskaper som kan ändras på stencilerna markerar du stencilen och informationen fylls i därefter. I följande exempel visas före och efter att en **databasstencil** dras till diagrammet:

#### <a name="before"></a>Före

![Före](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Efter

![Efter](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Meddelanden

Om du skapar en hotmodell och glömmer att ansluta dataflöden till element får du ett meddelande. Du kan ignorera meddelandet eller följa instruktionerna för att åtgärda problemet. 

![Meddelanden](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Anteckningar

Om du vill lägga till anteckningar i diagrammet växlar du från fliken **Meddelanden** till fliken **Anteckningar.**

## <a name="analysis-view"></a>Analysvy

När du har skapat diagrammet väljer du **analyssymbolen** (förstoringsglaset) i verktygsfältet genvägar för att växla till **analysvyn.**

![Analysvy](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Genererad hotval

När du väljer ett hot kan du använda tre olika funktioner:

| Funktion                               | Information      |
| --------------------------------------- | ------------ |
| **Läs indikator** | <p>Hotet markeras som läst, vilket hjälper dig att hålla reda på de objekt du granskat.</p><p>![Indikator för läsning/oläst](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interaktion fokus** | <p>Interaktion i diagrammet som tillhör ett hot markeras.</p><p>![Interaktion fokus](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Hotegenskaper** | <p>Ytterligare information om hotet visas i fönstret **Hotegenskaper.**</p><p>![Hotegenskaper](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Prioriterad ändring

Du kan ändra prioritetsnivån för varje genererat hot. Olika färger gör det enkelt att identifiera hot med hög, medelhög och låg prioritet.

![Prioriterad ändring](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Redigerbara fält för hotegenskaper

Som framgår av föregående bild kan du ändra informationen som genereras av verktyget. Du kan också lägga till information i vissa fält, till exempel motivering. Dessa fält genereras av mallen. Om du behöver mer information för varje hot kan du göra ändringar.

![Hotegenskaper](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Rapporter

När du har ändrat prioritet och uppdaterat statusen för varje genererat hot kan du spara filen och/eller skriva ut en rapport. Gå till **Rapportera** > **Skapa fullständig rapport**. Namnge rapporten och du bör se något som liknar följande bild:

![Rapport](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Nästa steg

- Skicka dina frågor, kommentarer tmtextsupport@microsoft.comoch funderingar till . **[Ladda ned](https://aka.ms/threatmodelingtool)** verktyget för hotmodellering för att komma igång.
- Om du vill bidra med en mall för communityn går du till vår [GitHub-sida.](https://github.com/Microsoft/threat-modeling-templates)