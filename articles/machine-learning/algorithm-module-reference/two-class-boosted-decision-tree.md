---
title: 'Tvåklassade förstärkta beslutsträd: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Tvåklassad marknadsförd beslutsträd i Azure Machine Learning för att skapa en maskininlärningsmodell som baseras på algoritmen för marknadsförda beslutsträd.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920747"
---
# <a name="two-class-boosted-decision-tree-module"></a>Tvåklassad förstärkt beslutsträdmodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en maskininlärningsmodell som baseras på algoritmen för förstärkta beslutsträd. 

Ett förstärkt beslutsträd är en ensemble inlärningsmetod där det andra trädet korrigerar för fel i det första trädet, korrigerar det tredje trädet för felen i första och andra träd, och så vidare.  Förutsägelser är baserade på hela ensemblen av träd tillsammans som gör förutsägelse.
  
När de är korrekt konfigurerade är förstärkta beslutsträd de enklaste metoderna för att få högsta prestanda på en mängd olika maskininlärningsuppgifter. Men de är också en av de mer minnesintensiva eleverna, och den nuvarande implementeringen har allt i minnet. Därför kanske en förstärkt beslutsträdmodell inte kan bearbeta de stora datauppsättningar som vissa linjära elever kan hantera.

## <a name="how-to-configure"></a>Konfigurerar du

Den här modulen skapar en otränad klassificeringsmodell. Eftersom klassificering är en övervakad inlärningsmetod behöver du en *taggad datauppsättning* som innehåller en etikettkolumn med ett värde för alla rader för att träna modellen.

Du kan träna den här typen av modell med hjälp av [Tågmodell](././train-model.md). 

1.  I Azure Machine Learning lägger du till modulen **Marknadsfört beslutsträd** i pipelinen.
  
2.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**
  
    + **Enkel parameter:** Om du vet hur du vill konfigurera modellen kan du ange en specifik uppsättning värden som argument.
  
    + **Parameterintervall:** Om du inte är säker på de bästa parametrarna kan du hitta de optimala parametrarna med hjälp av modulen [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Du anger ett visst värdeintervall och tränaren itererar över flera kombinationer av inställningarna för att bestämma vilken kombination av värden som ger bäst resultat.
  
3.  För **Maximalt antal löv per träd**anger du det maximala antalet terminalnoder (löv) som kan skapas i valfritt träd.
  
     Genom att öka detta värde, du potentiellt öka storleken på trädet och få bättre precision, med risk för övermontering och längre träningstid.
  
4.  För **Minsta antal prover per bladnod**anger du hur många fall som krävs för att skapa en terminalnod (löv) i ett träd.  
  
     Genom att öka det här värdet ökar du tröskelvärdet för att skapa nya regler. Till exempel med standardvärdet 1 kan även ett enskilt ärende orsaka att en ny regel skapas. Om du ökar värdet till 5 måste utbildningsdata innehålla minst fem fall som uppfyller samma villkor.
  
5.  För **Utbildningsfrekvens**skriver du ett tal mellan 0 och 1 som definierar stegstorleken när du lär dig.  
  
     Inlärningsfrekvensen avgör hur snabbt eller långsamt eleven konvergerar på den optimala lösningen. Om stegstorleken är för stor kan du överskrida den optimala lösningen. Om stegstorleken är för liten tar träningen längre tid att konvergera på den bästa lösningen.
  
6.  För **Antal byggda träd**anger det totala antalet beslutsträd att skapa i ensemblen. Genom att skapa fler beslutsträd kan du eventuellt få bättre täckning, men träningstiden kommer att öka.
  
     Det här värdet styr också antalet träd som visas när den tränade modellen visualiseras. Om du vill se eller skriva ut ett enda träd anger du värdet till 1. Men när du gör det produceras bara ett träd (trädet med den ursprungliga uppsättningen parametrar) och inga ytterligare iterationer utförs.
  
7.  För **slumptalsutsäde**anger du eventuellt ett icke-negativt heltal som ska användas som slumpmässigt frövärde. Om du anger ett frö säkerställs reproducerbarhet över körningar som har samma data och parametrar.  
  
     Det slumpmässiga fröet ställs som standard in på 0, vilket innebär att det ursprungliga frövärdet erhålls från systemklockan.  Successiva körningar med hjälp av ett slumpmässigt frö kan ha olika resultat.
  

9. Träna modellen.
  
    + Om du ställer in **Skapa trainer-läge** till **En parameter**ansluter du en taggad datauppsättning och modulen [Tågmodell.](./train-model.md)  
   
## <a name="results"></a>Resultat

Efter träningen är klar:

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.

+ Om du vill använda modellen för bedömning lägger du till modul **poängmodell** i en pipeline.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 