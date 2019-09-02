---
title: Kör din första fråga med Azure Resource Graph Explorer
description: Den här artikeln vägleder dig genom stegen för att köra din första fråga från Azure Portal med Azure Resource Graph Explorer.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 8b765f0b7a8e3e610f849ebe92a14f1498c4b959
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208481"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Snabbstart: Kör din första resurs diagram fråga med Azure Resource Graph Explorer

Azure Resource graphs kraft är tillgängligt direkt i Azure Portal via Azure Resource Graph Explorer. Resurs diagram Utforskaren ger bläddringsbar information om de resurs typer och egenskaper för Azure Resource Manager som du kan fråga. Resurs diagram Utforskaren innehåller också ett rent gränssnitt för att arbeta med flera frågor, utvärdering av resultaten och till och med konvertera resultatet av vissa frågor till ett diagram som kan fästas på en Azure-instrumentpanel.

I slutet av den här snabb starten har du använt Azure Portal och resurs diagram Utforskaren för att köra din första resurs diagram fråga och fäst resultatet på en instrument panel.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Öppna [Azure Portal](https://portal.azure.com) för att hitta och använda resurs diagram Utforskaren genom att följa dessa steg för att köra din första resurs diagram fråga:

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **resurs diagram Utforskaren**.

1. I **fråga 1** -delen av fönstret anger du frågan `project name, type | limit 5` och väljer **Kör fråga**.

   > [!NOTE]
   > Eftersom den här frågan inte tillhandahåller en sorterings modifierare, `order by`till exempel att köra den här frågan flera gånger, är det troligt att en annan uppsättning resurser skapas per begäran.

1. Granska svaret på frågan på fliken **resultat** . Välj fliken **meddelanden** om du vill visa information om frågan, inklusive antalet resultat och varaktighet för frågan. Eventuella fel visas under den här fliken.

1. Uppdatera frågan till `order by` egenskapen **Name** : `project name, type | limit 5 | order by name asc`. Välj sedan **Kör fråga**.

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Det begränsar först frågeresultaten och sorterar sedan dem.

1. Uppdatera frågan till `order by` först egenskapen **namn** och sedan `limit` till de fem främsta resultaten:. `project name, type | order by name asc | limit 5` Välj sedan **Kör fråga**.

När den slutliga frågan körs flera gånger, förutsatt att ingenting i din miljö ändras, är resultaten som returneras konsekventa och som förväntat – beställt av egenskapen **namn** , men fortfarande begränsat till de fem främsta resultaten.

### <a name="schema-browser"></a>Schema webbläsare

Schema webbläsaren finns i den vänstra rutan i resurs diagram Utforskaren. I den här listan över resurser visas alla _resurs typer_ av Azure-resurser som båda stöds av Azures resurs diagram och som finns i en klient som du har åtkomst till. Om du expanderar en resurs typ eller underEgenskaper visas underordnade egenskaper som kan användas för att skapa en resurs diagram fråga. Välja resurs typ platser `where type =="<resource type>"` i rutan fråga. Att välja ett av de underordnade egenskaperna `where <propertyName> == "INSERT_VALUE_HERE"` läggs till i rutan fråga. Schema läsaren är ett bra sätt att identifiera egenskaper för användning i frågor. Se till att ersätta _infoga\_värde\_här_ med ditt eget värde, justera frågan med villkor, operatorer och funktioner för att uppnå dina avsedda resultat.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Skapa ett diagram från resurs diagram frågan

När du har kört den sista frågan ovan får du ett meddelande om att resultat uppsättningen inte är kompatibel med ett cirkel diagram visualisering om du väljer fliken **diagram** . Frågor som visar resultat kan inte göras i ett diagram, men frågor som tillhandahåller resurser kan räknas. Med hjälp av [typen fråga-Count Virtual Machines by OS](./samples/starter.md#count-virtual-machines-by-os-type)kan vi skapa en visualisering från resurs diagram frågan.

1. Ange följande fråga i delen med **frågan 1** i fönstret och välj **Kör fråga**.

   ```kusto
   where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Välj fliken **resultat** och Observera att svaret för den här frågan innehåller antal.

1. Välj fliken **diagram** . Frågan resulterar nu i visualiseringar. Ändra typen från _Välj diagram typ..._ till ett _liggande diagram_ eller _Ring diagram_ för att experimentera med tillgängliga visualiserings alternativ.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Fästa frågans visualisering på en instrument panel

När du har resultat från en fråga som kan visualiseras kan data visualiseringen fästas på en av dina instrument paneler. Följ dessa steg när du har kört frågan ovan:

1. Välj **Spara** och ange namnet "virtuella datorer efter OS-typ". Välj sedan **Spara** längst ned i den högra rutan.

1. Välj **Kör fråga** för att köra frågan igen nu när den har sparats.

1. Välj en data visualisering på fliken **diagram** . Välj **Fäst på instrument panelen**.

1. Välj antingen det Portal meddelande som visas eller Välj **instrument panel** i det vänstra fönstret.

Frågan är nu tillgänglig på din instrument panel med rubriken för den panel som matchar frågans namn. Om frågan har sparats när den har fästs, har den namnet "fråga 1" i stället.

Frågan och resulterande data visualisering körs och uppdateras varje gången instrument panelen laddas, vilket ger real tids och dynamiska insikter i din Azure-miljö direkt i ditt arbets flöde.

> [!NOTE]
> Frågor som resulterar i en lista kan också fästas på instrument panelen. Funktionen är inte begränsad till data visualiseringar av frågor.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Importera exempel resurs diagram instrument paneler i Graph Explorer

Prova följande exempel på instrument paneler för att tillhandahålla exempel på resurs diagram frågor och hur du kan använda resurs Utforskaren för att förbättra ditt Azure Portal arbets flöde.

- [Resurs diagram Utforskare – exempel på instrument panel #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Exempel bild för instrument panelens exempel #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Resurs diagram Utforskare – exempel på instrument panel #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Exempel bild för instrument panelens exempel #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Antalet och diagram i exempel skärmarna ovan kan skärm bilderna variera beroende på din Azure-miljö.

1. Välj och ladda ned exempel instrument panel som du vill utvärdera.

1. I Azure Portal väljer du **instrument panel** i det vänstra fönstret.

1. Välj **överför**och leta upp och välj den instrument panels fil som har hämtats. Välj sedan **Öppna**.

Den importerade instrument panelen visas automatiskt. Eftersom det nu finns i Azure Portal kan du utforska och göra ändringar efter behov eller skapa nya instrument paneler från exemplet för att dela med dina team. Mer information om hur du arbetar med instrument paneler finns [i skapa och dela instrument paneler i Azure Portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort exempel instrument panelerna för resurs diagram från din Azure Portal-miljö kan du göra det med följande steg:

1. Välj **instrument panel** i det vänstra fönstret.

1. I list rutan instrument panel väljer du det exempel på den resurs diagram instrument panel som du vill ta bort.

1. Välj **ta bort** på instrument panelens meny längst upp på instrument panelen och välj **OK** för att bekräfta.

## <a name="next-steps"></a>Nästa steg

- Få mer information om [frågespråket](./concepts/query-language.md)
- Lär dig att [utforska resurser](./concepts/explore-resources.md)
- Kör din första fråga med [Azure CLI](first-query-azurecli.md)
- Se exempel på [startfrågor](./samples/starter.md)
- Se exempel på [avancerade frågor](./samples/advanced.md)
- Ge feedback på [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)