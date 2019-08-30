---
title: 'Importera data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen importera data i Azure Machine Learning-tjänsten för att läsa in data i ett maskin inlärnings experiment från befintliga data tjänster i molnet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e8522c759efb0cc599bbcf8e7569f6045da571a8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128792"
---
# <a name="import-data-module"></a>Importera datamodul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att läsa in data i ett maskin inlärnings experiment från befintliga data tjänster i molnet.  
Modulen innehåller nu en guide som hjälper dig att välja ett lagrings alternativ och välja bland befintliga prenumerationer och konton för att snabbt konfigurera alla alternativ. Behöver du redigera en befintlig data anslutning? Inga problem. guiden läser in all tidigare konfigurations information så att du inte behöver börja om från början. 
  
När du har definierat de data som du vill ha och ansluter till [](./import-data.md) källan, härleds data typen för varje kolumn baserat på de värden som den innehåller och data läses in i Azure Machine Learning-arbetsytan. Utdata från [import data](./import-data.md) är en data uppsättning som kan användas med alla experiment.

  
Om dina källdata ändras kan du uppdatera data uppsättningen och lägga till nya data genom att köra [Importera data](./import-data.md)på nytt. Men om du inte vill läsa igen från källan varje gång du kör experimentet väljer du alternativet **Använd cachelagrat resultat** till sant. När det här alternativet är markerat kontrollerar modulen om experimentet har körts tidigare med samma källa och samma ingångs alternativ. Om en tidigare körning hittas används data i cacheminnet i stället för att läsa in data från källan igen.
 

## <a name="data-sources"></a>Datakällor

Modulen importera data stöder följande data källor. Klicka på länkarna om du vill ha detaljerade instruktioner och exempel på hur du använder varje data källa. 
 
Om du inte är säker på hur eller var du ska lagra dina data, se den här guiden för vanliga data scenarier i data vetenskaps processen:  [Scenarier för avancerad analys i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Datakälla| Använd med|
|-----------|-----------|  
|[Webb adress via HTTP](./import-from-web-url-via-http.md)|Hämta data som finns på en webb-URL som använder HTTP och som har angetts i formaten CSV, TSV, ARFF eller SvmLight|  
|[Importera från Azure Blob Storage](./import-from-azure-blob-storage.md) |Hämta data som lagras i Azure Blob service|  

## <a name="how-to-use-import-data"></a>Använda importera data
 
1. Lägg till modulen **Importera data** i experimentet. Du hittar den här modulen i kategorin **data indata och utdata** i-gränssnittet.

2. Konfigurera data källan med hjälp av en guide genom att klicka på **Starta data import guiden** .

    Guiden hämtar konto namnet och autentiseringsuppgifterna, och hjälper dig att konfigurera andra alternativ. Om du redigerar en befintlig konfiguration laddar den först de aktuella värdena.

3. Om du inte vill använda guiden klickar du på **data källa**och väljer den typ av molnbaserad lagring som du läser från. 

    Ytterligare inställningar beror på vilken typ av lagring du väljer och om lagrings utrymmet är skyddat eller inte. Du kan behöva ange konto namn, filtyp eller autentiseringsuppgifter. Vissa källor kräver inte autentisering. för andra kan du behöva känna till konto namnet, en nyckel eller container namn.

4. Välj alternativet **Använd cachelagrat resultat** om du vill cachelagra data uppsättningen för åter användning på efterföljande körningar.

    Förutsatt att det inte finns några andra ändringar i modulens parametrar, läser experimentet endast den första gången modulen körs och använder sedan en cachelagrad version av data uppsättningen.

    Avmarkera det här alternativet om du behöver läsa in data igen varje gången du kör experimentet.

5. Kör experimentet.

    När importera data läser in data i gränssnittet härleds data typen för varje kolumn baserat på de värden som den innehåller, antingen numeriska eller kategoriska.

    - Om det finns ett sidhuvud används rubriken för att namnge kolumnerna i data uppsättningen för utdata.

    - Om det inte finns några befintliga kolumn rubriker i data genereras nya kolumn namn med formatet col1, col2,... , coln*.

## <a name="results"></a>Resultat

När importen är klar klickar du på data uppsättningen för utdata och väljer **visualisera** för att se om data har importer ATS korrekt.

Om du vill spara data för åter användning, i stället för att importera en ny uppsättning data varje gång experimentet körs, högerklickar du på utdata och väljer **Spara som data uppsättning**. Välj ett namn för data uppsättningen. Den sparade data uppsättningen bevarar data vid tidpunkten för Spara och data uppdateras inte när experimentet körs igen, även om data uppsättningen i experimentet ändras. Detta kan vara praktiskt när du vill ta ögonblicks bilder av data.

När du har importerat data kan det behövas ytterligare förberedelser för modellering och analys:


- Använd [Redigera metadata](./edit-metadata.md) för att ändra kolumn namn, för att hantera en kolumn som en annan datatyp, eller för att ange att vissa kolumner är etiketter eller funktioner.

- Använd [Välj kolumner i data uppsättning](./select-columns-in-dataset.md) för att välja en delmängd av kolumner att transformera eller använda i modellering. De omvandlade eller borttagna kolumnerna kan enkelt återanslutas till den ursprungliga data uppsättningen med hjälp av modulen [Lägg till kolumner](./add-columns.md) .  

- Använd [partition och exempel](./partition-and-sample.md) för att dela upp data uppsättningen, utföra sampling eller hämta de översta n raderna.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 