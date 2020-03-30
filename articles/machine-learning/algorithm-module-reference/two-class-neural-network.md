---
title: 'Neurala nätverk i två klasser: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Neurala nätverk i två klasser i Azure Machine Learning för att skapa en neural nätverksmodell som kan användas för att förutsäga ett mål som bara har två värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a5cf2b210781d43ddd1a7aa87736a53df222cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477399"
---
# <a name="two-class-neural-network-module"></a>Modul med neurala nätverk i två klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en neural nätverksmodell som kan användas för att förutsäga ett mål som bara har två värden.

Klassificering med hjälp av neurala nätverk är en övervakad inlärningsmetod och kräver därför en *taggad datauppsättning*, som innehåller en etikettkolumn. Du kan till exempel använda den här neurala nätverksmodellen för att förutsäga binära resultat, till exempel om en patient har en viss sjukdom eller om en maskin sannolikt kommer att misslyckas inom ett angivet tidsfönster.  

När du har definierat modellen tränar du den genom att tillhandahålla en taggad datauppsättning och modellen som en indata till [Train Model](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för nya indata.

### <a name="more-about-neural-networks"></a>Mer om neurala nätverk

Ett neuralt nätverk är en uppsättning sammankopplade lager. Ingångarna är det första lagret och är kopplade till ett utdatalager med ett acykliskt diagram som består av viktade kanter och noder.

Mellan indata- och utdatalagren kan du infoga flera dolda lager. De flesta prediktiva uppgifter kan enkelt utföras med bara ett eller några dolda lager. Emellertid, ny forskning har visat att djupa neurala nätverk (DNN) med många lager kan vara effektiva i komplexa uppgifter såsom bild eller taligenkänning. De successiva lagren används för att modellera ökande nivåer av semantiskt djup.

Förhållandet mellan indata och utgångar lärs genom att träna neurala nätverket på indata. Diagrammets riktning fortsätter från ingångarna genom det dolda lagret och till utdatalagret. Alla noder i ett lager kopplas samman med de viktade kanterna till noder i nästa lager.

Om du vill beräkna nätverkets utdata för en viss indata beräknas ett värde vid varje nod i de dolda lagren och i utdatalagret. Värdet anges genom att beräkna den viktade summan av nodernas värden från föregående lager. En aktiveringsfunktion tillämpas sedan på den viktade summan.
  
## <a name="how-to-configure"></a>Konfigurerar du

1.  Lägg till modulen **Neurala nätverk i två klasser** i pipelinen. Du hittar den här modulen under **Maskininlärning**, **Initiera**, i kategorin **Klassificering.**  
  
2.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Välj det här alternativet om du redan vet hur du vill konfigurera modellen.

    -   **Parameterintervall:** Om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Du anger ett visst värdeintervall och tränaren itererar över flera kombinationer av inställningarna för att bestämma vilken kombination av värden som ger bäst resultat.  

3.  För **Dold lagerspecifikation**väljer du vilken typ av nätverksarkitektur som ska skapas.  
  
    -   **Fullständigt anslutet fall**: Använder standardkarautena nätverksarkitektur, definierad för tvåklassiga neurala nätverk enligt följande:
  
        -   Har ett dolt lager.
  
        -   Utdatalagret är helt anslutet till det dolda lagret och det dolda lagret är helt anslutet till inmatningslagret.
  
        -   Antalet noder i indatalagret är lika med antalet funktioner i träningsdata.
  
        -   Antalet noder i det dolda lagret anges av användaren. Standardvärdet är 100.
  
        -   Antalet noder är lika med antalet klasser. För ett neuralt nätverk i två klasser innebär det att alla indata måste mappas till en av två noder i utdatalagret.

5.  För **utbildningsfrekvens**definierar du storleken på det steg som tas vid varje iteration, före korrigering. Ett större värde för inlärningshastighet kan orsaka att modellen konvergerar snabbare, men den kan överskrida lokala minima.

6.  För **Antal inlärningsiterationer**anger du det maximala antalet gånger som algoritmen ska bearbeta utbildningsfallen.

7.  För **diametern första inlärningsvikter**anger du nodvikterna i början av inlärningsprocessen.

8.  För **Momentum**anger du en vikt som ska tillämpas under inlärning av noder från tidigare iterationer  

10. Välj alternativet **Blanda exempel** om du vill blanda ärenden mellan iterationer. Om du avmarkerar det här alternativet bearbetas ärenden i exakt samma ordning varje gång du kör pipelinen.
  
11. För **slumptalsutsäde**skriver du ett värde som ska användas som utsände.
  
     Det är användbart att ange ett frövärde när du vill säkerställa repeterbarhet över körningar av samma pipeline.  Annars används ett systemklockvärde som utsåld, vilket kan orsaka lite olika resultat varje gång du kör pipelinen.
  
13. Lägg till en taggad datauppsättning i pipelinen och anslut en av [utbildningsmodulerna](module-reference.md).  
  
    -   Om du ställer in **Skapa trainer-läge** till **En parameter**använder du modulen [Tågmodell.](train-model.md)  
  
14. Skicka pipelinen.

## <a name="results"></a>Resultat

Efter träningen är klar:

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för bedömning lägger du till modul **poängmodell** i en pipeline.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
