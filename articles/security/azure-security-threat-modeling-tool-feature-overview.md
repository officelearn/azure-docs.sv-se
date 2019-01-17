---
title: Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Läs om funktionerna som är tillgängliga i Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 8bbc13aa77c50c6ae02ecc67869035e7ec85effa
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359348"
---
# <a name="threat-modeling-tool-feature-overview"></a>Funktionsöversikt för Threat Modeling Tool

Threat Modeling Tool kan hjälpa dig med din threat modeling behov. En grundläggande introduktion till verktyget finns i [Kom igång med Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Threat Modeling Tool uppdateras ofta, så kontrollera den här guiden ofta för att se våra senaste funktionerna och förbättringarna.

Om du vill öppna en tom sida, Välj **skapa en modell**.

![Tom sida](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Om du vill se funktionerna som för närvarande finns i verktyget, använder du hotmodell som skapats av vårt team i den [börjar](./azure-security-threat-modeling-tool-getting-started.md) exempel.

![Grundläggande hotmodell](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigering

Innan vi går igenom de inbyggda funktionerna vi ska gå igenom huvudkomponenterna i verktyget.

### <a name="menu-items"></a>Menyalternativ

Upplevelsen är liknande på andra Microsoft-produkter. Vi ska gå igenom de översta menyalternativen.

![Menyalternativ](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Etikett                               | Information      |
| --------------------------------------- | ------------ |
| **Fil** | <ul><li>Öppna, spara och stäng filer</li><li>Logga in och logga ut från OneDrive-konton.</li><li>Dela länkar (Visa och redigera).</li><li>Visa filinformation.</li><li>Använda en ny mall för befintliga modeller.</li></ul> |
| **Redigera** | Ångra och gör om åtgärder som kopiera, klistra in, och ta bort. |
| **Visa** | <ul><li>Växla mellan **Analysis** och **Design** vyer.</li><li>Öppna stängda windows (till exempel stenciler, egenskaper och meddelanden).</li><li>Återställ layout till standardinställningar.</li></ul> |
| **Diagram** | Lägga till och ta bort diagram och gå till flik av diagram. |
| **Rapporter** | Skapa HTML-rapporter för att dela med andra. |
| **Hjälp** | Hitta med vars hjälp du kan använda verktyget. |

Symbolerna är genvägar i översta menyer:

| Symbol                               | Information      |
| --------------------------------------- | ------------ |
| **Öppna** | Öppnar en ny fil. |
| **Spara** | Sparar den aktuella filen. |
| **Design** | Öppnar den **Design** vy, där du kan skapa modeller. |
| **Analysera** | Visar genererade hot och deras egenskaper. |
| **Lägg till diagram** | Lägger till ett nytt diagram (liknar nya flikar i Excel). |
| **Ta bort diagram** | Tar bort det aktuella diagrammet. |
| **Kopiera och klipp ut/klistra in** | Kopior, rabatter och klistrar in element. |
| **Undo/Redo** | Ångrar och gör om åtgärder. |
| **Zooma in / Zooma ut** | Zoomar in och ut ur diagram för en bättre vy. |
| **Feedback** | Öppnas MSDN-Forum. |

### <a name="canvas"></a>Canvas

Arbetsytan är det utrymme som du drar och släpper element. Dra och släpp är det snabbaste och mest effektiva sättet att bygga modeller. Du kan också högerklicka och välj objekt från menyn för att lägga till allmänna versioner av element, som visas:

#### <a name="drop-the-stencil-on-the-canvas"></a>Ta bort stencilen på arbetsytan

![Ta bort arbetsyta](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Välj stencilen

![Elementegenskaper](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stenciler

Du kan hitta alla stenciler som kan användas baserat på mallen som du väljer. Om du inte hittar rätt element kan använda en annan mall. Eller du kan ändra en mall för att passa dina behov. I allmänhet kan du hitta en kombination av kategorier som dessa:

| Stencil namn                               | Information      |
| --------------------------------------- | ------------ |
| **Process** | Program, plugin-program för webbläsaren, trådar, virtuella datorer |
| **Extern kontakt** | Autentiseringsprovidrar, webbläsare, användare, webbprogram |
| **Datalager** | Cache, lagring, konfigurationsfiler, databaser, register |
| **Dataflöde** | Binär ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, namngiven pipe, RPC/DCOM, SMB, UDP |
| **Lita på kantlinje/gräns** | Företagets nätverk, internet, dator, sandbox-miljön, användar-/ kernel-läge |

### <a name="notesmessages"></a>Anteckningar/meddelanden

| Komponent                               | Information      |
| --------------------------------------- | ------------ |
| **Meddelanden** | Interna verktyget logik som varnar användare när det finns ett fel, till exempel inga data flödar mellan element. |
| **Anteckningar** | Manuell kommentarer har lagts till i filen med teknikteam vid utformningen och granskningsprocessen. |

### <a name="element-properties"></a>Elementegenskaper

Elementegenskaper för varierar de element som du väljer. Utöver förtroendegränser innehålla andra element tre allmänna alternativ:

| Elementegenskapen                               | Information      |
| --------------------------------------- | ------------ |
| **Namn** | Användbart för att namnge dina processer, butiker, interaktörer och flöden så att de är lätt att känna igen. |
| **Utanför** | Om elementet är tas bort från matrisen threat generations (rekommenderas inte). |
| **Orsaken till utanför omfånget** | Justering fält så att användarna vet varför utanför har valts. |

Egenskaper har ändrats under varje element kategori. Välj varje element granska de tillgängliga alternativen. Alternativt kan du öppna mallen om du vill veta mer. Vi granskar du funktionerna.

## <a name="welcome-screen"></a>Välkomstskärmen

När du öppnar appen visas den **Välkommen** skärmen.

### <a name="open-a-model"></a>Öppna en modell

Hovra över **öppen en modell** att visa två alternativ: **Öppna från den här datorn** och **öppna från OneDrive**. De första alternativet Öppna den **öppna** skärmen. Det andra alternativet tar dig igenom processen för inloggning för OneDrive. Efter en lyckad autentisering kan du välja mappar och filer.

![Öppen modell](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Öppna från datorn eller OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, förslag och problem

När du väljer **Feedback, förslag och problem**, du gå till MSDN-Forum för SDL-verktyg. Du kan läsa vad andra säger om verktyg, inklusive alternativa lösningar eller nya idéer.

![Feedback](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Designläge

När du öppnar eller skapa en ny modell i **Design** visa öppnas.

### <a name="add-elements"></a>Lägg till element

Du kan lägga till element i rutnätet på två sätt:

- **Dra och släpp**: Dra det önskade elementet till rutnätet. Sedan använda egenskaperna elementet som innehåller ytterligare information.
- **Högerklicka på**: Högerklicka i rutnätet och välj objekt från den nedrullningsbara menyn. En allmän representation av element som du väljer visas på skärmen.

### <a name="connect-elements"></a>Ansluta element

Du kan ansluta element på två sätt:

- **Dra och släpp**: Dra det önskade dataflödet till rutnätet och Anslut båda ändar till de relevanta delarna.
- **Klicka på + SKIFT**: Klicka på det första elementet (skicka data), tryck på och håll ned SKIFT och välj sedan det andra elementet (mottagning av data). Högerklicka och välj **Connect**. Ordningen är inte lika viktigt om du använder ett dubbelriktad dataflöde.

### <a name="properties"></a>Egenskaper

 Om du vill se de egenskaper som kan ändras på stenciler, väljer stencilen och informationen som fyller på lämpligt sätt. I följande exempel visas före och efter en **databasen** stencil dras till diagrammet:

#### <a name="before"></a>Före

![Före](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Efter

![Efter](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Meddelanden

Om du skapar en hotmodell och glöm att ansluta dataflöden till element, får du ett meddelande. Du kan ignorera meddelandet, eller du kan följa anvisningarna för att åtgärda problemet. 

![Meddelanden](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Anteckningar

Om du vill lägga till anteckningar i diagrammet, växla från den **meddelanden** fliken till den **anteckningar** fliken.

## <a name="analysis-view"></a>Visa analys

När du skapar diagrammet, väljer du den **Analysis** symbolen (förstoringsglas) i verktygsfältet genvägar att växla till den **Analysis** vy.

![Visa analys](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Val av genererade hot

När du väljer ett hot, kan du använda tre olika funktioner:

| Funktion                               | Information      |
| --------------------------------------- | ------------ |
| **Läs indikator** | <p>Hotet markeras som läst som hjälper dig att hålla reda på de objekt som du har granskat.</p><p>![Läs/olästa indikator](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interaktion fokus** | <p>Interaktion i diagrammet som hör till ett hot har markerats.</p><p>![Interaktion fokus](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Egenskaper för hot** | <p>Mer information om hotet som visas i den **Threat egenskaper** fönster.</p><p>![Egenskaper för hot](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Ändring av skrivningsregionsprioritet

Du kan ändra prioritetsnivån för varje genererad hot. Olika färger gör det enkelt att identifiera hög-, medel- och hot med låg prioritet.

![Ändring av skrivningsregionsprioritet](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Redigerbara Threat egenskaper-fält

Du kan ändra den information som genereras av verktyget som visas i föregående bild. Du kan också lägga till information till vissa fält, till exempel motivering. De här fälten genereras av mallen. Om du behöver mer information för varje hot kan göra du ändringar.

![Egenskaper för hot](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Rapporter

När du har ändrat prioritet och uppdaterar statusen för varje genererad hot, du kan spara filen och/eller skriva ut rapporten. Gå till **rapporten** > **skapa hela rapporten**. Namnge rapporten och du bör se något som liknar följande bild:

![Rapport](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Nästa steg

* För att bidra en mall för communityn, gå till vår [GitHub](https://github.com/Microsoft/threat-modeling-templates) sidan. 
* Kom igång med verktyget genom att gå till den [hämta](https://aka.ms/tmtpreview) sidan.
