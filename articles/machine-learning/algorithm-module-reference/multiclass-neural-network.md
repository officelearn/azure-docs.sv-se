---
title: 'Multiclass Neural Network: Modul Referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Multiclass Neural Network i Azure Machine Learning för att skapa en neural nätverksmodell som kan användas för att förutsäga ett mål som har flera värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920424"
---
# <a name="multiclass-neural-network-module"></a>Multiklass neurala nätverk modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en neural nätverksmodell som kan användas för att förutsäga ett mål som har flera värden. 

Neurala nätverk av det här slaget kan till exempel användas i komplexa datorseendeuppgifter, till exempel siffra- eller bokstavsigenkänning, dokumentklassificering och mönsterigenkänning.

Klassificering med hjälp av neurala nätverk är en övervakad inlärningsmetod och kräver därför en *taggad datauppsättning* som innehåller en etikettkolumn.

Du kan träna modellen genom att tillhandahålla modellen och den taggade datauppsättningen som en indata till [Train Model](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya indataexemplen.  

## <a name="about-neural-networks"></a>Om neurala nätverk

Ett neuralt nätverk är en uppsättning sammankopplade lager. Ingångarna är det första lagret och är kopplade till ett utdatalager med ett acykliskt diagram som består av viktade kanter och noder.

Mellan indata- och utdatalagren kan du infoga flera dolda lager. De flesta prediktiva uppgifter kan enkelt utföras med bara ett eller några dolda lager. Emellertid, ny forskning har visat att djupa neurala nätverk (DNN) med många lager kan vara effektiva i komplexa uppgifter såsom bild eller taligenkänning. De successiva lagren används för att modellera ökande nivåer av semantiskt djup.

Förhållandet mellan indata och utgångar lärs genom att träna neurala nätverket på indata. Diagrammets riktning fortsätter från ingångarna genom det dolda lagret och till utdatalagret. Alla noder i ett lager kopplas samman med de viktade kanterna till noder i nästa lager.

Om du vill beräkna nätverkets utdata för en viss indata beräknas ett värde vid varje nod i de dolda lagren och i utdatalagret. Värdet anges genom att beräkna den viktade summan av nodernas värden från föregående lager. En aktiveringsfunktion tillämpas sedan på den viktade summan.

## <a name="configure-multiclass-neural-network"></a>Konfigurera neurala nätverk för fleraklasser

1. Lägg till **multiclass neurala nätverk** modulen till din pipeline i designern. Du hittar den här modulen under **Maskininlärning**, **Initiera**, i kategorin **Klassificering.**

2. **Skapa träningsläge:** Använd det här alternativet för att ange hur du vill att modellen ska tränas:

    - **Enkel parameter:** Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    - **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.  

3. **Dold lagerspecifikation:** Välj vilken typ av nätverksarkitektur som ska skapas.

    - **Fullständigt anslutet fall:** Välj det här alternativet om du vill skapa en modell med standardarkitekturen för neurala nätverk. För neurala nätverksmodeller med flera klass är standarderna följande:

        - Ett dolt lager
        - Utdatalagret är helt anslutet till det dolda lagret.
        - Det dolda lagret är helt anslutet till inmatningslagret.
        - Antalet noder i indatalagret bestäms av antalet funktioner i träningsdata.
        - Antalet noder i det dolda lagret kan anges av användaren. Standardvärdet är 100.
        - Antalet noder i utdatalagret beror på antalet klasser.
  
   

5. **Antal dolda noder**: Med det här alternativet kan du anpassa antalet dolda noder i standardarkitekturen. Skriv antalet dolda noder. Standardär ett dolt lager med 100 noder.

6. **Inlärningshastigheten**: Definiera storleken på det steg som tas vid varje iteration, före korrigering. Ett större värde för inlärningshastighet kan orsaka att modellen konvergerar snabbare, men den kan överskrida lokala minima.

7. **Antal inlärningsiterationer:** Ange det maximala antalet gånger som algoritmen ska bearbeta utbildningsfallen.

8. **Diametern för de första inlärningsvikterna**: Ange nodvikterna i början av inlärningsprocessen.

9. **Drivkraften**: Ange en vikt som ska tillämpas under inlärning av noder från tidigare iterationer.
  
11. **Blanda exempel:** Välj det här alternativet om du vill blanda ärenden mellan iterationer.

    Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör pipelinen.

12. **Slumptalsutsäde:** Skriv ett värde som ska användas som utssäde, om du vill säkerställa repeterbarhet över körningar av samma pipeline.

14. Anslut en träningsdatauppsättning och en av [utbildningsmodulerna:](module-reference.md) 

    - Om du ställer in **Skapa trainer-läge** till **En parameter**använder du Train [Model](train-model.md).  
  

## <a name="results"></a>Resultat

Efter träningen är klar:

- Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 