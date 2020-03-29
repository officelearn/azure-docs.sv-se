---
title: 'Beslutsskog i två klasser: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Tvåklassigt beslutskog i Azure Machine Learning för att skapa en maskininlärningsmodell baserat på beslutsskogsalgoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916718"
---
# <a name="two-class-decision-forest-module"></a>Modul i skogen i två klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en maskininlärningsmodell baserat på beslutets skogsalgoritm.  

Beslutsskogar är snabba, övervakade ensemblemodeller. Denna modul är ett bra val om du vill förutsäga ett mål med högst två resultat. 

## <a name="understanding-decision-forests"></a>Förstå beslutsskogar

Detta beslut skog algoritm är en ensemble inlärningsmetod avsedd för klassificering uppgifter. Ensemble metoder bygger på den allmänna principen att i stället för att förlita sig på en enda modell, kan du få bättre resultat och en mer generaliserad modell genom att skapa flera relaterade modeller och kombinera dem på något sätt. Generellt ensemble modeller ger bättre täckning och noggrannhet än enda beslut träd. 

Det finns många sätt att skapa individuella modeller och kombinera dem i en ensemble. Just detta genomförande av ett beslut skogsarbeten genom att bygga flera beslut träd och sedan **rösta** på de mest populära produktionen klass. Röstning är en av de mer kända metoderna för att generera resultat i en ensemblemodell. 

+ Många enskilda klassificeringsträd skapas med hjälp av hela datauppsättningen, men olika (vanligtvis randomiserade) startpunkter. Detta skiljer sig från den slumpmässiga skogsmetoden, där de enskilda beslutsträden endast kan använda någon randomiserad del av data eller funktioner.
+ Varje träd i beslutet skogsträd ut en icke-normaliserad frekvens histogram av etiketter. 
+ Aggregeringsprocessen summerar dessa histogram och normaliserar resultatet för att få "sannolikheter" för varje etikett. 
+ De träd som har hög förutsägelse förtroende kommer att ha en större betydelse i det slutliga beslutet av ensemblen.

Beslutsträd i allmänhet har många fördelar för klassificeringsuppgifter:
  
- De kan fånga icke-linjära beslutsgränser.
- Du kan träna och förutsäga på massor av data, eftersom de är effektiva i beräkning och minnesanvändning.
- Funktionsval integreras i utbildnings- och klassificeringsprocesserna.  
- Träd kan rymma bullriga data och många funktioner.  
- De är icke-parametriska modeller, vilket innebär att de kan hantera data med olika distributioner. 

Men enkla beslut träd kan överlätt på data, och är mindre generaliserbara än träd ensembler.

Mer information finns i [Beslut skogar](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Konfigurerar du
  
1.  Lägg till modulen **Beslutsskog i två klasser** i pipelinen i Azure Machine Learning och öppna fönstret **Egenskaper** för modulen. 

    Du hittar modulen under **Machine Learning**. Expandera **Initiera**och sedan **klassificering**.  
  
2.  För **omsamplingsmetod**väljer du den metod som används för att skapa de enskilda träden.  Du kan välja mellan **Uppsamlare** eller **Replikera**.  
  
    -   **Uppsamlare:** Uppsamlare kallas också *bootstrap aggregating*. I den här metoden odlas varje träd på ett nytt urval, skapat genom att slumpmässigt sampling av den ursprungliga datauppsättningen med ersättning tills du har en datauppsättning storleken på originalet.  
  
         Modellernas resultat kombineras med *omröstning*, vilket är en form av aggregering. Varje träd i ett klassificeringsbeslut skog ut en orormaliserad frekvens histogram av etiketter. Aggregeringen är att summera dessa histogram och normalisera för att få "sannolikheter" för varje etikett. På detta sätt kommer de träd som har hög förutsägelse förtroende har en större betydelse i det slutliga beslutet av ensemblen.  
  
         Mer information finns i Wikipedia-posten för Bootstrap-sammanställning.  
  
    -   **Replikera**: I replikering tränas varje träd på exakt samma indata. Fastställandet av vilken delad predikat används för varje trädnod förblir slumpmässigt och träden kommer att vara olika.   
  
3.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Om du vet hur du vill konfigurera modellen kan du ange en specifik uppsättning värden som argument.

    -   **Parameterintervall:** Om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Du anger ett visst värdeintervall och tränaren itererar över flera kombinationer av inställningarna för att bestämma vilken kombination av värden som ger bäst resultat.
  
4.  För **Antal beslutsträd**anger du det maximala antalet beslutsträd som kan skapas i ensemblen. Genom att skapa fler beslutsträd kan du eventuellt få bättre täckning, men träningstiden ökar.  
  
    > [!NOTE]
    >  Det här värdet styr också antalet träd som visas när den tränade modellen visualiseras. Om du vill visa eller skriva ut ett enda träd kan du ange värdet till 1. Det finns dock bara ett träd (trädet med den ursprungliga uppsättningen parametrar) och inga ytterligare iterationer utförs.
  
5.  För **Maximalt djup för beslutsträden**skriver du ett tal för att begränsa det maximala djupet för ett beslutsträd. Öka djupet av trädet kan öka precisionen, med risk för viss övermontering och ökad träningstid.
  
6.  För **Antal slumpmässiga delningar per nod**anger du antalet delningar som ska användas när varje nod i trädet skapas. En *delning* innebär att funktioner i varje nivå i trädet (noden) delas upp slumpmässigt.
  
7.  För **Minsta antal prover per bladnod**anger du det minsta antal fall som krävs för att skapa en terminalnod (löv) i ett träd.
  
     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel med standardvärdet 1 kan även ett enskilt ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste utbildningsdata innehålla minst fem fall som uppfyller samma villkor.  
  
8.  Välj alternativet **Tillåt okända värden för kategoriska funktioner om** du vill skapa en grupp för okända värden i utbildnings- eller valideringsuppsättningarna. Modellen kan vara mindre exakt för kända värden, men det kan ge bättre förutsägelser för nya (okända) värden. 

     Om du avmarkerar det här alternativet kan modellen bara acceptera de värden som finns i träningsdata.
  
9. Bifoga en märkt datauppsättning och en av [utbildningsmodulerna:](module-reference.md)  
  
    -   Om du ställer in **Skapa trainer-läge** till **En parameter**använder du modulen [Tågmodell.](./train-model.md)  
    
## <a name="results"></a>Resultat

Efter träningen är klar:

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för bedömning lägger du till modul **poängmodell** i en pipeline.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 