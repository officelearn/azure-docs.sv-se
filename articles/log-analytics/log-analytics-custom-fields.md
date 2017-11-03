---
title: "Anpassade fält i Log Analytics | Microsoft Docs"
description: "Funktionen anpassade fält i logganalys kan du skapa egna sökbara fält från OMS-data som lägger till egenskaperna för en post som samlats in.  Den här artikeln beskriver processen för att skapa ett anpassat fält och ger en detaljerad genomgång en exempelhändelse."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: bwren
ms.openlocfilehash: 9e02094f155eaade9bc5fb49c4fbb798e546e989
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="custom-fields-in-log-analytics"></a>Anpassade fält i logganalys
Den **anpassade fält** funktionen för Log Analytics kan du utöka befintliga poster i OMS-databasen genom att lägga till egna sökbara fält.  Anpassade fält fylls automatiskt i från data som hämtats från andra egenskaper i samma post.

![Översikt över anpassade fält](media/log-analytics-custom-fields/overview.png)

Posten exemplet nedan har till exempel användbara data begravd i händelsebeskrivningen.  Extrahera data i separata egenskaper gör den tillgänglig för exempelvis sortera och filtrera.

![Logga sökknappen](media/log-analytics-custom-fields/sample-extract.png)

> [!NOTE]
> Du är begränsad till 100 anpassade fält i din arbetsyta i förhandsgranskningen.  Den här gränsen ska expanderas när den här funktionen når allmän tillgänglighet.
> 
> 

## <a name="creating-a-custom-field"></a>Skapa ett anpassat fält
När du skapar ett anpassat fält förstå logganalys vilka data som ska användas för att fylla i dess värde.  En teknik från Microsoft Research kallas FlashExtract används för att snabbt identifiera dessa data.  I stället för att kräva att du explicit anvisningar logganalys lär sig om de data som du vill extrahera från de exempel som du anger.

Följande avsnitt innehåller proceduren för att skapa ett anpassat fält.  Är en genomgång av en exempel-extrahering längst ned i den här artikeln.

> [!NOTE]
> Det anpassade fältet fylls när poster som matchar de angivna kriterierna läggs till datalagret OMS så visas endast på poster som samlas in när det anpassade fältet har skapats.  Anpassat fält kommer inte att lägga till poster som redan finns i datalagret när den skapas.
> 
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Steg 1 – identifiera vilka poster som har anpassade fält
Det första steget är att identifiera de poster som får det anpassade fältet.  Du börjar med en [standard loggen Sök](log-analytics-log-searches.md) och välj sedan en post som fungerar som den modell som logganalys lära dig från.  När du anger att du kommer att extrahera data i ett anpassat fält i **fältet Extraheringsguiden** öppnas där du validerar och finjustera villkoret.

1. Gå till **loggen Sök** och använda en [att hämta posterna](log-analytics-log-searches.md) som har det anpassade fältet.
2. Markera en post som Log Analytics för att fungera som en modell för att extrahera data för att fylla i det anpassade fältet.  Du identifierar de data som du vill extrahera från den här posten och Log Analytics använder den här informationen för att bestämma logiken för att fylla i det anpassade fältet för alla liknande poster.
3. Klicka på knappen till vänster om varje text-egenskap i posten och välj **extrahera fält från**.
4. Den **fältet Extraheringsguiden öppnas**, och den valda posten visas i den **Main exempel** kolumn.  Det anpassade fältet ska definieras för de posterna som har samma värden i de egenskaper som är markerade.  
5. Om markeringen är inte exakt vad du vill kan du välja ytterligare fält att begränsa kriterierna.  För att kunna ändra värdena för villkoret måste du avbryta och väljer en annan post som matchar de villkor du vill använda.

### <a name="step-2---perform-initial-extract"></a>Steg 2 – utför inledande extrahera.
När du har identifierat de poster som har det anpassade fältet kan identifiera du de data som du vill extrahera.  Log Analytics använder den här informationen för att identifiera liknande mönster i liknande poster.  I steg efter detta kommer du att kunna Validera resultaten och visa ytterligare information om logganalys ska användas i sin analys.

1. Markera texten i exempelpost som du vill fylla i det anpassade fältet.  Sedan visas en dialogruta för att ange ett namn på fältet och utföra de inledande extrahera.  Tecknen  **\_CF** läggs automatiskt.
2. Klicka på **extrahera** att utföra en analys av insamlade poster.  
3. Den **sammanfattning** och **sökresultat** avsnitt visas resultatet av rättigheten extrahera så kan du granska dess noggrannhet.  **Sammanfattning** visar de kriterier som används för att identifiera poster och ett antal för varje datavärdena identifieras.  **Sökresultat** ger en detaljerad lista över poster som matchar villkoren.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Steg 3 – verifiera noggrannhet rättigheten extrahera och skapa anpassade fält
När du har utfört den inledande extrahera visas logganalys resultaten baserat på data som redan samlats in.  Om resultatet ser korrekt kan du skapa anpassade fält med inget ytterligare arbete.  Om inte, sedan kan du förfina resultatet så att Log Analytics kan förbättra sin logik.

1. Om alla värden i den första extrahera inte stämmer klickar du på **redigera** ikonen bredvid ett felaktigt och välja **ändra den här markeringen** för att ändra valet.
2. Posten kopieras till den **ytterligare exempel** avsnittet under den **Main exempel**.  Du kan justera markeringen här för att förstå markeringen skulle ha gjort logganalys.
3. Klicka på **extrahera** att använda nya informationen för att utvärdera alla befintliga poster.  Resultatet kan ändras efter poster än den du just har ändrat baserat på den här nya intelligence.
4. Fortsätta att lägga till korrigeringar tills alla poster i rättigheten extrahera identifiera data för att fylla i det nya anpassade fälten.
5. Klicka på **spara extrahera** när du är nöjd med resultaten.  Det anpassade fältet definieras nu, men den läggs inte till några poster ännu.
6. Vänta tills den nya poster som matchar de angivna villkoren ska samlas in och kör sedan log-sökningen igen. Nya poster ska ha det anpassade fältet.
7. Använd det anpassade fältet precis som andra poster egenskapen.  Du kan använda den för att aggregera och gruppera data och även använda den för att skapa nya insikter.

## <a name="viewing-custom-fields"></a>Visa anpassade fält
Du kan visa en lista över alla anpassade fält i hanteringsgruppen från den **inställningar** panelen på OMS-instrumentpanelen.  Välj **Data** och sedan **anpassade fält** en lista över alla anpassade fält i arbetsytan.  

![Anpassade fält](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Ta bort ett anpassat fält
Det finns två sätt att ta bort ett anpassat fält.  Först är den **ta bort** alternativ för varje fält när du visar den fullständiga listan enligt beskrivningen ovan.  Den andra metoden är att hämta en post och klicka på knappen till vänster om fältet.  Menyn har ett alternativ för att ta bort det Anpassa fältet.

## <a name="sample-walkthrough"></a>Exempel genomgång
Följande avsnitt beskriver hur en komplett exempel på hur du skapar anpassade fält.  Det här exemplet extraherar tjänstnamnet i Windows-händelser som indikerar att en tjänst som ändrar tillstånd.  Detta är beroende av händelser som skapas av Service Control Manager i systemloggen på Windows-datorer.  Om du vill följa det här exemplet måste du vara [samla informationshändelser i systemloggen](log-analytics-data-sources-windows-events.md).

Vi anger du följande fråga för att returnera alla händelser från Service Control Manager som har händelse-ID 7036 som är den händelse som anger en tjänst startar eller stoppar.

![Fråga](media/log-analytics-custom-fields/query.png)

Vi kan sedan välja en post med händelse-ID 7036.

![Posten i datakällan](media/log-analytics-custom-fields/source-record.png)

Vi vill tjänstnamnet som visas i den **RenderedDescription** egenskapen och välj knappen bredvid den här egenskapen.

![Extrahera fält](media/log-analytics-custom-fields/extract-fields.png)

Den **fältet Extraheringsguiden** har öppnats och **EventLog** och **EventID** fält har markerats i den **Main exempel** kolumn.  Detta anger att det anpassade fältet definieras händelser från systemloggen med händelse-ID 7036.  Detta är tillräckliga så vi inte behöver välja andra fält.

![Main-exempel](media/log-analytics-custom-fields/main-example.png)

Vi markerar du namnet på tjänsten i den **RenderedDescription** egenskapen och Använd **Service** att identifiera namnet på tjänsten.  Det anpassade fältet anropas **Service_CF**.

![Fältet Rubrik](media/log-analytics-custom-fields/field-title.png)

Vi kan se att tjänstnamnet identifieras korrekt för vissa poster men inte för andra.   Den **sökresultat** visas som en del av namnet på den **WMI-prestanda adaptern** inte valts.  Den **sammanfattning** visar fyra poster med **DPRMA** service felaktigt ingår en extra word och två poster identifieras **moduler Installer** i stället för **Windows moduler Installer**.  

![Sökresultat](media/log-analytics-custom-fields/search-results-01.png)

Vi börjar med den **WMI-prestanda adaptern** post.  Vi klickar på redigeringsikonen och sedan **ändra den här markeringen**.  

![Ändra markeringen](media/log-analytics-custom-fields/modify-highlight.png)

Markera om du vill inkludera ordet ökas **WMI** och kör sedan rättigheten extrahera.  

![Ytterligare exempel](media/log-analytics-custom-fields/additional-example-01.png)

Vi kan se det poster för **WMI-prestanda adaptern** har korrigerats och logganalys också använder informationen för att korrigera poster för **Windows Installer för modulen**.  Vi kan se i den **sammanfattning** om avsnittet som **DPMRA** är fortfarande inte identifieras korrekt.

![Sökresultat](media/log-analytics-custom-fields/search-results-02.png)

Vi bläddrar du till en post med DPMRA-tjänsten och använder samma process för att korrigera posten.

![Ytterligare exempel](media/log-analytics-custom-fields/additional-example-02.png)

 När vi kör uppackningen kan vi se att alla våra resultat nu är korrekt.

![Sökresultat](media/log-analytics-custom-fields/search-results-03.png)

Vi kan se det **Service_CF** har skapats, men ännu inte har lagts till alla poster.

![Inledande antal](media/log-analytics-custom-fields/initial-count.png)

Efter en stund har passerat så nya händelser som samlas in, kan vi se som som den **Service_CF** fältet läggs nu till poster som matchar våra villkor.

![Slutresultatet](media/log-analytics-custom-fields/final-results.png)

Vi kan nu använda det anpassade fälten som någon annan post-egenskap.  För att illustrera detta vi skapa en fråga som grupper med den nya **Service_CF** fält som du vill kontrollera vilka tjänster som är mest aktiva.

![Grupp av frågan](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att skapa frågor med anpassade fält för villkoret.
* Övervakaren [anpassade loggfiler](log-analytics-data-sources-custom-logs.md) som du tolkas med anpassade fält.

