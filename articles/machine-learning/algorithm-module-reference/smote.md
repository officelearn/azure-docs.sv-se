---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder SMOTE-modulen i Azure Machine Learning för att öka antalet exempel med låg incidens i en datauppsättning med hjälp av översampling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: ed6d9e86143c3a5d6c97c4bd92a07c258bbd1bbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477467"
---
# <a name="smote"></a>SMOTE

I den här artikeln beskrivs hur du använder SMOTE-modulen i Azure Machine Learning designer (förhandsversion) för att öka antalet underrepresenterade ärenden i en datauppsättning som används för maskininlärning. SMOTE är ett bättre sätt att öka antalet sällsynta fall än att bara duplicera befintliga fall.  

Du ansluter SMOTE-modulen till en datauppsättning som är *obalanserad*. Det finns många anledningar till att en datauppsättning kan vara obalanserad. Den kategori du riktar in dig på kan till exempel vara sällsynt i populationen eller så kan data vara svåra att samla in. Vanligtvis använder du SMOTE när *klassen* som du vill analysera är underrepresenterad. 
  
Modulen returnerar en datauppsättning som innehåller de ursprungliga exemplen. Det returnerar också ett antal syntetiska minoritetsprover, beroende på den procentandel som du anger.  
  
## <a name="more-about-smote"></a>Mer om SMOTE

Synthetic Minority Oversampling Technique (SMOTE) är en statistisk teknik för att öka antalet fall i din datauppsättning på ett balanserat sätt. Modulen fungerar genom att generera nya instanser från befintliga minoritetsärenden som du tillhandahåller som indata. Detta genomförande av SMOTE ändrar *inte* antalet majoritetsfall.

De nya fallen är inte bara kopior av befintliga minoritetsfall. I stället tar algoritmen exempel på *funktionsutrymmet* för varje målklass och dess närmaste grannar. Algoritmen genererar sedan nya exempel som kombinerar funktioner i målfallet med funktioner i sina grannar. Den här metoden ökar de funktioner som är tillgängliga för varje klass och gör proverna mer allmänna.
  
SMOTE tar hela datauppsättningen som indata, men ökar andelen endast minoritetsfallen. Anta till exempel att du har en obalanserad datauppsättning där bara 1 procent av fallen har målvärdet A (minoritetsklassen) och 99 procent av fallen har värdet B. Om du vill öka procentandelen minoritetsfall till två gånger den tidigare procentsatsen anger du **200** för **SMOTE-procent** i modulens egenskaper.  
  
## <a name="examples"></a>Exempel  

Vi rekommenderar att du försöker använda SMOTE med en liten datauppsättning för att se hur det fungerar. I följande exempel används datauppsättningen Blodgivning som är tillgänglig i Azure Machine Learning-designern.
  
Om du lägger till datauppsättningen i en pipeline och väljer **Visualisera** datauppsättningens utdata kan du se att av de 748 raderna eller ärendena i datauppsättningen är 570 ärenden (76 procent) av klass 0 och 178 ärenden (24 procent) av klass 1. Även om detta resultat inte är särskilt obalanserad, representerar klass 1 de personer som donerade blod, så dessa rader innehåller *det funktionsutrymme* som du vill modellera.
 
Om du vill öka antalet ärenden kan du ange värdet för **SMOTE-procentsatsen**med hjälp av multiplar av 100 enligt följande:

||Klass 0|Klass 1|totalt|  
|-|-------------|-------------|-----------|  
|Ursprunglig datauppsättning<br /><br /> (motsvarande **SMOTE-procentsats** = **0**)|570<br /><br /> 76%|178<br /><br /> 24%|748|  
|**SMOTE procent** = **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE procent** = **200**|570<br /><br /> 52 %|534<br /><br /> 48%|1,104|  
|**SMOTE procent** = **300**|570<br /><br /> 44%|712<br /><br /> 56%|1,282|  
  
> [!WARNING]
> Att öka antalet fall genom att använda SMOTE är inte garanterat att producera mer exakta modeller. Prova pipelining med olika procentsatser, olika funktionsuppsättningar och olika antal närmaste grannar för att se hur lägga till ärenden påverkar din modell.  
  
## <a name="how-to-configure-smote"></a>Konfigurera SMOTE
  
1.  Lägg till SMOTE-modulen i pipelinen. Du hittar modulen under Moduler för **dataomvandling**i kategorin **Manipulation.**

2. Anslut den datauppsättning som du vill marknadsföra. Om du vill ange funktionsutrymmet för att skapa de nya ärendena, antingen genom att bara använda specifika kolumner eller genom att utesluta vissa, använder du modulen [Välj kolumner i datauppsättning.](select-columns-in-dataset.md) Du kan sedan isolera de kolumner som du vill använda innan du använder SMOTE.
  
    Annars baseras skapandet av nya ärenden via SMOTE på *alla* kolumner som du anger som indata. Minst en kolumn i funktionskolumnerna är numerisk.
  
3.  Kontrollera att kolumnen som innehåller etiketten, eller målklassen, är markerad. SMOTE accepterar endast binära etiketter.
  
4.  SMOTE-modulen identifierar automatiskt minoritetsklassen i etikettkolumnen och hämtar sedan alla exempel för minoritetsklassen. Alla kolumner kan inte ha NaN-värden.
  
5.  I alternativet **SMOTE-procent** anger du ett heltal som anger målprocenten för minoritetsfall i utdatauppsättningen. Ett exempel:  
  
    - Du anger **0**. SMOTE-modulen returnerar exakt samma datauppsättning som du angav som indata. Det tillför inga nya minoritetsfall. I den här datauppsättningen har klassproportionen inte ändrats.  
  
    - Du anger **100**. SMOTE-modulen genererar nya minoritetsfall. Den lägger till samma antal minoritetsfall som fanns i den ursprungliga datauppsättningen. Eftersom SMOTE inte ökar antalet majoritetsfall har andelen fall av varje klass ändrats.  
  
    - Du anger **200**. Modulen fördubblar andelen minoritetsfall jämfört med den ursprungliga datauppsättningen. Detta *leder inte* till att de har dubbelt så många minoritetsfall som tidigare. Snarare ökar datauppsättningens storlek på ett sådant sätt att antalet majoritetsfall förblir detsamma. Antalet minoritetsfall ökas tills det matchar önskat procentvärde.  
  
    > [!NOTE]
    > Använd endast multiplar av 100 för SMOTE-procentsatsen.

6.  Använd alternativet **Antal närmaste grannar** för att bestämma storleken på det funktionsutrymme som SMOTE-algoritmen använder för att skapa nya ärenden. En närmaste granne är en rad med data (ett ärende) som liknar ett målfall. Avståndet mellan två fall mäts genom att kombinera de viktade vektorerna för alla funktioner.  
  
    + Genom att öka antalet närmaste grannar får du funktioner från fler fall.
    + Genom att hålla antalet närmaste grannar lågt använder du funktioner som är mer som de i det ursprungliga provet.  
  
7. Ange ett värde i rutan **Slumpmässigt frö** om du vill säkerställa samma resultat över körningar av samma pipeline, med samma data. Annars genererar modulen ett slumpmässigt utsprön baserat på processorklockvärden när pipelinen distribueras. Genereringen av ett slumpmässigt frö kan orsaka lite olika resultat över körningar.

8. Skicka pipelinen.  
  
   Utdata för modulen är en datauppsättning som innehåller de ursprungliga raderna plus ett antal tillagda rader med minoritetsfall.  

## <a name="technical-notes"></a>Tekniska anmärkningar

+ När du publicerar en modell som använder **SMOTE-modulen** tar du bort **SMOTE** från den prediktiva pipelinen innan den publiceras som en webbtjänst. Anledningen är att SMOTE är avsett att förbättra en modell under träning, inte för poängsättning. Du kan få ett felmeddelande om en publicerad prediktiv pipeline innehåller SMOTE-modulen.

+ Du kan ofta få bättre resultat om du rensar värden som saknas eller använder andra omvandlingar för att åtgärda data innan du använder SMOTE. 

+ Vissa forskare har undersökt om SMOTE är effektivt på högdimensionella eller glesa data, till exempel data som används i textklassificering eller genomikdatamängder. Detta dokument har en bra sammanfattning av effekterna och av den teoretiska giltigheten av att tillämpa SMOTE i sådana fall: [Blagus och Lusa: SMOTE för högdimensionella klassobalansdata](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Om SMOTE inte är effektivt i datauppsättningen kan andra metoder som du kan tänka dig vara:
  + Metoder för att förenkla minoritetsfallen eller undersampling av majoritetsfallen.
  + Ensemble tekniker som hjälper eleven direkt genom att använda klustring, uppsamlare, eller adaptiv öka.


## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

