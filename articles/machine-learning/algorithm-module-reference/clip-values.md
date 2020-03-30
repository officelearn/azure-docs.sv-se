---
title: Beskärningsvärden
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Klippvärden i Azure Machine Learning för att identifiera extremvärden och klippa ut eller ersätta deras värden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 6466cea9fe04bb308a670cb03fd3de5314758142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456615"
---
# <a name="clip-values"></a>Beskärningsvärden

I den här artikeln beskrivs en modul med Azure Machine Learning designer (förhandsversion).

Använd modulen Klippvärden för att identifiera och eventuellt ersätta datavärden som ligger över eller under ett angivet tröskelvärde med ett medelvärde, en konstant eller annat ersättningsvärde.  

Du ansluter modulen till en datauppsättning som har de tal du vill klippa, väljer kolumnerna att arbeta med och anger sedan ett tröskelvärde eller värdeintervall samt en ersättningsmetod. Modulen kan mata ut antingen bara resultaten eller de ändrade värden som läggs till den ursprungliga datauppsättningen.

## <a name="how-to-configure-clip-values"></a>Konfigurera klippvärden

Innan du börjar identifierar du de kolumner som du vill klippa ut och vilken metod du ska använda. Vi rekommenderar att du testar alla urklippsmetoder på en liten delmängd av data först.

Modulen tillämpar samma villkor och ersättningsmetod på **alla** kolumner som du tar med i markeringen. Se därför till att utesluta kolumner som du inte vill ändra.

Om du behöver använda urklippsmetoder eller olika villkor på vissa kolumner måste du använda en ny förekomst av **Klippvärden** för varje uppsättning liknande kolumner.

1.  Lägg till modulen **Klippvärden** i pipelinen och anslut den till den datauppsättning som du vill ändra. Du hittar den här modulen under **Dataomvandling**i kategorin **Skala och Minska.** 
  
1.  I **Kolumnlista**använder du kolumnväljaren för att välja de kolumner som **Klippvärden** ska användas på.  
  
1.  För **Ange tröskelvärden**väljer du ett av följande alternativ i listrutan. De här alternativen avgör hur du anger de övre och nedre gränserna för godtagbara värden kontra värden som måste klippas ut.  
  
    - **ClipPeaks**: När du klipper värden efter toppar anger du bara en övre gräns. Värden som är större än det gränsvärdet ersätts.
  
    -  **ClipSubpeaks**: När du klipper värden efter underspråk anger du bara en nedre gräns. Värden som är mindre än det gränsvärdet ersätts.  
  
    - **ClipPeaksAndSubpeaks**: När du klipper värden efter toppar och underspråk kan du ange både de övre och nedre gränserna. Värden som ligger utanför det intervallet ersätts. Värden som matchar gränsvärdena ändras inte.
  
1.  Beroende på ditt val i föregående steg kan du ställa in följande tröskelvärden: 

    + **Lägre tröskelvärde:** Visas bara om du väljer **ClipSubPeaks**
    + **Övre tröskel:** Visas bara om du väljer **ClipPeaks**
    + **Tröskelvärde**: Visas bara om du väljer **ClipPeaksAndSubPeaks**

    För varje tröskeltyp väljer du antingen **Konstant** eller **Percentil**.

1. Om du väljer **Konstant**skriver du det högsta eller lägsta värdet i textrutan. Anta till exempel att du vet att värdet 999 användes som platshållarvärde. Du kan välja **Konstant** för det övre tröskelvärdet och skriva 999 i **Konstant värde för övre tröskel.**
  
1. Om du väljer **Percentil**begränsar du kolumnvärdena till ett percentilintervall. 

    Anta till exempel att du bara vill behålla värdena i percentilområdet 10-80 och ersätta alla andra. Du väljer **Percentil**och skriver sedan 10 för **Percentilvärde för lägre tröskelvärde**och skriver 80 för **Percentil-värde för det övre tröskelvärdet**. 

    Se avsnittet om [percentiler](#examples-for-clipping-using-percentiles) för några exempel på hur du använder percentilintervall.  
  
1.  Definiera ett ersättningsvärde.

    Siffror som exakt matchar de angivna gränserna anses vara inom det tillåtna värdeintervallet och ersätts därför inte. Alla tal som faller utanför det angivna intervallet ersätts med ersättningsvärdet. 
  
    + **Ersättningsvärde för toppar**: Definierar värdet som ersättning för alla kolumnvärden som är större än det angivna tröskelvärdet.  
    + **Ersättningsvärde för underspråk:** Definierar det värde som ska användas som ersättning för alla kolumnvärden som är mindre än det angivna tröskelvärdet.  
    + Om du använder alternativet **ClipPeaksAndSubpeaks** kan du ange separata ersättningsvärden för de övre och nedre klippta värdena.  

    Följande ersättningsvärden stöds:  
  
    -   **Tröskelvärde**: Ersätter klippta värden med det angivna tröskelvärdet.  
  
    -   **Medel:** Ersätter klippta värden med medelvärdet för kolumnvärdena. Medelvärdet beräknas innan värden klipps ut.  
  
    -   **Median**: Ersätter klippta värden med medianvärdet för kolumnvärdena. Medianen beräknas innan värden klipps.   
  
    -   **Saknas**. Ersätter klippta värden med det saknade (tomma) värdet.  
  
1.  **Lägg till indikatorkolumner:** Välj det här alternativet om du vill generera en ny kolumn som talar om för dig om den angivna urklippsåtgärden som tillämpas på data på den raden. Det här alternativet är användbart när du testar en ny uppsättning urklipps- och ersättningsvärden.  
  
1. **Skriv över flagga**: Ange hur du vill att de nya värdena ska genereras. Som standard skapar **Klippvärden** en ny kolumn med toppvärdena klippta till önskat tröskelvärde. Nya värden skriver över den ursprungliga kolumnen.  
  
    Om du vill behålla den ursprungliga kolumnen och lägga till en ny kolumn med de klippta värdena avmarkerar du det här alternativet.  
  
1.  Skicka pipelinen.  
  
    Högerklicka på modulen **Klippvärden** och välj **Visualisera** eller välj modulen och växla till fliken **Utdata** på den högra panelen, klicka på histogramikonen i **portutgångarna**, för att granska värdena och se till att urklippsåtgärden uppfyllde dina förväntningar.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Exempel för klippning med percentiler

För att förstå hur urklipp av percentiler fungerar, överväga en datauppsättning med 10 rader, som har en instans vardera av värdena 1-10.  
  
- Om du använder percentilen som det övre tröskelvärdet måste 90 procent av alla värden i datauppsättningen vara mindre än det värdet vid värdet för den 90:e percentilen.  
  
- Om du använder percentilen som det lägre tröskelvärdet måste 10 procent av alla värden i datauppsättningen vara mindre än det värdet vid värdet för den tionde percentilen.  
  
1.  För **Tröskelvärden**väljer du **ClipPeaksAndSubPeaks**.  
  
1.  För **övre tröskelvärde**väljer du **Percentil**och för **Percentilnummer**skriver du 90.  
  
1.  För **det övre ersättningsvärdet**väljer du **Saknat värde**.  
  
1.  För **lägre tröskelvärde**väljer du **Percentil**och för **Percentilnummer**skriver du 10.  
  
1.  För **lägre ersättningsvärde**väljer du **Saknat värde**.  
  
1.  Avmarkera alternativet **Skriv över flagga**och välj alternativet Lägg till **indikatorkolumn**.  
  
Försök nu med samma pipeline med 60 som den övre percentiltröskeln och 30 som den nedre percentiltröskeln och använd tröskelvärdet som ersättningsvärde. I följande tabell jämförs dessa två resultat:  
  
1.  Ersätt med saknad; Övre tröskel = 90; Lägre tröskelvärde = 20  
  
1.  Ersätt med tröskelvärdet. Övre percentil = 60; Lägre percentil = 40  
  
|Ursprungliga data|Ersätt med saknad|Ersätt med tröskelvärdet|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSKT<br /><br /> 4, FALSKT<br /><br /> 5, FALSKT<br /><br /> 6, FALSKT<br /><br /> 7, FALSKT<br /><br /> 8, FALSKT<br /><br /> 9, FALSKT<br /><br /> TRUE|4, SANT<br /><br /> 4, SANT<br /><br /> 4, SANT<br /><br /> 4, SANT<br /><br /> 5, FALSKT<br /><br /> 6, FALSKT<br /><br /> 7, SANT<br /><br /> 7, SANT<br /><br /> 7, SANT<br /><br /> 7, SANT| 
 
## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 
