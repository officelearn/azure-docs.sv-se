---
title: 'Snabbstart: Din första portalfråga'
description: I den här snabbstarten följer du stegen för att köra din första fråga från Azure-portalen med Azure Resource Graph Explorer.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 5cf355e78ad51e06d7ba27d48dd352f35b4c0740
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74406803"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Snabbstart: Kör din första Resursdiagramfråga med Utforskaren i Azure Resource Graph

Kraften i Azure Resource Graph är tillgänglig direkt i Azure Portal via Azure Resource Graph Explorer. Resource Graph Explorer innehåller webbbar information om azure Resource Manager-resurstyper och egenskaper som du kan fråga efter. Resource Graph Explorer tillhandahåller också ett rent gränssnitt för att arbeta med flera frågor, utvärdera resultaten och till och med konvertera resultaten av vissa frågor till ett diagram som kan fästas på en Azure-instrumentpanel.

I slutet av den här snabbstarten har du använt Azure Portal och Resource Graph Explorer för att köra din första Resource Graph-fråga och fästa resultaten på en instrumentpanel.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Öppna [Azure-portalen](https://portal.azure.com) för att hitta och använda Resource Graph Explorer så här för att köra din första Resource Graph-fråga:

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Resource Graph Explorer**.

1. I delen **Fråga 1** i fönstret `Resources | project name, type | limit 5` anger du frågan och väljer **Kör fråga**.

   > [!NOTE]
   > Eftersom det här frågeexemplet inte `order by`innehåller en sorteringsmodifierare, till exempel, kommer det sannolikt att ge en annan uppsättning resurser per begäran att köra den här frågan flera gånger.

1. Granska frågesvaret på fliken **Resultat.** Välj fliken Meddelanden om du vill se information om frågan, inklusive antalet resultat och **frågans** varaktighet. Eventuella eventuella fel visas under den här fliken.

1. Uppdatera frågan `order by` till egenskapen `Resources | project name, type | limit 5 | order by name asc` **Name:** . Välj sedan **Kör fråga**.

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Det begränsar först frågeresultaten och sorterar sedan dem.

1. Uppdatera frågan till `order by` först egenskapen `limit` **Name** och sedan `Resources | project name, type | order by name asc | limit 5`till de fem bästa resultaten: . Välj sedan **Kör fråga**.

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö förändras, resultaten returneras är konsekventa och som förväntat - beställts av egenskapen **Name,** men ändå begränsad till de fem bästa resultaten.

### <a name="schema-browser"></a>Webbläsare för schema

Schemabläddraren finns i den vänstra rutan i Resource Graph Explorer. Den här listan över resurser visar alla _resurstyper_ av Azure-resurser som både stöds av Azure Resource Graph och som finns i en klient som du har åtkomst till. Om du expanderar en resurstyp eller underegenskaper visas underordnade egenskaper som kan användas för att skapa en resursdiagramfråga.

Om du markerar `where type =="<resource type>"` platsplatserna för resurstypen i frågerutan. Om du väljer en `where <propertyName> == "INSERT_VALUE_HERE"` av de underordnade egenskaperna läggs till i frågerutan.
Schemabläddraren är ett bra sätt att identifiera egenskaper för användning i frågor. Var noga med att ersätta _INSERT\_VÄRDE\_HÄR_ med ditt eget värde, justera frågan med villkor, operatörer och funktioner för att uppnå dina avsedda resultat.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Skapa ett diagram från resursdiagramfrågan

När du har kört den sista **Charts** frågan ovan får du ett meddelande om att "resultatuppsättningen inte är kompatibel med en visualisering av cirkeldiagram". Frågor som listar resultat kan inte göras i ett diagram, men frågor som ger antal resurser kan. Använda [exempelfrågan - Räkna virtuella datorer efter OS-typ](./samples/starter.md#count-virtual-machines-by-os-type), låt oss skapa en visualisering från resursdiagramfrågan.

1. I delen **Fråga 1** i fönstret anger du följande fråga och väljer **Kör fråga**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Välj fliken **Resultat** och notera att svaret för den här frågan innehåller antal.

1. Välj fliken **Diagram.** Nu resulterar frågan i visualiseringar. Ändra typen från _Välj diagramtyp..._ till antingen _stapeldiagram_ eller _Donut-diagram_ för att experimentera med tillgängliga visualiseringsalternativ.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Fästa frågevisualiseringen på en instrumentpanel

När du har resultat från en fråga som kan visualiseras kan den datavisualiseringen sedan fästas på en av instrumentpanelerna. När du har kört frågan ovan gör du så här:

1. Välj **Spara** och ange namnet "Virtuella datorer efter OS-typ". Välj sedan **Spara** längst ned i den högra rutan.

1. Välj **Kör fråga** om du vill köra frågan igen nu när den har sparats.

1. Välj en datavisualisering på fliken **Diagram.** Välj sedan **Fäst på instrumentpanelen**.

1. Välj antingen det portalmeddelande som visas eller välj **Instrumentpanel** i den vänstra rutan.

Frågan är nu tillgänglig på instrumentpanelen med rubriken på panelen som matchar frågenamnet. Om frågan inte sparades när den fästes heter den i stället "Fråga 1".

Frågan och den resulterande datavisualiseringen körs och uppdateras varje gång instrumentpanelen läses in, vilket ger realtids- och dynamiska insikter till din Azure-miljö direkt i arbetsflödet.

> [!NOTE]
> Frågor som resulterar i en lista kan också fästas på instrumentpanelen. Funktionen är inte begränsad till datavisualiseringar av frågor.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Importera exempel på instrumentpaneler i Resource Graph Explorer

Om du vill ge exempel på Resource Graph-frågor och hur Resource Graph Explorer kan användas för att förbättra arbetsflödet för Azure-portalen kan du prova dessa exempelinstrumentpaneler.

- [Utforskaren för resursdiagram – exempel på instrumentpanel #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Exempelbild för exempelinstrumentpanelen #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Utforskaren för resursdiagram – exempel på instrumentpanel #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Exempelbild för exempelinstrumentpanelen #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Antal och diagram i ovanstående exempel instrumentpanel skärmdumpar varierar beroende på din Azure-miljö.

1. Markera och hämta den exempelinstrumentpanel som du vill utvärdera.

1. I Azure-portalen väljer du **Instrumentpanel** i den vänstra rutan.

1. Välj **Ladda upp**och leta sedan reda på och välj den hämtade exempelinstrumentpanelfilen. Välj sedan **Öppna**.

Den importerade instrumentpanelen visas automatiskt. Eftersom den nu finns i din Azure-portal kan du utforska och göra ändringar efter behov eller skapa nya instrumentpaneler från exemplet att dela med dina team. Mer information om hur du arbetar med instrumentpaneler finns [i Skapa och dela instrumentpaneler i Azure-portalen](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort exempel på Instrumentpaneler för Resursdiagram från din Azure-portalmiljö kan du göra det med följande steg:

1. Välj **Instrumentpanel** i den vänstra rutan.

1. Välj det exempel på instrumentpanelspanel som du vill ta bort i listrutan instrumentpanel.

1. Välj **Ta bort** på instrumentpanelsmenyn högst upp på instrumentpanelen och välj **Ok** för att bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du använt Azure Resource Graph Explorer för att köra din första fråga och tittat på instrumentpanelsexempel som drivs av Resource Graph. Om du vill veta mer om resursdiagramspråket fortsätter du till sidan med frågespråkinformation.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)