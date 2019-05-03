---
title: 'Importera Data: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen importera Data i Azure Machine Learning-tjänsten för att läsa in data till en machine learning-experiment från befintliga data för molntjänster.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028312"
---
# <a name="import-data-module"></a>Importera modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att läsa in data till en machine learning-experiment från befintliga data för molntjänster.  
Modulen nu innehåller en guide som hjälper dig att välja ett lagringsalternativ och välj bland befintliga prenumerationer och konton för att snabbt konfigurera alla alternativ. Behöver du redigera en befintlig dataanslutning? Inga problem; guiden läser in konfigurationsinformation om alla föregående så att du inte behöver starta igen från början. 
  
När du definierar de data du vill ha och ansluta till källan, [importdata](./import-data.md) härleder datatypen för varje kolumn baserat på de värden som den innehåller och läser in data i din Azure Machine Learning-arbetsyta. Utdata från [importdata](./import-data.md) är en datauppsättning som kan användas med alla experiment.

  
Om dina källdata ändras, kan du uppdatera datauppsättningen och lägga till nya data genom att köra [importdata](./import-data.md). Men om du inte vill nytt läsa från källdatabasen varje gång du kör experimentet väljer den **Använd cachelagrade resultat** på Sant. När det här alternativet väljs, kontrollerar modulen om experimentet har körts tidigare med hjälp av samma käll- och samma inkommande alternativ. Om en tidigare körning har hittat data i cacheminnet används, i stället för att ladda om informationen från källan.
 

## <a name="data-sources"></a>Datakällor

Modulen importera Data stöder följande datakällor. Klicka på länkarna för detaljerade anvisningar och exempel på användning av varje datakälla. 
 
Om du inte är säker på hur eller där du ska lagra dina data finns i den här guiden till vanliga datascenarier i data science process:  [Scenarier för avancerade analyser i Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Datakälla| Använda med|
|-----------|-----------|  
|[Webbadress till via HTTP](./import-from-web-url-via-http.md)|Hämta data som finns på en URL som använder HTTP och som har angetts i CSV, TVS, ARFF eller SvmLight format|  
|[Import från Azure Blob Storage](./import-from-azure-blob-storage.md) |Hämta data som lagras i Azure blob service|  

## <a name="how-to-use-import-data"></a>Hur du använder importera Data
 
1. Lägg till den **importdata** modulen i experimentet. Du hittar den här modulen i den **Data indata och utdata** kategori i gränssnittet.

2. Klicka på **starta Dataimportguiden** att konfigurera datakällan med hjälp av en guide.

    Guiden hämtar kontonamnet och autentiseringsuppgifter och hjälper dig konfigurera andra alternativ. Om du redigerar en befintlig konfiguration, läser det in de aktuella värdena först.

3. Om du inte vill använda guiden klickar du på **datakälla**, och välj typ av molnbaserad lagring som du läser från. 

    Ytterligare inställningar beror på vilken typ av lagring som du väljer, och huruvida lagringsutrymmet är skyddad eller inte. Du kan behöva ange kontonamn, typ eller autentiseringsuppgifter. Vissa datakällor kräver inte autentisering; för andra, kanske du behöver veta namnet på kontot, en nyckel eller behållarnamn.

4. Välj den **Använd cachelagrade resultat** om du vill cachelagra datauppsättningen för återanvändning på efterföljande körningar.

    Om vi antar att det har inga andra ändringar i modulparametrar, experimentet läser in data endast första gången modulen körs, och därefter använder en cachelagrad version av datauppsättningen.

    Avmarkera det här alternativet om du vill läsa in data varje gång du kör experimentet.

5. Kör experimentet.

    När Importera Data har lästs in data i gränssnittet den härleder datatypen för varje kolumn baserat på de värden som den innehåller numeriska eller kategorisk.

    - Om det finns en rubrik för rubriken namn på kolumnerna i utdatauppsättningen.

    - Om det finns inga befintliga kolumnrubriker i data, genereras nya kolumnnamnen med hjälp av format Kol1 col2... , coln *.

## <a name="results"></a>Resultat

När importen har slutförts klickar du på datauppsättningen för utdata och välj **visualisera** att se om data har importerats.

Om du vill spara data användas igen i stället för att importera en ny uppsättning data varje gång experimentet har körts, högerklickar du på utdata och välj **Spara som datamängd**. Välj ett namn för datauppsättningen. Sparade datauppsättningen bevarar data vid tidpunkten för sparar och data uppdateras inte när experimentet körs, även om datauppsättningen i experimentet ändras. Detta kan vara praktiskt för att ta ögonblicksbilder av data.

När du har importerat data behöva ha vissa ytterligare förberedelser för modellering och analys:


- Använd [redigera Metadata](./edit-metadata.md) ändra kolumnnamn, att hantera en kolumn som en annan datatyp eller att vissa kolumner är etiketter eller funktioner.

- Använd [Välj kolumner i datauppsättning](./select-columns-in-dataset.md) att välja en delmängd med kolumner för att transformera eller använda i modellering. Transformerade eller borttagna kolumner kan enkelt vara återkopplas den till den ursprungliga datauppsättningen genom att använda den [Lägg till kolumner](./add-columns.md) modulen.  

- Använd [partitionera och ta prover](./partition-and-sample.md) att dela upp datauppsättningen, utföra sampling eller hämta de översta n raderna.

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 