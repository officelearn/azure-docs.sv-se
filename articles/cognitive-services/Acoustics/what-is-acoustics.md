---
title: Översikt över projektets akustik
titlesuffix: Azure Cognitive Services
description: Project Acoustics är en akustikmotor för 3D-interaktiva upplevelser, som integrerar simulering av bakad vågfysik med interaktiva designkontroller.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351144"
---
# <a name="what-is-project-acoustics"></a>Vad är Project Acoustics?
Project Acoustics är en vågakustikmotor för 3D-interaktiva upplevelser. Det modeller våg effekter som ocklusion, obstruktion, portaling och efterklang effekter i komplexa scener utan att kräva manuell zon uppmärkning eller CPU-intensiv raytracing. Den innehåller också spelmotor och ljud middleware integration. Project Acoustics filosofi liknar statisk belysning: baka detaljerad fysik offline för att ge en fysisk baslinje, och använda en lätt körning med uttrycksfulla designkontroller för att uppfylla dina konstnärliga mål för akustiken i din virtuella värld.

![Skärmdump från Gears of War 4 visar akustik voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Använda vågfysik för interaktiv akustik
Ray-baserade akustik metoder kan kontrollera ocklusion med hjälp av en enda källa-till-lyssnare ray rösterna, eller köra reverb genom att uppskatta lokala scenvolym med några strålar. Men dessa tekniker kan vara opålitliga eftersom en sten occludes så mycket som ett stenblock. Rays tar inte hänsyn till hur ljudet böjer sig runt objekt, ett fenomen som kallas diffraktion. Project Acoustics simulering fångar dessa effekter med hjälp av en vågbaserad simulering. Akustiken är mer förutsägbar, exakt och sömlös.

Project Acoustics viktigaste innovation är att koppla ihop verklig ljudvågsbaserad akustisk simulering med traditionella ljuddesignkoncept. Det översätter simuleringsresultat till traditionella ljud DSP parametrar för ocklusion, portaling och reverb. Designern använder kontroller över den här översättningsprocessen. För mer information om kärnteknikerna bakom Project Acoustics, besök [sidan för forskningsprojektet](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animering som visar en vågförökning med våg med våg över 2D-segment genom en scen](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Videopresentation från GDC 2019 (~30 min)
[![Project Akustik Video](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Klicka för att spela upp video")

## <a name="setup"></a>Installation
[Project Acoustics Unity integration](unity-integration.md) är dra-och-släpp och innehåller en Unity ljudmotor plugin. Utöka unity-ljudkällans kontroller genom att ansluta en komponent i Project Acoustics C# controls till varje ljudobjekt.

[Project Acoustics Unreal integration](unreal-integration.md) inkluderar redaktör och spel plugins för Unreal, och en Wwise mixer plugin. En anpassad ljudkomponent utökar välbekanta Wwise-funktioner inom Unreal med designkontroller för levande akustik. Design kontroller är också exponeras i Wwise på mixern plugin.

## <a name="workflow"></a>Arbetsflöde
* **Pre-baka:** Börja med att ställa in baka genom att välja vilken geometri som svarar på akustik, genom att till exempel ignorera ljusaxlar. Redigera sedan automatiska materialtilldelningar och välja navigeringsområden för att styra lyssnarens sampling. Det finns ingen manuell markering för reverb/portal/rumszoner.
* **Baka:** Ett analyssteg körs lokalt, vilket gör voxelization och annan geometrisk analys på scenen baserat på urval ovan. Resultaten visualiseras i redigeraren för att verifiera sceninställningar. Vid bakning skickas voxel-data ut till Azure och du får tillbaka en akustikspeltillgång.
* **Körtid:** Ladda tillgången i din nivå, och du är redo att lyssna på akustik i din nivå. Designa akustiken live i redigeraren med hjälp av detaljerade kontroller per källa. Kontrollerna kan också drivas från nivåskript.

## <a name="runtime-platforms"></a>Körningsplattformar
Project Acoustics runtime plugins kan för närvarande distribueras till följande plattformar:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Redaktörsplattformar
Project Acoustics editor plugin är tillgänglig för följande plattformar:
* Windows
* MacOS (endast enhet)

## <a name="download"></a>Ladda ned
* [Project Acoustics Unity plugin och prover](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Project Acoustics Unreal & Wwise plugins och prover](https://www.microsoft.com/download/details.aspx?id=58090)
  * För Xbox binärfiler och annan support, kontakta oss via [forumet](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Kontakta oss
* [Diskussion och ärenderapportering för projekt akustik](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Nästa steg
* Prova en [snabbstart för Project Acoustics för Unity](unity-quickstart.md) eller [för Unreal](unreal-quickstart.md)
* Utforska [ljuddesignfilosofin i Project Acoustics](design-process.md)

