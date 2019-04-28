---
title: Projektet Akustik översikt
titlesuffix: Azure Cognitive Services
description: Projektet Akustik är en Akustik motor för 3D-interaktiva upplevelser, integrera inbyggd wave fysik simulering med interaktiva design kontroller.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 1fc125322b83a0eb51095fac21dee05c7ffb39c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432457"
---
# <a name="what-is-project-acoustics"></a>Vad är Project Acoustics?
Projektet Akustik är en wave Akustik motor för 3D-interaktiva upplevelser. Wave effekter som diffraction, portaling och eko effekterna i komplexa bakgrunder modellerar utan manuell zon markup. Den innehåller också game engine och ljud mellanprogram integration. Projektet Akustik filosofin liknar statiska belysning: Skapa detaljerade fysik offline för att tillhandahålla en fysisk baslinje, och använda en enkel körning med lättfattliga design kontroller för konstnärlig ändamålet.

![Skärmbild från Gears of War 4 visar Akustik voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Med wave fysik för interaktiva Akustik
Ray-baserade Akustik metoder kan söka efter ocklusion med hjälp av en enda källa till lyssnaren ray cast eller enhet eko genom att uppskatta lokala scen volym med några strålar. Men dessa metoder kan vara otillförlitliga eftersom en pebble occludes så mycket som en boulder. Strålar hänsyn inte till den vägen ljud böjningar runt objekt, ett fenomen som kallas diffraction. Projektet Akustik simulering samlar in dessa effekter med hjälp av en wave-baserade simulering. Resultatet är mer förutsägbar och tillförlitlig.

Projektet Akustik viktiga innovation är att par akustiska simulering med traditionella ljud designbegrepp. Den översätter simuleringsresultat till traditionella ljud DSP-parametrar för ocklusion, portaling och eko. Designern använder kontroller över den här process för översättning. Mer information om kärntekniker bakom projekt Akustik på den [research projektsida](https://www.microsoft.com/en-us/research/project/project-triton/).

![Animering av en vågrät 2D sektor i wave-spridning via en scen](media/wave-simulation.gif)

## <a name="setup"></a>Konfiguration
[Projektet Akustik Unity-integrering](unity-integration.md) är dra och släpp och innehåller ett plugin-program för Unity ljud-motorn. Utöka Unity ljudkälla kontroller genom att koppla ett projekt Akustik C# kontroller komponenten till varje ljud-objektet.

[Projektet Akustik Unreal integrering](unreal-integration.md) innehåller redigerare och spelskript plugin-program för Unreal och en Wwise mixer-plugin-programmet. En anpassad ljud komponent utökar välkända Wwise funktioner inom Unreal med live Akustik design. Design kontroller är också tillgängliga i Wwise på mixer-plugin-programmet.

## <a name="workflow"></a>Arbetsflöde
* **Skapa före:** Börja med att konfigurera ändamålet genom att välja vilka geometri besvarar Akustik, av, till exempel ignorerar ljus axlar. Redigera sedan automatisk samband tilldelningar och välja navigeringsområden att guiden lyssnare sampling. Det finns ingen manuell markup för eko/portal/rummet zoner.
* **Skapa:** En analysis-steget körs lokalt, vilket gör voxelization och andra geometriska analyser på scenen baserat på ovanstående val. Resultaten visualiseras i Redigeraren för att verifiera scen installationen. Voxel data skickas till Azure på ändamålet bidrag, och du kommer tillbaka en Akustik spel tillgång.
* **Körtid:** Läs in tillgången till din nivå och du är redo att lyssna på Akustik i din nivå. Design i Akustik live i redigeringsprogrammet med hjälp av detaljerade per källkod kontroller. Kontroller kan också styras från nivån skript.

## <a name="platforms"></a>Plattformar
Plugin-program för projektet Akustik runtime kan för närvarande distribueras till följande plattformar:
* Windows
* Android
* Xbox One

## <a name="download"></a>Ladda ned
* [Projektet Akustik Unity-plugin-programmet och exempel](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projektet Akustik Unreal & Wwise plugin-program och exempel](https://www.microsoft.com/download/details.aspx?id=58090)
  * Xbox-binärfiler och support kontakta oss via registrera dig formuläret nedan

## <a name="contact-us"></a>Kontakta oss
* [Projektet Akustik forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics)
* [Registrera dig för att få uppdateringar om projektet Akustik](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Nästa steg
* Prova en [projekt Akustik Snabbstart för Unity](unity-quickstart.md) eller för [Unreal](unreal-quickstart.md)
* Utforska den [ljud designfilosofin av projektet Akustik](design-process.md)

