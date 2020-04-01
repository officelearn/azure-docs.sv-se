---
title: Skapa ISE-miljöer (Integration Service Environments) med LOGIC Apps REST API
description: Skapa en integrationstjänstmiljö (ISE) med hjälp av REST-API:et för Logic Apps så att du kan komma åt virtuella Azure-nätverk (VNETs) från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478826"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Skapa en integrationstjänstmiljö (ISE) med hjälp av REST-API:et för Logic Apps

Den här artikeln visar hur du skapar en [ *integrationstjänstmiljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) via LOGIC Apps REST API för scenarier där dina logikappar och integrationskonton behöver åtkomst till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). En ISE är en isolerad miljö som använder dedikerad lagring och andra resurser som hålls åtskilda från den "globala" Logic Apps-tjänsten för flera innehavare. Den här separationen minskar också all påverkan som andra Azure-klienter kan ha på dina appars prestanda. En ISE ger dig också dina egna statiska IP-adresser. Dessa IP-adresser är åtskilda från de statiska IP-adresser som delas av logikapparna i den offentliga tjänsten med flera innehavare.

Du kan också skapa en ISE med hjälp av [snabbstartsmallen för Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) eller med hjälp av [Azure-portalen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Logikappar, inbyggda utlösare, inbyggda åtgärder och kopplingar som körs i ISE använder en prisplan som skiljer sig från den förbrukningsbaserade prisplanen. Mer information om hur prissättning och fakturering fungerar för ISE finns i [logic apps-prismodellen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Prispriser finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Krav

* Samma [förutsättningar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) och [krav för att aktivera åtkomst för din ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) som när du skapar en ISE i Azure-portalen

* Ett verktyg som du kan använda för att skapa din ISE genom att anropa Logic Apps REST API med en HTTPS PUT-begäran. Du kan till exempel använda [Postman](https://www.getpostman.com/downloads/)eller skapa en logikapp som utför den här uppgiften.

## <a name="send-the-request"></a>Skicka begäran

Om du vill skapa din ISE genom att anropa LOGIC Apps REST API gör du den här HTTPS PUT-begäran:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01-versionen kräver att du gör din egen HTTP PUT-begäran om ISE-kopplingar.

Distributionen tar vanligtvis inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrollera distributionsstatus väljer du ikonen meddelanden i [Azure-portalen](https://portal.azure.com)i azure-verktygsfältet som öppnar meddelandefönstret.

> [!NOTE]
> Om distributionen misslyckas eller om du tar bort din ISE kan Det ta upp till en timme innan du släpper dina undernät. Den här fördröjningen innebär att du kanske måste vänta innan du återanvänder dessa undernät i en annan ISE.
>
> Om du tar bort det virtuella nätverket tar Azure vanligtvis upp till två timmar innan du släpper upp dina undernät, men den här åtgärden kan ta längre tid. 
> När du tar bort virtuella nätverk kontrollerar du att inga resurser fortfarande är anslutna. 
> Se [Ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Begärandehuvud

I begäran huvudet, inkludera dessa egenskaper:

* `Content-type`: Ange egenskapsvärdet till `application/json`.

* `Authorization`: Ange det här egenskapsvärdet till innehavartoken för kunden som har åtkomst till Azure-prenumerationen eller resursgruppen som du vill använda.

### <a name="request-body-syntax"></a>Syntax för begäran om brödtext

Här är syntaxen för begärantexten, som beskriver de egenskaper som ska användas när du skapar ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Exempel på begärantext

I det här exemplet visar begäranden exempelvärdena:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integrationstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

