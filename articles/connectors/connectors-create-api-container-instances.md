---
title: Distribuera & hantera Azure Container-instanser
description: Automatisera uppgifter och arbetsflöden som skapar och hanterar behållardistributioner i Azure Container Instances med hjälp av Azure Logic Apps
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046297"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Distribuera och hantera Azure Container-instanser med hjälp av Azure Logic Apps

Med Azure Logic Apps och Azure Container Instance-kopplingen kan du konfigurera automatiserade uppgifter och arbetsflöden som distribuerar och hanterar [behållargrupper](../container-instances/container-instances-container-groups.md). Container Instance-kopplingen stöder följande åtgärder:

* Skapa eller ta bort en behållargrupp
* Hämta egenskaperna för en behållargrupp
* Hämta en lista över behållargrupper
* Hämta loggarna för en behållarinstans

Använd dessa åtgärder i logikapparna för uppgifter som att köra en behållararbetsbelastning som svar på en Logic Apps-utlösare. Du kan också låta andra åtgärder använda utdata från åtgärder för behållarinstans. 

Den här kopplingen innehåller endast åtgärder, så för att starta logikappen använder du en separat utlösare, till exempel en **återkommande** utlösare för att köra en behållararbetsbelastning enligt ett regelbundet schema. Du kan också behöva utlösa en distribution av en behållargrupp efter en händelse, till exempel ankomsten av ett Outlook-e-postmeddelande. 

Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md) och [hur du skapar och hanterar behållarinstanser](../container-instances/container-instances-quickstart.md)

* Logikappen där du vill komma åt behållarinstanserna. Om du vill använda en åtgärd startar du logikappen med en annan utlösare, till exempel **upprepningsutlösaren.**

## <a name="add-a-container-instance-action"></a>Lägga till en åtgärd för behållarinstans

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * Under det sista steget där du vill lägga till en åtgärd väljer du **Nytt steg**. 

     ELLER

   * Mellan de steg där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange "behållarförekomst" som filter i sökrutan. Under åtgärdslistan väljer du den Azure Container Instance-anslutningsåtgärd som du vill ha.

1. Ange ett namn på anslutningen. 

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa logikappens arbetsflöde.

  Välj till exempel **Skapa behållargrupp** och ange egenskaperna för en behållargrupp och en eller flera behållarinstanser i gruppen, som visas i följande bild (partiell detaljnivå):

  ![Skapa containergrupp](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/aci/) eller behållargrupp [YAML-referens](../container-instances/container-instances-reference-yaml.md).

## <a name="next-steps"></a>Nästa steg

* Visa en [exempellogikapp](https://github.com/Azure-Samples/aci-logicapps-integration) som kör en behållare i Azure Container Instances för att analysera känslan av e-post eller Twitter-text

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)