---
title: Hantera Azure Resource Manager-grupper med hjälp av Azure portal | Microsoft Docs
description: Använd Azure-portalen för att hantera dina Azure Resource Manager-grupper.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: cb1eb5ac27c53f4c0d48fe3644febc62f848486d
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484703"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Hantera resursgrupper i Azure Resource Manager med hjälp av Azure-portalen

Lär dig hur du använder den [Azure-portalen](https://portal.azure.com) med [Azure Resource Manager](resource-group-overview.md) att hantera dina Azure-resursgrupper. Hantera Azure-resurser finns i [hantera Azure-resurser med hjälp av Azure portal](./manage-resources-portal.md).

Andra artiklar om hur du hanterar resursgrupper:

- [Hantera Azure-resursgrupper med hjälp av Azure CLI](./manage-resources-cli.md)
- [Hantera Azure-resursgrupper med hjälp av Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Vad är ett resursgrupp

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. I allmänhet ska du lägga till resurser som delar samma livscykel i samma resursgrupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du var metadatan ska lagras.

## <a name="create-resource-groups"></a>Skapa resursgrupper

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **resursgrupper**

    ![Lägg till resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Välj **Lägg till**.
4. Ange följande värden:

   - **Prenumeration**: Välj din Azure-prenumeration. 
   - **Resursgrupp**: Ange ett nytt resursgruppnamn. 
   - **Region**: Välj en Azure-plats, till exempel **centrala USA**.

     ![Skapa resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Välj **granska + skapa**
6. Välj **Skapa**. Det tar några sekunder att skapa en resursgrupp.
7. Välj **uppdatera** på den översta menyn för att uppdatera resursen grupplistan och välj sedan resursgruppen du skapade att öppna den. Eller välj **meddelande**(klockikon) längst upp och välj sedan **går du till resursgruppen** att öppna resursgruppen du skapade

    ![Gå till resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Lista resursgrupper

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Om du vill visa resursgrupper, Välj **resursgrupper**

    ![Bläddra resursgrupper](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Om du vill anpassa informationen som visas för resursgrupper, Välj **Redigera kolumner**. Följande skärmbild visar kolumnerna tillägg kan du lägga till det som visningen på:

## <a name="open-resource-groups"></a>Öppna resursgrupper

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **Resursgrupper**.
3. Välj den resursgrupp som du vill öppna.

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

1. Öppna resursgruppen som du vill ta bort.  Se [öppna resursgrupper](#open-resource-groups).
2. Välj **Ta bort resursgrupp**.

    ![ta bort azure-resursgrupp](./media/manage-resource-groups-portal/delete-group.png)

Mer information om hur Azure Resource Manager beställningar borttagningen av resurser finns i [borttagning av Azure Resource Manager resource](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resursgrupp

När du har skapat en Resource Manager-mall kan kan du använda Azure-portalen för att distribuera dina Azure-resurser. Skapa en mall finns i [snabbstarten: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](./resource-manager-quickstart-create-templates-use-the-portal.md). Distribuera en mall med hjälp av portalen finns i [distribuera resurser med Resource Manager-mallar och Azure-portalen](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resursgrupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resursgrupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Lås resursgrupper

Låsning förhindrar andra användare i din organisation av misstag tar bort eller ändrar kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

1. Öppna resursgruppen som du vill ta bort.  Se [öppna resursgrupper](#open-resource-groups).
2. I den vänstra rutan väljer **låser**.
3. Om du vill lägga till ett lås i resursgruppen, Välj **Lägg till**.
4. Ange **låsnamnet**, **låsa typ**, och **anteckningar**. Typer av Lås **skrivskyddad**, och **ta bort**.

    ![Lås azure-resursgrupp](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Mer information finns i [låsa resurser för att förhindra oväntade ändringar](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Resursgrupper för taggen

Du kan lägga till taggar till resursgrupper och resurser och organisera dem logiskt dina tillgångar. Mer information finns i [med taggar för att organisera Azure-resurser](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exportera resursgrupper till mallar

När du har skapat en resursgrupp kan du visa Resource Manager-mallen för resursgruppen. Exportera mallen erbjuder två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller alla hela infrastrukturen.
- Lär dig mer om mallsyntaxen genom att titta på den JavaScript Object Notation (JSON) som representerar din lösning.

Det finns två sätt att exportera en mall:

- Du kan exportera själva mallen som används för distribution. Den exporterade mallen innehåller alla parametrar och variabler exakt som de visas i den ursprungliga mallen. Den här metoden är användbar om du har distribuerat resurser via portalen och vill se mallen för att skapa dessa resurser. Mallen är enkel att använda. 
- Du kan exportera en genererad mall som representerar det aktuella tillståndet för resursgruppen. Den exporterade mallen inte är baserad på en mall som du använde för distributionen. I stället skapar den en mall som är en ”ögonblicksbild” eller ”säkerhetskopiering” för resursgruppen. Den exporterade mallen har många hårdkodade värden och troligen inte så många parametrar som du vanligtvis definierar. Använd det här alternativet för att distribuera om resurser till samma resursgrupp. Du kan behöva ändra avsevärt den om du vill använda den här mallen för en annan resursgrupp.

### <a name="export-templates-from-deployment-history"></a>Exportera mallar från distributionshistoriken

Den här metoden exporterar mallar för vissa distributioner. Om du har ändrat resurserna från portalen eller resurs som har lagts till eller tas bort i flera distributioner kan du läsa [Exportera mallar från resursgrupper](#export-templates-from-resource-groups).

1. Öppna resursgruppen som du vill exportera.  Se [öppna resursgrupper](#open-resource-groups).
2. I den vänstra rutan väljer **distributioner**, eller klicka på länken under **distributioner**.  På följande skärmbild visas **4 lyckades** eftersom det fanns fyra avgränsas distributioner med fyra olika distributionsnamn. Du kan se **1 lyckades**.

    ![Azure resource group export-mallar](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. Välj en av distributionerna i listan.
4. I den vänstra rutan väljer **mallen**. Resource Manager hämtar följande sex filer åt dig:

   - **Mall**– Mallen som definierar infrastrukturen för lösningen. När du skapade lagringskontot på portalen använde Resource Manager en mall för att distribuera det och sparade mallen för framtida bruk.
   - **Parametrar** – En parameterfil som du kan använda för att skicka in värden under distributionen. Den innehåller de värden som du angav under den första distributionen. Du kan ändra dessa värden när du distribuerar om mallen.
   - **CLI** – en Azure-CLI-skriptfil som du kan använda för att distribuera mallen.
   - **PowerShell** – En Azure PowerShell-skriptfil som du kan använda för att distribuera mallen.
   - **.NET** – En .NET-klass som du kan använda för att distribuera mallen.
   - **Ruby**– En Ruby-klass som du kan använda för att distribuera mallen.

     Som standard visar portalen mallen.

5. Välj **hämta** du exporterar en mall till den lokala datorn.

    ![Azure resource group export-mallar](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>Exportera mallar från resursgrupper

Om du har ändrat dina resurser från portalen eller lagts till/ta bort resurser i flera distributioner, motsvarar hämta en mall från distributionshistoriken inte det aktuella tillståndet för resursgruppen. I det här avsnittet visas hur du exporterar en mall som representerar resursgruppens aktuella tillstånd. Den är avsedd som en ögonblicksbild av resursgruppen som du kan använda för att distribuera om till samma resursgrupp. Om du vill använda den exporterade mallen andra lösningar, måste du avsevärt ändra den.

1. Öppna resursgruppen som du vill exportera.  Se [öppna resursgrupper](#open-resource-groups).
2. I den vänstra rutan väljer **exportmallen**. Resource Manager hämtar följande sex filer åt dig:

   - **Mall**– Mallen som definierar infrastrukturen för lösningen. När du skapade lagringskontot på portalen använde Resource Manager en mall för att distribuera det och sparade mallen för framtida bruk.
   - **Parametrar** – En parameterfil som du kan använda för att skicka in värden under distributionen. Den innehåller de värden som du angav under den första distributionen. Du kan ändra dessa värden när du distribuerar om mallen.
   - **CLI** – en Azure-CLI-skriptfil som du kan använda för att distribuera mallen.
   - **PowerShell** – En Azure PowerShell-skriptfil som du kan använda för att distribuera mallen.
   - **.NET** – En .NET-klass som du kan använda för att distribuera mallen.
   - **Ruby**– En Ruby-klass som du kan använda för att distribuera mallen.

     Som standard visar portalen mallen.
3. Välj **hämta** du exporterar en mall till den lokala datorn.

Vissa exporterade mallar måste vissa ändringar innan de kan användas. Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).

### <a name="export-template-before-deploying"></a>Exportera mallen innan du distribuerar

Du kan använda portalen för att definiera en resurs.  Innan du distribuerar resursen, kan du visa och exportera en mall. Instruktionerna finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](./resource-manager-quickstart-create-templates-use-the-portal.md).

### <a name="fix-export-issues"></a>Åtgärda exportproblem

Alla resurstyper stöder inte funktionen för mallexport. Du kan bara se exportera problem när du exporterar från en resursgrupp i stället för från distributionshistoriken. Om din senaste distribution korrekt representerar resursgruppens aktuella tillstånd exporterar du mallen från distributionshistoriken i stället för från resursgruppen. Exportera bara från en resursgrupp när du har gjort ändringar i resursgruppen som inte har definierats i en enda mall.

Om du vill åtgärda exportproblem genom att manuellt lägga till resurserna som saknas i mallen. Felmeddelandet innehåller de resurstyper som inte kan exporteras. Leta reda på resurstypen i [mallreferensen](/azure/templates/). Om du till exempel vill lägga till en virtuell nätverksgateway manuellt kan du läsa [mallreferensen Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). Mallreferensen ger dig JSON som att lägga till resursen i mallen.

När du har fått JSON-format för resursen som du behöver hämta resurs-värden. Du kan se värdena för resursen med hjälp av GET-åtgärden i REST API för resurstypen. Till exempel om du vill hämta värdena för din virtuella nätverksgateway, se [virtuella Nätverksgatewayer - hämta](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resursgrupper

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

- Läs Azure Resource Manager i [översikt över Azure Resource Manager](./resource-group-overview.md).
- Läs Resource Manager-mallens syntax i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](./resource-group-authoring-templates.md).
- Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).
- Mallscheman för Azure Resource Manager-finns [mallreferensen](/azure/templates/).