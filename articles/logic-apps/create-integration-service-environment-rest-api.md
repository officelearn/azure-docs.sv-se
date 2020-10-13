---
title: Skapa integrerings tjänst miljöer (ISEs) med Logic Apps REST API
description: Skapa en integrerings tjänst miljö (ISE) med hjälp av Logic Apps REST API så att du kan komma åt Azure Virtual Networks (virtuella nätverk) från Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 427b488fe6673bef505fccdaa7185d69437bceaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231324"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Skapa en integrerings tjänst miljö (ISE) med hjälp av Logic Apps REST API

Den här artikeln visar hur du skapar en [ *integrerings tjänst miljö* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) via Logic Apps REST API för scenarier där dina Logic Apps och integrations konton behöver åtkomst till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md). En ISE är en dedikerad miljö som använder dedikerad lagring och andra resurser som hålls åtskilda från den ”globala” Logic Apps-tjänsten med flera klienter. Den här separationen minskar också eventuell påverkan som andra Azure-klienter kan ha på dina appars prestanda. Med en ISE får du även egna statiska IP-adresser. De här IP-adresserna skiljer sig från de statiska IP-adresser som delas av logi Kap par i den offentliga tjänsten för flera innehavare.

Du kan också skapa en ISE med hjälp av [exemplet Azure Resource Manager snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) eller genom att använda [Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Logi Kap par, inbyggda utlösare, inbyggda åtgärder och anslutningar som körs i din ISE använder en pris plan som skiljer sig från den förbruknings bara pris planen. Information om hur priser och fakturering fungerar för ISEs finns i [pris modellen Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Pris nivåer finns i [Logic Apps prissättning](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Förutsättningar

* Samma krav [prerequisites](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) och [krav för att ge åtkomst till din ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) som när du skapar en ISE i Azure Portal

* Ett verktyg som du kan använda för att skapa din ISE genom att anropa Logic Apps REST API med en begäran om HTTPS-begäran. Du kan till exempel använda [Postman](https://www.getpostman.com/downloads/), eller så kan du bygga en logisk app som utför den här uppgiften.

## <a name="send-the-request"></a>Skicka begäran

Om du vill skapa din ISE genom att anropa Logic Apps REST API, gör du denna HTTPS-begäran:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01-versionen kräver att du gör en egen HTTP-begäran för ISE-anslutningar.

Distributionen tar vanligt vis inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrol lera distributions statusen i [Azure Portal](https://portal.azure.com)går du till Azure-verktygsfältet och väljer aviserings ikonen, som öppnar fönstret meddelanden.

> [!NOTE]
> Om distributionen Miss lyckas eller om du tar bort din ISE kan Azure ta upp till en timme innan du släpper upp dina undernät. Den här fördröjningen innebär att du kan behöva vänta innan du återanvänder dessa undernät i en annan ISE.
>
> Om du tar bort det virtuella nätverket tar Azure vanligt vis upp till två timmar innan du frigör dina undernät, men den här åtgärden kan ta längre tid. 
> Se till att inga resurser fortfarande är anslutna när du tar bort virtuella nätverk. 
> Se [ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Begärandehuvud

I begär ande huvudet inkluderar du följande egenskaper:

* `Content-type`: Ange det här egenskap svärdet till `application/json` .

* `Authorization`: Ange det här egenskap svärdet till Bearer-token för den kund som har åtkomst till den Azure-prenumeration eller resurs grupp som du vill använda.

<a name="request-body"></a>

## <a name="request-body"></a>Begärandetext

Här är syntaxen för begär ande texten, som beskriver de egenskaper som ska användas när du skapar din ISE. Om du vill skapa en ISE som tillåter att du använder ett självsignerat certifikat som är installerat på `TrustedRoot` platsen inkluderar du `certificates` objektet i definitions `properties` området för ISE. För en befintlig ISE kan du bara skicka en PATCH-begäran för `certificates` objektet. Mer information om hur du använder självsignerade certifikat finns i [säker åtkomst och data åtkomst för utgående anrop till andra tjänster och system](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

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
      },
      // Include `certificates` object to enable self-signed certificate support
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Exempel på begär ande text

Den här exempel texten visar exempel värden:

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
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
   }
}
```

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integreringstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

