---
title: Vad är Project Acoustics?
titlesuffix: Azure Cognitive Services
description: Plugin-programmet Project Acoustics Unity ger ocklusion, reverb och spatialisering för projekt som riktar in sig på VR och traditionella skärmar.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 8305eca478854eeff29268a86e4e49b697261ca2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868267"
---
# <a name="what-is-project-acoustics"></a>Vad är Project Acoustics?
Plugin-programmet Project Acoustics Unity ger ocklusion, reverb och spatialisering för projekt som riktar in sig på VR och traditionella skärmar. Med det får du ett sätt att utforma spelakustik som lägger ett lager av designavsikter över en fysikbaserad vågsimulering.

## <a name="why-use-acoustics-in-virtual-environments"></a>Varför använda akustik i virtuella miljöer?
Vi människor använder audiovisuella ledtrådar för att förstå världen omkring oss. I virtuella världar omsluts användaren med hjälp av rumsliga ljud kombinerat med akustik. Akustikverktyget som beskrivs här analyserar virtuella världar för att skapa en realistisk akustisk simulering och stöder en designprocess efter simulering. I analysen ingår både geometrin och materialen hos varje yta i världen. Simuleringen innehåller parametrar ankomstriktning, reverbeffekt, efterklangstid och ocklusion samt obstruktionseffekter.

## <a name="how-does-this-approach-to-acoustics-work"></a>Hur fungerar den här metoden för att hantera akustik?
Systemet förlitar sig på offlineberäkning av den virtuella världen, som tillåter en mer komplex simulering än om analysen skulle göras vid körning. Offlineberäkningen producerar en uppslagstabell med akustikparametrar. En designer anger regler som tillämpas på parametrarna vid körning. Justering av dessa regler kan åstadkomma hyperrealistiska effekter för högemotionell intensitet eller hyperrealistiska scener för fler bakgrundsljud.

## <a name="design-process-comparison"></a>Jämförelse av designprocessen
Plugin-programmet Project Acoustics stöder en ny designprocess för akustik i Unity-scener. För att förklara den här nya designprocessen jämför vi den med en av dagens populära akustikmetoder.

### <a name="typical-approach-to-acoustics-today"></a>Vanlig akustikmetod idag
I en vanlig akustikmetod idag drar du reverbvolymer:

![Designvy](media/reverbZonesAltSPace2.png)

Sedan justerar du parametrar för varje zon:

![Designvy](media/TooManyReverbParameters.png)

Lägg slutligen till strålspårningslogik för att få rätt ocklusion och obstruktionsfiltrering genom hela scenen, och vägsökningslogik för ankomstriktning. Med den här koden kan det tillkomma en körningskostnad. Den har även problem med jämnheten runt hörn och ”edge case” i oregelbundet utformade scener.

### <a name="an-alternative-approach-with-physics-based-design"></a>En alternativ metod med fysikbaserad design
Med metoden från plugin-programmet Unity från Project Acoustics får formen och materialen för en statisk scen. Eftersom scenen är voxeliserad och processen inte använder strålspårning behöver du inte ange ett förenklat eller vattentätt akustiknät. Du behöver inte heller markera scenen med reverbvolymer. Plugin-programmet laddar upp scenen i molnet, där det använder fysikbaserad vågsimulering. Resultatet integreras i projektet som en uppslagstabell och kan ändras för estetiska effekter eller speleffekter.

![Designvy](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>Krav
* Unity 2018.2+ för akustiska ”bakes” och Unity 5.2+ för ljuddesign och -utveckling
* Windows 64-bitars Unity Editor
* Azure Batch-prenumeration för akustiska ”bakes”
* ”.NET 4.x equivalent” måste anges för Unity-skriptkörning

## <a name="platform-support"></a>Plattformssupport
* Windows-skrivbordet (x86 och AMD64)
* Windows UWP (x86, AMD64 och ARM)
* Android (x86 och ARM64)

## <a name="download"></a>Ladda ned
Om du är intresserad av att utvärdera akustikplugin-programmet registrerar du dig [här](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) för att delta i Designer-förhandsversionen.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [designprocessen](design-process.md)
* Kom igång med att [integrera akustik i ditt Unity-projekt](getting-started.md)

