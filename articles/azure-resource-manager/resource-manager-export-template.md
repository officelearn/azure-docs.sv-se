---
title: Exportera en Azure Resource Manager-mall | Microsoft Docs
description: Använd Azure Resource Manager om du vill exportera en mall från en befintlig resursgrupp.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 3e1dd8ad49ceb126a14070ed641146d91419640a
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025950"
---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Exportera en Azure Resource Manager-mall från befintliga resurser
I den här artikeln får du lära dig hur du exporterar en Resource Manager-mall från befintliga resurser i din prenumeration. Du kan använda mallen som genereras för att få en bättre förståelse av mallens syntax.

Det finns två sätt att exportera en mall:

* Du kan exportera **själva mallen som du använde för en distribution**. Den exporterade mallen innehåller alla parametrar och variabler exakt som de visas i den ursprungliga mallen. Den här metoden är användbar om du har distribuerat resurser via portalen och vill se mallen för att skapa dessa resurser. Mallen är enkel att använda. 
* Du kan exportera en **genererad mall som representerar resursgruppens aktuella tillstånd**. Den exporterade mallen baseras inte på en mall som du använde för distribution. I stället skapar en mall som är en ”ögonblicksbild” eller ”backup” av resursgruppen. Den exporterade mallen har många hårdkodade värden och troligen inte så många parametrar som du vanligtvis definierar. Använd det här alternativet om du vill distribuera resurser i samma resursgrupp. För att använda den här mallen för en annan resursgrupp måste kanske du ändra avsevärt den.

Den här artikeln visar båda metoderna via portalen.

## <a name="deploy-resources"></a>Distribuera resurser
Låt oss börja med att distribuera resurser till Azure som du kan använda för att exportera som en mall. Om du redan har en resursgrupp i den prenumeration som du vill exportera till en mall kan du hoppa över det här avsnittet. Resten av den här artikeln förutsätter att du har distribuerat webbapp och SQL database-lösning som visas i det här avsnittet. Om du använder en annan lösning kan din upplevelse bli lite annorlunda, men stegen för att exportera en mall är desamma. 

1. I den [Azure-portalen](https://portal.azure.com)väljer **skapar du en resurs**.
   
      ![Välj ny](./media/resource-manager-export-template/new.png)
2. Sök efter **webbapp + SQL** och välj det bland de tillgängliga alternativen.
   
      ![Sök webbapp och SQL](./media/resource-manager-export-template/webapp-sql.png)

3. Välj **Skapa**.

      ![Välj Skapa](./media/resource-manager-export-template/create.png)

4. Ange obligatoriska värden för webbappen och SQL-databasen. Välj **Skapa**.

      ![Ange webb- och SQL-värde](./media/resource-manager-export-template/provide-web-values.png)

Distributionen kan ta någon minut. När distributionen är klar ingår lösningen i din prenumeration.

## <a name="view-template-from-deployment-history"></a>Visa en mall från distributionshistoriken
1. Gå till resursgruppen för din nya resursgrupp. Observera att portalen visar resultatet av den senaste distributionen. Välj den här länken.
   
      ![Resursgrupp](./media/resource-manager-export-template/select-deployment.png)
2. Du ser distributionshistoriken för gruppen. I ditt fall visas antagligen bara en distribution i portalen. Välj den här distributionen.
   
     ![Senaste distributionen](./media/resource-manager-export-template/select-history.png)
3. Portalen visar en sammanfattning av distributionen. Sammanfattningen innehåller statusen för distributionen och dess åtgärder samt de värden som du angav för parametrarna. Om du vill visa mallen som du använde för distributionen väljer du **Visa mall**.
   
     ![Visa distributionssammanfattning](./media/resource-manager-export-template/view-template.png)
4. Resource Manager hämtar följande sju filer åt dig:
   
   1. **Mall**– Mallen som definierar infrastrukturen för lösningen. När du skapade lagringskontot på portalen använde Resource Manager en mall för att distribuera det och sparade mallen för framtida bruk.
   2. **Parametrar** – En parameterfil som du kan använda för att skicka in värden under distributionen. Den innehåller de värden som du angav under den första distributionen. Du kan ändra dessa värden när du distribuerar om mallen.
   3. **CLI** -ett Azure CLI-skriptfil som du kan använda för att distribuera mallen.
   4. **PowerShell** – En Azure PowerShell-skriptfil som du kan använda för att distribuera mallen.
   5. **.NET** – En .NET-klass som du kan använda för att distribuera mallen.
   6. **Ruby**– En Ruby-klass som du kan använda för att distribuera mallen.
      
      Som standard visas på portalen mallen.
      
       ![Visa mall](./media/resource-manager-export-template/see-template.png)
      
Den här mallen är den mall som används för att skapa din webbapp och SQL-databas. Observera att den innehåller parametrar som gör att du kan ange olika värden under distributionen. Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).

## <a name="export-the-template-from-resource-group"></a>Exportera mallen från resursgruppen
Om du har manuellt dina resurser har ändrats eller lagts till resurser i flera distributioner, återger hämta en mall från distributionshistoriken inte det aktuella tillståndet för resursgruppen. I det här avsnittet visas hur du exporterar en mall som representerar resursgruppens aktuella tillstånd. Den är avsedd som en ögonblicksbild av resursgrupp, där du kan distribuera att samma resursgrupp. Om du vill använda den exporterade mallen andra lösningar ändra du avsevärt den.

> [!NOTE]
> Du kan inte exportera en mall för en resursgrupp som har fler än 200 resurser.
> 
> 

1. Du visar mallen för en resursgrupp genom att välja **Automationsskript**.
   
      ![Exportera resursgrupp](./media/resource-manager-export-template/select-automation.png)
   
     Resource Manager utvärderar resurserna i resursgruppen och genererar en mall för dessa resurser. Alla resurstyper stöder inte funktionen för mallexport. Ett felmeddelande om att det finns ett problem med exporten kan visas. Du lär dig hur du hanterar dessa problem i avsnittet [Åtgärda exportproblem](#fix-export-issues).
2. De sex filer som du kan använda för att distribuera om lösningen visas på nytt. Men den här gången är mallen är lite annorlunda. Observera att den genererade mallen innehåller färre parametrar än mallen i föregående avsnitt. Många av värdena (t.ex. platsen och SKU-värden) är också hårdkodade i den här mallen i stället för att acceptera ett parametervärde. Innan du återanvänder den här mallen kanske du vill redigera mallen och utnyttja parametrarna på ett bättre sätt. 
   
3. Du har ett par alternativ för att fortsätta att arbeta med den här mallen. Du kan antingen hämta mallen och arbeta med den lokalt med en JSON-redigerare, eller så kan du spara mallen i biblioteket och arbeta med den via portalen.
   
     Om du vill använda en JSON-redigerare som [VS kod](https://code.visualstudio.com/) eller [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), kanske du föredrar Hämta mall lokalt och använda den editor. Om du vill arbeta lokalt väljer du **Hämta**.
   
      ![Ladda ned mall](./media/resource-manager-export-template/download-template.png)
   
     Om du inte är registrerat med en JSON-redigerare, kanske du föredrar att redigera mallen via portalen. Resten av den här artikeln förutsätter att du har sparat mallen till biblioteket i portalen. Du gör dock samma syntaxändringar för mallen oavsett om du arbetar lokalt med en JSON-redigerare eller via portalen. Om du vill arbeta via portalen väljer du **Lägg till i bibliotek**.
   
      ![Lägg till i bibliotek](./media/resource-manager-export-template/add-to-library.png)
   
     När du lägger till en mall i biblioteket ge mallen ett namn och beskrivning. Välj sedan **Spara**.
   
     ![Mallvärden](./media/resource-manager-export-template/save-library-template.png)
4. Om du vill visa en mall som sparats i biblioteket väljer du **Fler tjänster**, skriv **Mallar** för att filtrera resultaten och välj **Mallar**.
   
      ![Det finns mallar](./media/resource-manager-export-template/find-templates.png)
5. Välj mallen med det namn som du sparade.
   
      ![Välj mall](./media/resource-manager-export-template/select-saved-template.png)

## <a name="customize-the-template"></a>Anpassa mallen
Den exporterade mallen fungerar bra om du vill skapa samma webbapp och SQL-databas för varje distribution. Resource Manager innehåller dock alternativ som gör att du kan distribuera mallar med mycket bättre flexibilitet. Den här artikeln visar hur du lägger till parametrar för databasadministratörens namn och lösenord. Du kan använda samma metod för att lägga till mer flexibilitet för andra värden i mallen.

1. Välj **Redigera** för att anpassa mallen.
   
     ![Visa mall](./media/resource-manager-export-template/select-edit.png)
2. Välj mallen.
   
     ![Redigera mall](./media/resource-manager-export-template/select-added-template.png)
3. Du kan skicka värden som du kan ange under distributionen, lägger du till följande två parametrar till den **parametrar** avsnitt i mallen:

   ```json
   "administratorLogin": {
       "type": "String"
   },
   "administratorLoginPassword": {
       "type": "SecureString"
   },
   ```

4. För att kunna använda de nya parametrarna ersätter du SQL-serverdefinitionen i **resursavsnittet**. Observera att parametervärden nu används för **administratorLogin** och **administratorLoginPassword**.

   ```json
   {
       "comments": "Generalized from resource: '/subscriptions/{subscription-id}/resourceGroups/exportsite/providers/Microsoft.Sql/servers/tfserverexport'.",
       "type": "Microsoft.Sql/servers",
       "kind": "v12.0",
       "name": "[parameters('servers_tfserverexport_name')]",
       "apiVersion": "2014-04-01-preview",
       "location": "South Central US",
       "scale": null,
       "properties": {
           "administratorLogin": "[parameters('administratorLogin')]",
           "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
           "version": "12.0"
       },
       "dependsOn": []
   },
   ```

6. Välj **OK** när du är klar redigering av mallen.
7. Välj **Spara** för att spara ändringarna i mallen.
   
     ![Spara mallen](./media/resource-manager-export-template/save-template.png)
8. Om du vill distribuera om den uppdaterade mallen väljer du **Distribuera**.
   
     ![Distribuera mallen](./media/resource-manager-export-template/redeploy-template.png)
9. Ange parametervärden och välj en resursgrupp att distribuera resurserna till.


## <a name="fix-export-issues"></a>Åtgärda exportproblem
Alla resurstyper stöder inte funktionen för mallexport. Du kan bara se exportera problem när du exporterar från en resursgrupp i stället för från distributionshistoriken. Om din senaste distribution korrekt representerar resursgruppens aktuella tillstånd exporterar du mallen från distributionshistoriken i stället för från resursgruppen. Exportera endast från en resursgrupp när du har gjort ändringar i den resursgrupp som inte är definierad i en mall.

Lös export problem genom att manuellt lägga till saknade resurser tillbaka till din mall. Felmeddelandet innehåller resurstyper som inte kan exporteras. Leta reda på resurstypen i [mallreferensen](/azure/templates/). Om du till exempel vill lägga till en virtuell nätverksgateway manuellt kan du läsa [mallreferensen Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). Mallreferensen får du JSON att lägga till resursen i mallen.

När JSON-format för resursen, behöver du resurs-värden. Du kan se värdena för resursen med hjälp av GET-åtgärden i REST-API för resurstypen. Till exempel värdena för din virtuella nätverksgateway finns [virtuella Nätverksgatewayer - hämta](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="next-steps"></a>Nästa steg

* Du kan distribuera en mall genom [PowerShell](resource-group-template-deploy.md), [Azure CLI](resource-group-template-deploy-cli.md) eller [REST API](resource-group-template-deploy-rest.md).
* Information om hur du exporterar en mall med PowerShell finns [exportera Azure Resource Manager-mallar med PowerShell](resource-manager-export-template-powershell.md).
* Information om hur du exporterar en mall med Azure CLI finns [exportera Azure Resource Manager-mallar med Azure CLI](resource-manager-export-template-cli.md).

