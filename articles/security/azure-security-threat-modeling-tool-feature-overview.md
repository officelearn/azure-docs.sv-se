---
title: Microsoft Threat Modeling verktyget - Azure | Microsoft Docs
description: Lär dig mer om alla funktioner som finns i verktyget Modeling hot
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 76e49fc0e680acbc2b7b7c62b69fbf72d6690acf
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
ms.locfileid: "26754753"
---
# <a name="threat-modeling-tool-feature-overview"></a>Översikt över hot Modeling verktyget funktioner

Verktyget Modeling hot kan hjälpa dig med din hot modeling behov. En grundläggande introduktion till verktyget, se [Kom igång med verktyget Modeling hot](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Verktyget Modeling hot uppdateras ofta, så kontrollera den här guiden ofta om du vill se vår nya funktioner och korrigeringar.

Om du vill öppna en tom sida, Välj **skapa en modell**.

![Tom sida](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

För närvarande tillgängliga funktioner i verktyget, Använd hotmodell som skapats av vårt team i den [Kom igång](./azure-security-threat-modeling-tool-getting-started.md) exempel.

![Grundläggande hotmodell](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigering

Innan vi går de inbyggda funktionerna nu ska vi se huvudkomponenterna i verktyget.

### <a name="menu-items"></a>Menyalternativ

Upplevelsen liknar andra Microsoft-produkter. Nu ska vi se de översta menyalternativen.

![Menyalternativ](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Etikett                               | Information      |
| --------------------------------------- | ------------ |
| **Fil** | <ul><li>Öppna, spara och stänga filer</li><li>Logga in och logga ut från OneDrive-konton.</li><li>Dela länkar (Visa och redigera).</li><li>Visa filinformation.</li><li>Använd en ny mall för befintliga modeller.</li></ul> |
| **Redigera** | Ångra och gör om åtgärder samt kopiera, klistra in och ta bort. |
| **Visa** | <ul><li>Växla mellan **Analysis** och **Design** vyer.</li><li>Öppna stängd windows (till exempel stenciler, egenskaper och meddelanden).</li><li>Återställa layouten till standardinställningar.</li></ul> |
| **Diagram** | Lägg till och ta bort diagram och gå till flik av diagram. |
| **Rapporter** | Skapa HTML-rapporter för att dela med andra. |
| **Hjälp** | Hitta guider som hjälper dig att använda verktyget. |

Symbolerna är genvägar i översta menyer:

| Symbol                               | Information      |
| --------------------------------------- | ------------ |
| **Öppna** | Öppnar en ny fil. |
| **Spara** | Sparar den aktuella filen. |
| **Design** | Öppnar den **Design** vy, där du kan skapa modeller. |
| **Analysera** | Visar genererade hot och deras egenskaper. |
| **Lägg till diagram** | Lägger till ett nytt diagram (liknar nya flikar i Excel). |
| **Ta bort diagram** | Tar bort det aktuella diagrammet. |
| **Kopiera och klipp ut/klistra in** | Kopierar klipper och klistrar in element. |
| **Ångra/Gör om** | Ångrar och gör om åtgärder. |
| **Zooma in / Zooma ut** | Zoomar in och ut diagram för en bättre vy. |
| **Feedback** | Öppnar MSDN-Forum. |

### <a name="canvas"></a>Arbetsytan

Arbetsytan är det utrymme som du drar och släpper element. Dra och släpp är det snabbaste och mest effektiva sättet att bygga modeller. Du kan också högerklicka och välj objekt från menyn för att lägga till allmän versioner av element, som visas:

#### <a name="drop-the-stencil-on-the-canvas"></a>Släpp stencilen på arbetsytan

![Arbetsytan släpp](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Välj stencilen

![Egenskaper](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stenciler

Baserat på den mall som du väljer, hittar du alla stenciler som är tillgängliga för användning. Om du inte hittar rätt elementen kan använda en annan mall. Eller ändra en mall så att de passar dina behov. I allmänhet kan du hitta en kombination av kategorier som dessa:

| Stencilens namn                               | Information      |
| --------------------------------------- | ------------ |
| **Processen** | Program, plugin-program för webbläsaren, trådar, virtuella datorer |
| **Extern kontakt** | Autentiseringsproviders, webbläsare, användare, webbprogram |
| **Datalager** | Cache, lagring, konfigurationsfiler, databaser, registret |
| **Dataflöde** | Binär ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, namngiven pipe, RPC/DCOM, SMB, UDP |
| **Förtroende kantlinje/gräns** | Företagets nätverk, internet, dator, sandbox, användar-/ kernel-läge |

### <a name="notesmessages"></a>Anteckningar/meddelanden

| Komponent                               | Information      |
| --------------------------------------- | ------------ |
| **Meddelanden** | Interna verktyget logik som varnar användare när det uppstår ett fel, till exempel inga data flödar mellan element. |
| **Anteckningar** | Manuell information har lagts till i filen med engineering team vid utformningen och granska. |

### <a name="element-properties"></a>Egenskaper

Egenskaper för varierar beroende på de element som du väljer. Förutom förtroendegränser innehåller alla andra element tre allmänna inställningar:

| Egenskapen element                               | Information      |
| --------------------------------------- | ------------ |
| **Namn** | Användbart för att namnge din processer, Arkiv, interaktörer och flöden så att de är lätt att känna igen. |
| **Utanför omfånget** | Om markerad hämtas elementet utanför matrisen hot generation (rekommenderas inte). |
| **Orsak till utanför omfånget** | Motiveringen fält så att användarna vet varför täcker har valts. |

Egenskaper har ändrats under varje element. Markera varje element att inspektera de tillgängliga alternativen. Alternativt kan du öppna mallen om du vill veta mer. Nu ska vi se funktionerna.

## <a name="welcome-screen"></a>Välkomstskärmen

När du öppnar appen visas den **Välkommen** skärmen.

### <a name="open-a-model"></a>Öppna en modell

Hovra över **öppna en modell** avslöjar två alternativ: **öppna från den här datorn** och **öppna från OneDrive**. De första alternativet Öppna den **öppna** skärmen. Det andra alternativet tar dig igenom processen inloggning för OneDrive. När autentiseringen lyckas kan du välja mappar och filer.

![Öppna modellen](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Öppna från datorn eller OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, förslag och problem

När du väljer **Feedback, förslag och problem**, du gå till MSDN-Forum för SDL-verktyg. Du kan läsa vad andra säger om verktyget, inklusive lösningar och nya idéer.

![Feedback](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Designvyn

När du öppnar eller skapa en ny modell i **Design** visa öppnas.

### <a name="add-elements"></a>Lägga till element

Du kan lägga till element i rutnätet på två sätt:

- **Dra och släpp**: dra önskade elementet i rutnätet. Sedan med elementet kan du ange ytterligare information.
- **Högerklicka på**: Högerklicka någonstans i rutnätet och välj objekt från den nedrullningsbara menyn. En allmän representation av element som du väljer visas på skärmen.

### <a name="connect-elements"></a>Ansluta element

Du kan ansluta element på två sätt:

- **Dra och släpp**: dra önskade dataflöde i rutnätet och ansluta båda ändarna till lämplig elementen.
- **Klicka på + SKIFT**: klickar du på det första elementet (skicka data), håll ned SKIFT, och välj sedan det andra elementet (mottagning av data). Högerklicka och välj **Anslut**. Ordningen är inte lika viktigt om du använder en dubbelriktad dataflöde.

### <a name="properties"></a>Egenskaper

 Om du vill se vilka egenskaper som kan ändras i stenciler, Välj stencilen och informationen fyller du i enlighet med detta. I följande exempel visas före och efter ett **databasen** stencil dras till diagrammet:

#### <a name="before"></a>Före

![Före](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Efter

![Efter](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Meddelanden

Om du skapar en hotmodell och glömt att ansluta dataflöden till element, får du ett meddelande. Du kan ignorera meddelandet eller du kan följa anvisningarna för att åtgärda problemet. 

![Meddelanden](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Anteckningar

Om du vill lägga till anteckningar i diagrammet växla från den **meddelanden** fliken till den **anteckningar** fliken.

## <a name="analysis-view"></a>Analysen

När du skapar ditt diagram, väljer du den **Analysis** symbol (förstoringsglas) i verktygsfältet genvägar växla till den **Analysis** vyn.

![Analysen](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Val av genererade hot

När du väljer ett hot kan använda du tre olika funktioner:

| Funktion                               | Information      |
| --------------------------------------- | ------------ |
| **Läs indikator** | <p>Hotet har markerats som Läs-, som hjälper dig att hålla reda på de objekt som du granskat.</p><p>![Läs oläst indikator](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interaktion fokus** | <p>Interaktion i som hör till ett hot i diagrammet markeras.</p><p>![Interaktion fokus](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Egenskaper för hot** | <p>Mer information om hot visas i den **hot egenskaper** fönster.</p><p>![Egenskaper för hot](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Ändra prioritet

Du kan ändra prioritetsnivån för varje genererade hot. Olika färger gör det enkelt att identifiera hög, medel- och låg prioritet hot.

![Ändra prioritet](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Redigerbart fält för hot egenskaper

Du kan ändra den information som genereras av verktyget som visas i föregående bild. Du kan också lägga till information till vissa fält, till exempel justering. De här fälten skapas av mallen. Om du behöver mer information för varje hot kan göra du ändringar.

![Egenskaper för hot](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Rapporter

När du har ändrat prioriteter och uppdaterar statusen för varje genererade hot, du kan spara filen och/eller skriva ut rapporten. Gå till **rapporten** > **skapa fullständig**. Namn på rapporten och du bör se något liknande följande bild:

![Rapport](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill bidra med en mall för gemenskapen, gå till vår [GitHub](https://github.com/Microsoft/threat-modeling-templates) sidan. 
* Om du vill komma igång med verktyget går du till den [hämta](https://aka.ms/tmtpreview) sidan.
