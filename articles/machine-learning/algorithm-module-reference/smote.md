---
title: SMOTE
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder SMOTE-modulen i Azure Machine Learning-tjänsten för att öka antalet exempel i låg frekvens i en data uppsättning med översampling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 072268af52ebf7d3bede564d8c949eec9fc9f625
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516193"
---
# <a name="smote"></a>SMOTE

Den här artikeln beskriver hur du använder **SMOTE** -modulen i Azure Machine Learning designer (för hands version) för att öka antalet undervisade ärenden i en data uppsättning som används för Machine Learning. SMOTE är ett bättre sätt att öka antalet sällsynta fall än att helt enkelt duplicera befintliga fall.  

 Du ansluter SMOTE-modulen till en data uppsättning som är *obalanserad*. Det finns många skäl till varför en data uppsättning kan vara obalanserade: den kategori som du är mål för kan vara sällsynt i populationen, eller så kan det vara svårt att samla in data. Normalt använder du SMOTE när *klassen* som du vill analysera är underordnad. 
  
 Modulen returnerar en data uppsättning som innehåller de ursprungliga exemplen plus ett ytterligare antal syntetiska minoritets exempel, beroende på procent andelen som du anger.  
  
### <a name="more-about-smote"></a>Mer om SMOTE

**SMOTE** står för en *syntetisk minoritets översamplings teknik*. Det här är en statistisk teknik för att öka antalet fall i data uppsättningen på ett balanserat sätt.  Modulen fungerar genom att generera nya instanser från befintliga minoritets fall som du anger som inmatade. Den här implementeringen av SMOTE ändrar **inte** antalet majoritets fall.

De nya instanserna är inte bara kopior av befintliga minoritets fall. algoritmen tar i stället exempel på *funktions utrymmet* för varje målklass och dess närmsta grannar och genererar nya exempel som kombinerar funktioner i mål fallet med funktioner i dess grannar. Den här metoden ökar funktionerna som är tillgängliga för varje klass och gör exemplen mer generella.
  
SMOTE tar hela data uppsättningen som indata, men det ökar bara procent andelen av minoritets fallen. Anta till exempel att du har en obalanserad data uppsättning där bara 1% av fallen har målvärdet A (minoritets klassen) och 99% av fallen har värdet B. Om du vill öka procent andelen minoritets fall till två gånger den föregående procent andelen, anger du 200 för **SMOTE procent** i modulens egenskaper.  
  
## <a name="examples"></a>Exempel  

Vi rekommenderar att du provar att använda **SMOTE** med en liten data uppsättning för att se hur det fungerar. I följande exempel används den data uppsättning för blod donation som finns i Azure Machine Learning designer.
  
Om du lägger till data uppsättningen i en pipeline och klickar på **visualisera** på data uppsättningens utdata kan du se att, med 748 rader eller fall i data uppsättningen, det finns 570 fall (76%) i klass 0, och 178 fall (24%) för klass 1. Även om detta inte är terribly, visar klass 1 de personer som har donerat blod, och därför innehåller dessa rader det *funktions utrymme* som du vill modellera.
 
Om du vill öka antalet fall kan du ange värdet för **SMOTE procent**, med hjälp av multiplar av 100, enligt följande:

||Klass 0|Klass 1|Totalt|  
|-|-------------|-------------|-----------|  
|Ursprunglig data uppsättning<br /><br /> (motsvarar **SMOTE procent** = **0**)|570<br /><br /> 76%|178<br /><br /> 24.1|748|  
|**SMOTE procent** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE procent** = **200**|570<br /><br /> 52%|534<br /><br /> 48%|1104|  
|**SMOTE procent** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1282|  
  
> [!WARNING]
>  Att öka antalet fall som använder SMOTE är inte garanterat att skapa mer exakta modeller. Du bör prova pipelineing med olika procent satser, olika funktions uppsättningar och olika antal närmsta grannar för att se hur du lägger till fall som påverkar din modell.  
  
## <a name="how-to-configure-smote"></a>Så här konfigurerar du SMOTE
  
1.  Lägg till SMOTE-modulen i din pipeline. Du hittar modulen under datatransformering-moduler i kategorin modifiering.

2. Anslut den data uppsättning som du vill öka. Om du vill ange funktions utrymmet för att skapa nya ärenden, antingen genom att enbart använda vissa kolumner eller utesluta vissa, använder du modulen [Välj kolumner i data uppsättning](select-columns-in-dataset.md) för att isolera de kolumner som du vill använda innan du använder **SMOTE**.
  
    Annars baseras skapandet av nya fall med **SMOTE** på **alla** kolumner som du anger som indata. Minst en kolumn i funktions kolumnerna är numerisk.
  
3.  Se till att kolumnen som innehåller etiketten eller mål klassen är markerad.  **SMOTE** accepterar endast binära etiketter.
  
4.  **SMOTE** -modulen identifierar automatiskt minoritets klassen i kolumnen etikett och hämtar sedan alla exempel för klassen minoritet. Alla kolumner kan inte ha NaN.
  
5.  I **procent alternativet SMOTE** anger du ett heltal som anger mål procenten för minoritets fall i data uppsättningen för utdata. Till exempel:  
  
    - Du skriver **0** (%). SMOTE-modulen returnerar exakt samma data uppsättning som du angav som indata, utan att lägga till några nya minoritets fall. I den här data uppsättningen har klassens proportion inte ändrats.  
  
    -   Du skriver **100** (%). SMOTE-modulen genererar nya minoritets fall, och lägger till samma antal minoritets fall som fanns i den ursprungliga data uppsättningen. Eftersom SMOTE inte ökar antalet majoritets fall har alla fall av varje klass nu ändrats.  
  
    -   Du skriver **200** (%). Modulen fördubblar procent andelen minoritets fall jämfört med den ursprungliga data uppsättningen. Detta resulterar **inte** i att ha två gånger så många minoritets fall som tidigare.  I stället ökas storleken på data uppsättningen på ett sådant sätt att antalet majoritets fall förblir detsamma och att antalet minoritets fall ökar tills det matchar det önskade procent värdet.  
  
    > [!NOTE]
    > Använd endast multiplar av 100 för SMOTE procent.

6.  Använd **antalet närmsta grannar** för att fastställa storleken på det funktions utrymme som används av SMOTE-algoritmen när nya fall skapas. En *närmaste granne* är en rad med data (ett fall) som liknar vissa mål fall. Avståndet mellan två fall mäts genom att kombinera de viktade vektorerna för alla funktioner.  
  
     + Genom att öka antalet närmsta grannar får du funktioner från fler fall.
     + Genom att behålla antalet närmsta grannar, använder du funktioner som är fler som de i det ursprungliga exemplet.  
  
7. Ange ett värde i text rutan för **slumpmässigt utsäde** om du vill säkerställa samma resultat över körningar av samma pipeline, med samma data. Annars genererar modulen ett slumpmässigt Dirigerings värde baserat på processorns klock värden när pipelinen distribueras, vilket kan orsaka något annorlunda resultat över körningar.

8. Köra en pipeline.  
  
     Utdata från modulen är en data uppsättning som innehåller de ursprungliga raderna plus ett antal tillagda rader med minoritets fall.  

## <a name="technical-notes"></a>Tekniska anteckningar

+ När du publicerar en modell som använder modulen **SMOTE** tar du bort **SMOTE** från den förutsägande pipelinen innan den publiceras som en webb tjänst.  Orsaken är att SMOTE är avsedd för att förbättra en modell under utbildningen och inte är avsedd att användas.  Du kan få ett fel meddelande om en publicerad förutsägbar pipeline innehåller SMOTE-modulen.

+ Du kan ofta få bättre resultat om du använder rensning av saknade värden eller andra transformeringar för att åtgärda data innan du använder SMOTE. 

+ Vissa forskare har undersökt om SMOTE är effektivt på högdimensionella eller glesa data, t. ex. sådana som används i text klassificering eller genomiks data uppsättningar. Det här dokumentet innehåller en översikt över effekterna och den teoretiska giltighets tiden för att tillämpa SMOTE i sådana fall: [Blagus och Lusa: SMOTE för storskaliga klass data](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106)

    Om SMOTE inte är effektivt i din data uppsättning kan andra metoder som du kan tänka på ta med olika metoder för att översampla minoritets fallen eller undersampla de flesta fall, samt de Ensemble-tekniker som hjälper dig direkt genom att använda klustring, bagage eller anpassningsbar förstärkning.


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 

