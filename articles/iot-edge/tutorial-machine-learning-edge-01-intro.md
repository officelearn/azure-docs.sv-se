---
title: 'Självstudiekurs: Detaljerad genomgång av maskininlärning på Azure IoT Edge'
description: En självstudiekurs på hög nivå som går igenom de olika uppgifter som krävs för att skapa en maskininlärning från på alla nivåer i kantscenariot.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74106504"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Självstudiekurs: En heltäckande lösning med Azure Machine Learning och IoT Edge

Ofta vill IoT-program dra nytta av det intelligenta molnet och den intelligenta kanten. I den här självstudien går vi igenom utbildning av en maskininlärningsmodell med data som samlats in från IoT-enheter i molnet, distribuerar den modellen till IoT Edge och underhåller och förfinar modellen med jämna mellanrum.

Det primära målet med den här självstudien är att introducera bearbetningen av IoT-data med maskininlärning, särskilt på kanten. Även om vi berör många aspekter av ett allmänt arbetsflöde för maskininlärning, är den här självstudien inte avsedd som en djupgående introduktion till maskininlärning. Som ett typexempel försöker vi inte skapa en mycket optimerad modell för användningsfallet – vi gör bara tillräckligt för att illustrera processen att skapa och använda en livskraftig modell för IoT-databehandling.

## <a name="target-audience-and-roles"></a>Målgrupp och roller

Den här uppsättningen artiklar är avsedda för utvecklare utan tidigare erfarenhet av IoT-utveckling eller maskininlärning. Att distribuera maskininlärning på gränsen kräver kunskap om hur du ansluter ett brett spektrum av tekniker. Därför täcker den här självstudien ett helt end-to-end-scenario för att demonstrera ett sätt att ansluta dessa tekniker tillsammans för en IoT-lösning. I en verklig miljö kan dessa uppgifter fördelas mellan flera personer med olika inriktningar. Utvecklare skulle till exempel fokusera på antingen enhets- eller molnkod, medan dataexperter har utformat analysmodellerna. För att en enskild utvecklare ska kunna slutföra den här självstudien har vi gett kompletterande vägledning med insikter och länkar till mer information som vi hoppas är tillräcklig för att förstå vad som görs, samt varför.

Alternativt kan du samarbeta med medarbetare med olika roller för att följa handledningen tillsammans, föra din fulla expertis att bära, och lära dig som ett team hur saker passar ihop.

I båda fallen, för att orientera läsaren (s), varje artikel i den här guiden anger användarens roll. Dessa roller omfattar:

* Molnutveckling (inklusive en molnutvecklare som arbetar med en DevOps-kapacitet)
* Datanalys

## <a name="use-case-predictive-maintenance"></a>Användningsfall: Prediktivt underhåll

Vi baserade detta scenario på ett användningsfall som presenterades vid konferensen om prognostics and health management (PHM08) under 2008. Målet är att förutsäga återstående livslängd (RUL) av en uppsättning turbofan flygplansmotorer. Dessa data genererades med hjälp av C-MAPSS, den kommersiella versionen av MAPSS (Modular Aero-Propulsion System Simulation) programvara. Denna programvara ger en flexibel turbofan motorsimulering miljö för att bekvämt simulera hälsa, kontroll och motorparametrar.

De data som används i den här självstudien hämtas från [Turbofan motor nedbrytning simulering datauppsättning](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Från readme-filen:

***Experimentellt scenario***

*Datauppsättningar består av flera multivariata tidsserier. Varje datauppsättning är ytterligare uppdelad i tränings- och testundergrupper. Varje tidsserie kommer från en annan motor – det vill säga data kan anses komma från en motorpark av samma typ. Varje motor börjar med olika grader av inledande slitage och tillverkning variation som är okänd för användaren. Detta slitage och variation anses normalt, dvs det anses inte vara ett fel tillstånd. Det finns tre driftsinställningar som har en betydande inverkan på motorns prestanda. Dessa inställningar ingår också i data. Data är förorenade med sensorbuller.*

*Motorn fungerar normalt i början av varje tidsserie och utvecklar ett fel någon gång under serien. I träningsuppsättningen växer felet i magnitud tills systemet havererar. I testuppsättningen slutar tidsserien en tid före systemfel. Syftet med tävlingen är att förutsäga antalet återstående driftscykler före fel i testuppsättningen, dvs. Gav också en vektor med rul-värden (True Remaining Useful Life) för testdata.*

Eftersom uppgifterna publicerades för en tävling har flera metoder för att härleda maskininlärningsmodeller publicerats oberoende av dem. Vi fann att studera exempel är till hjälp för att förstå processen och resonemang som deltar i skapandet av en specifik maskininlärningsmodell. Se till exempel:

[Flygplan motorfel förutsägelse modell](https://github.com/jancervenka/turbofan_failure) av GitHub användare jancervenka.

[Turbofan motor nedbrytning](https://github.com/hankroark/Turbofan-Engine-Degradation) av GitHub användare hankroark.

## <a name="process"></a>Process

Bilden nedan illustrerar de grova steg vi följer i den här guiden:

![Arkitekturdiagram för processsteg](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Samla in träningsdata:** Processen börjar med att samla in träningsdata. I vissa fall har data redan samlats in och är tillgängliga i en databas, eller i form av datafiler. I andra fall, särskilt för IoT-scenarier, måste data samlas in från IoT-enheter och sensorer och lagras i molnet.

   Vi antar att du inte har en samling turbofanmotorer, så projektfilerna innehåller en enkel enhetssimulator som skickar NASA-enhetsdata till molnet.

1. **Förbered data**. I de flesta fall kräver rådata som samlas in från enheter och sensorer förberedelse för maskininlärning. Det här steget kan innebära datarensning, dataformatering eller förbearbetning för att injicera ytterligare information maskininlärning kan knappa ut.

   För våra flygplansmotormaskindata innebär dataförberedelse att beräkna explicita tid till feltider för varje datapunkt i urvalet baserat på de faktiska observationerna av data. Denna information gör det möjligt för maskininlärningsalgoritmen att hitta korrelationer mellan faktiska sensordatamönster och motorns förväntade återstående livslängd. Det här steget är mycket domänspecifikt.

1. **Bygg en maskininlärningsmodell**. Baserat på förberedda data kan vi nu experimentera med olika maskininlärningsalgoritmer och parameteriseringar för att träna modeller och jämföra resultaten med varandra.

   I det här fallet, för testning vi jämföra det förväntade resultatet beräknas av modellen med det verkliga resultatet observeras på en uppsättning motorer. I Azure Machine Learning kan vi hantera olika iterationer av modeller som vi skapar i ett modellregister.

1. **Distribuera modellen**. När vi har en modell som uppfyller våra framgångskriterier kan vi gå över till distribution. Det innebär att slå in modellen i en webbtjänstapp som kan matas med data med REST-anrop och returanalysresultat. Webbtjänstappen paketeras sedan till en dockerbehållare, som i sin tur kan distribueras antingen i molnet eller som en IoT Edge-modul. I det här exemplet fokuserar vi på distribution till IoT Edge.

1. **Underhåll och förfina modellen**. Vårt arbete är inte gjort när modellen har distribuerats. I många fall vill vi fortsätta samla in data och regelbundet överföra dessa data till molnet. Vi kan sedan använda dessa data för att omskola och förfina vår modell, som vi sedan kan distribuera om till IoT Edge.

## <a name="prerequisites"></a>Krav

För att slutföra självstudien behöver du åtkomst till en Azure-prenumeration där du har behörighet att skapa resurser. Flera av de tjänster som används i den här självstudien debiteras Azure-avgifter. Om du inte redan har en Azure-prenumeration kan du komma igång med ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/).

Du behöver också en dator med PowerShell installerat där du kan köra skript för att konfigurera en Virtuell Azure-dator som utvecklingsdator.

I det här dokumentet använder vi följande verktyg:

* En Azure IoT-hubb för datainsamling

* Azure Notebooks som vår huvudsakliga frontend för dataförberedelser och maskininlärningsexperiment. Att köra python-kod i en anteckningsbok på en delmängd av exempeldata är ett bra sätt att få snabb iterativ och interaktiv vändning under dataförberedelse. Jupyter-anteckningsböcker kan också användas för att förbereda skript för att köras i stor skala i en beräknings serverd.

* Azure Machine Learning som en backend för maskininlärning i stor skala och för maskininlärningsavbildningsgenerering. Vi kör Azure Machine Learning-backend med skript som förberetts och testats i Jupyter-anteckningsböcker.

* Azure IoT Edge för program utanför molnet av en maskininlärningsavbildning

Självklart finns det andra alternativ. I vissa scenarier kan till exempel IoT Central användas som ett alternativ utan kod för att samla in grundläggande träningsdata från IoT-enheter.

## <a name="next-steps"></a>Nästa steg

Den här självstudien är uppdelad i följande avsnitt:

1. Konfigurera din utvecklingsdator och Azure-tjänster.
2. Generera träningsdata för maskininlärningsmodulen.
3. Träna och distribuera maskininlärningsmodulen.
4. Konfigurera en IoT Edge-enhet så att den fungerar som en transparent gateway.
5. Skapa och distribuera IoT Edge-moduler.
6. Skicka data till din IoT Edge-enhet.

Fortsätt till nästa artikel för att konfigurera en utvecklingsdator och etablera Azure-resurser.

> [!div class="nextstepaction"]
> [Konfigurera en miljö för maskininlärning på IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)
