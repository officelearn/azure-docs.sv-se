---
title: Detaljerad genomgång av Machine Learning på Azure IoT Edge | Microsoft Docs
description: En övergripande självstudiekurs som beskriver de olika uppgifterna som krävs för att skapa en slutpunkt till slutpunkt för maskininlärning på edge-scenario.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 916e48752431be41ff150c2ac84e66eb1e98e81f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057752"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Självstudier: En slutpunkt till slutpunkt-lösning med Azure Machine Learning och IoT Edge

Ofta, vill IoT-program dra nytta av det smarta molnet och en intelligent gräns. I de här självstudierna vägleder vi dig igenom träna en maskininlärningsmodell med data som samlas in från IoT-enheter i molnet, distribuerar den modellen till IoT Edge, underhålla och förfina modellen med jämna mellanrum.

Det primära syftet med den här självstudien är att införa bearbetning av IoT-data med machine learning, särskilt på gränsen. Medan vi touch många aspekter av en allmän machine learning-arbetsflöde för är den här självstudiekursen inte avsedd som en djupgående introduktion till machine learning. Som en fall in, försök vi inte att skapa en modell som är optimerad för det – vi göra bara tillräckligt för att illustrera hur du skapar och använder en lönsamma modell för bearbetning av IoT.

## <a name="target-audience-and-roles"></a>Målgrupp och roller

Den här uppsättningen med artiklar är avsedd för utvecklare utan tidigare erfarenhet av IoT utveckling eller machine learning. Distribuera machine learning-kant kräver kunskaper om hur du ansluter en mängd olika tekniker. Därför den här självstudien tar upp ett hela slutpunkt till slutpunkt för att visa ett sätt att ansluta till dessa tekniker tillsammans för en IoT-lösning. Dessa uppgifter kan fördelas mellan flera personer med olika specialiseringar i en verklig-miljö. Utvecklare skulle exempelvis fokusera på enheten eller molnet kod, medan dataexperter utformats modeller för textanalys. Om du vill aktivera en enskild utvecklare att kunna slutföra den här självstudiekursen har vi lagt till ytterligare vägledning för insikter och länkar till mer information som vi hoppas att räcker för att förstå vad som görs, samt varför.

Du kan också arbeta tillsammans med kollegor för olika roller kursen tillsammans att kunskaperna fullständig förses med, och lär dig som ett team hur saker passar ihop.

I båda fallen för att förstå smartkortsläsare, anger alla artiklarna i den här självstudien vilken roll för användaren. Rollerna är:

* Molnutveckling (inklusive en cloud-utvecklare som arbetar i en DevOps-kapacitet)
* Datanalys

## <a name="use-case-predictive-maintenance"></a>Användningsfall: Förebyggande underhåll

Vi utifrån ett användningsfall som visas på konferensen på Prognostics och hälsohantering (PHM08) i 2008 det här scenariot. Målet är att förutsäga komponenternas livslängd (RUL) av en uppsättning turbofan flygplan motorer. Dessa data har genererats med hjälp av C-MAPSS, kommersiella versioner av MAPSS (modulära Aero Propulsion System simulering) programvara. Den här programvaran ger en flexibel turbofan engine simulering miljö för att simulera bekvämt hälsotillstånd, kontroll och motor parametrar.

De data som används i den här självstudien hämtas från den [Turbofan engine försämring simulering datauppsättning](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Från filen Viktigt:

***Experimentella Scenario***

*Datauppsättningar består av flera multivarierad tidsserier. Varje datauppsättning är ytterligare indelade i träning och testning delmängder. Varje tidsserie är från en annan modul – det vill säga, data som kan anses vara från en flotta med motorer av samma typ. Varje motor börjar med olika grader av inledande slitage och tillverkning variation som är okända för användaren. Den här slitage och variation anses vara normal, d.v.s., anses det inte uppstår. Det finns tre inställningar som har en betydande inverkan på motorns prestanda. De här inställningarna ingår även i data. Data är angripen sensor bruset.*

*Motorn fungerar normalt i början av varje tidsserier och utvecklar ett fel vid en viss tidpunkt under serien. I träningsmängden växer felet i storlek tills systemfel. I test-uppsättningen slutar tidsserien lite tid innan systemfel. Målet med tävlingen är att förutsäga antalet återstående operativa cykler innan misslyckades i testmängd, t.ex, antal operativa cykler efter den senaste cykel som motorn fortsätter att fungera. Du får också en vektor med värden som true återstående driftstid (RUL) för testdata.*

Eftersom data publicerades för en tävling, har flera metoder för att härleda machine learning-modeller publicerats oberoende av varandra. Vi har upptäckt att bedriva exempel är att hjälpa dig att förstå processen och skäl som ingår i skapandet av en specifik machine learning-modell. Se till exempel:

[Förutsägelsemodell för flygplan motorn fel](https://github.com/jancervenka/turbofan_failure) av GitHub användaren jancervenka.

[Turbofan engine försämring](https://github.com/hankroark/Turbofan-Engine-Degradation) av GitHub användaren hankroark.

## <a name="process"></a>Process

Bilden nedan visar ungefärlig stegen följer vi i den här självstudien:

![Arkitekturdiagram för processtegen](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Samla in utbildningsdata**: Börjar genom att samla in utbildningsdata. I vissa fall kan data har redan tagits ut och är tillgänglig i en databas eller i form av datafiler. I annat fall, särskilt för IoT-scenarier ska data samlas in från IoT-enheter och sensorer och lagras i molnet.

   Vi förutsätter att du inte har en uppsättning turbofan-motorer, så projektfilerna omfattar en enkel enhetssimulator som skickar NASA enhetens data till molnet.

1. **Förbereda data**. I de flesta fall kräver rådata som samlas in från enheter och sensorer förberedelse för machine learning. Det här steget kan omfatta data rensa, formatera om data eller Förbearbeta för att mata in ytterligare information maskininlärning kan nyckeln av.

   För vår flygplan motorn Maskindata innebär förberedelse av data att beräkna explicit tid att fel tiderna för varje datapunkt i det här exemplet utifrån de faktiska observationerna på data. Den här informationen kan de machine learning-algoritm för att hitta korrelationer mellan faktiska sensor datamönster och förväntade återstående livstiden för motorn. Det här steget är mycket domänspecifika.

1. **Skapa en modell för maskininlärning**. Baserat på förberedda data kan experimentera vi nu med olika maskininlärningsalgoritmer och parameterizations träna modeller och jämför resultatet med varandra.

   Vi kan i så fall jämför förväntade resultatet beräknas av modellen med verkliga resultat observerats på en uppsättning motorer för testning. Vi kan hantera olika iterationer av modeller som vi skapar i ett modell-register i Azure Machine Learning.

1. **Distribuera modellen**. När vi har en modell som uppfyller våra framgångskriterier kan flytta vi till distribution. Som innebär att omsluta modellen i en webbapp för tjänsten som kan matas med data med hjälp av REST-anrop och returnera analysresultat. Web service-app är sedan paketeras i en dockerbehållare, vilket i sin tur kan distribueras i molnet eller som en IoT Edge-modul. I det här exemplet fokusera vi på distribution till IoT Edge.

1. **Underhålla och förbättra modellen**. Vår arbetet utförs inte när modellen har distribuerats. I många fall vill vi fortsätta samla in data och regelbundet överföra dessa data till molnet. Vi kan sedan använda dessa data att träna och förbättra vår modell som vi sedan kan distribuera om till IoT Edge.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver åtkomst till en Azure-prenumeration där du har behörighet att skapa resurser för att slutföra den här självstudien. Flera av de tjänster som används i den här självstudien kommer att debiteras Azure. Om du inte redan har en Azure-prenumeration kanske du kan komma igång med en [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/).

Du behöver också en dator med PowerShell installerat där du kan köra skript för att ställa in en Azure virtuell dator som din utvecklingsdator.

I det här dokumentet har använder vi följande uppsättning verktyg:

* Azure IoT hub för datainsamling

* Azure-anteckningsböcker som våra viktigaste klientdelen för förberedelse av data och machine learning-experimentering. Köra python kod på en bärbar dator på en delmängd av exempeldata är ett bra sätt att få snabb iterativ och interaktiva arbetet vid dataförberedelse av. Jupyter-anteckningsböcker kan också användas för att förbereda skript körs i skala i en beräkning serverdel.

* Azure Machine Learning som serverdel för maskininlärning i skala och för bildhantering för maskininlärning. Vi kan förbättra Azure Machine Learning-serverdelen med hjälp av skript förberedde och testas i Jupyter-anteckningsböcker.

* Azure IoT Edge för ut-molnprogram av en machine learning-avbildning

Det finns självklart andra alternativ som är tillgängliga. I vissa fall, till exempel kan IoT Central användas som ett alternativ utan kod för att hämta inledande utbildningsdata från IoT-enheter.

## <a name="next-steps"></a>Nästa steg

Den här självstudien är uppdelad i följande avsnitt:

1. Ställ in din utvecklingsdator och Azure-tjänster.
2. Generera utbildningsdata för de machine learning-modulen.
3. Träna och distribuera machine learning-modul.
4. Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway.
5. Skapa och distribuera IoT Edge-moduler.
6. Skicka data till din IoT Edge-enhet.

Fortsätt till nästa artikel om du ställer in en utvecklingsdator och etablera Azure-resurser.

> [!div class="nextstepaction"]
> [Konfigurera en miljö för machine learning på IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
