---
title: Lägga till resurser i integrationstjänstmiljöer
description: Lägga till logikappar, integrationskonton, anpassade anslutningsappar och hanterade anslutningsappar i integrationstjänstmiljön (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164883"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Lägga till resurser i integrationstjänstmiljön (ISE) i Azure Logic Apps

När du har skapat en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lägger du till resurser som logikappar, integrationskonton och anslutningsappar så att de kan komma åt resurserna i ditt virtuella Azure-nätverk. Hanterade ISE-kopplingar som blir tillgängliga när du har skapat ISE visas till exempel inte automatiskt i Logic App Designer. Innan du kan använda dessa ISE-kopplingar måste du [manuellt lägga till och distribuera dessa kopplingar till DIN ISE](#add-ise-connectors-environment) så att de visas i Logic App Designer.

> [!IMPORTANT]
> För att logikappar och integrationskonton ska fungera tillsammans i en ISE måste båda använda *samma ISE* som sin plats.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ise som du skapade för att köra dina logikappar. Om du inte har en ISE [skapar du först en ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Om du vill skapa, lägga till eller uppdatera resurser som distribueras till en ISE måste du tilldelas rollen Ägare eller Deltagare på den ISE:n, eller så har du behörigheter som ärvts via Azure-prenumerationen eller Azure-resursgruppen som är associerad med ISE. För personer som inte har behörigheter för ägare, deltagare eller ärvda kan de tilldelas rollen integrationstjänstmiljödeltagare eller rollen integrationstjänstmiljöutvecklare. Mer information om rollbaserad åtkomstkontroll (RBAC) finns i [Vad är rollbaserad åtkomstkontroll (RBAC) för Azure-resurser](../role-based-access-control/overview.md)?

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Skapa logikappar

Så här skapar du logikappar som körs i integrationstjänstmiljön (ISE) :

1. Hitta och öppna din ISE, om den inte redan är öppen. Välj **Logikappar** > **Lägg till**under **Inställningar**på ISE-menyn.

   ![Lägga till ny logikapp i ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Ange information om logikappen som du vill skapa, till exempel:

   ![Välj integrationstjänstmiljö](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Namnet på logikappen som ska skapas |
   | **Prenumeration** | Ja | Namnet på azure-prenumerationen som ska användas |
   | **Resursgrupp** | Ja | Namnet på azure-resursgruppen (ny eller befintlig) som ska användas |
   | **Location** | Ja | Under **Integrationstjänstmiljöer**väljer du den ISE som ska användas, om den inte redan är markerad. <p><p> **Viktigt:** Om du vill använda logikapparna med ett integrationskonto måste båda använda samma ISE. |
   ||||

1. När du är klar väljer du **Skapa**.

1. Fortsätt [att skapa logikappen på vanligt sätt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Skillnader i hur utlösare och åtgärder fungerar och hur de är märkta när du använder en ISE jämfört med tjänsten Logic Apps med flera innehavare finns [i Isolerad jämfört med flera innehavare i ISE-översikten](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Mer om du vill hantera logikappar och API-anslutningar i DIN ISE finns i [Hantera din integrationstjänstmiljö](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Skapa integrationskonton

Baserat på den [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) som valts när den skapades innehåller ISE specifik användning av integrationskonto utan extra kostnad. Logikappar som finns i en integrationstjänstmiljö (ISE) kan endast referera till integrationskonton som finns i samma ISE. Så för att ett integrationskonto ska fungera med logikappar i en ISE måste både integrationskontot och logikapparna använda *samma miljö* som sin plats. Mer information om integrationskonton och ISE finns i [Integrationskonton med ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Så här skapar du ett integrationskonto som använder en ISE:

1. Hitta och öppna din ISE, om den inte redan är öppen. Välj **Integrationskonton** > **Lägg till**under **Inställningar**på ISE-menyn.

   ![Lägga till nytt integrationskonto i ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Ange information om logikappen som du vill skapa, till exempel:

   ![Välj integrationstjänstmiljö](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Namn** | Ja | Namnet på det integrationskonto som du vill skapa |
   | **Prenumeration** | Ja | Namnet på den Azure-prenumeration som du vill använda |
   | **Resursgrupp** | Ja | Namnet på azure-resursgruppen (ny eller befintlig) som ska användas |
   | **Prisnivå** | Ja | Den prisnivå som ska användas för integrationskontot |
   | **Location** | Ja | Under **Integrationstjänstmiljöer**väljer du samma ISE som dina logikappar använder, om de inte redan är markerade. <p><p> **Viktigt:** Om du vill använda ditt integrationskonto med logikappar måste båda använda samma ISE. |
   ||||

1. När du är klar väljer du **Skapa**.

1. [Länka logikappen till ditt integrationskonto på vanligt sätt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Fortsätt genom att lägga till resurser på ditt integrationskonto, till exempel [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal.](../logic-apps/logic-apps-enterprise-integration-agreements.md)

1. Om du vill hantera integrationskonton i DIN ISE läser du [Hantera din integrationstjänstmiljö](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Lägg till ISE-kopplingar

Microsoft-hanterade kopplingar som blir tillgängliga när du har skapat ISE visas inte automatiskt i anslutningsväljaren på Logic App Designer. Innan du kan använda dessa ISE-kopplingar måste du manuellt lägga till och distribuera dessa kopplingar till DIN ISE så att de visas i Logic App Designer.

1. Välj **Hanterade kopplingar**under **Inställningar**på ISE-menyn. Välj **Lägg till**i verktygsfältet .

   ![Visa hanterade kopplingar](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. Öppna listan **Sök koppling** i fönstret Lägg till en **ny hanterad koppling.** Välj den ISE-anslutning som du vill använda men har ännu inte distribuerats i ISE. Välj **Skapa**.

   ![Välj den ISE-anslutning som du vill distribuera i ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Endast ISE-kopplingar som är kvalificerade men ännu inte distribuerade till din ISE visas tillgängliga för dig att välja. Kopplingar som redan har distribuerats i ISE verkar inte tillgängliga för val.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Skapa anpassade kopplingar

Om du vill använda anpassade kopplingar i ISE skapar du de anpassade kopplingarna direkt i ISE.To use custom connectors in your ISE, create those custom connectors from directly inside your ISE.

1. Hitta och öppna din ISE, om den inte redan är öppen. Välj **Anpassade kopplingar** > **Lägg till**under **Inställningar**på ISE-menyn.

   ![Skapa anpassad anslutningsapp](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Ange namnet, Azure-prenumerationen och Azure-resursgruppen (ny eller befintlig) som ska användas för din anpassade anslutningsapp.

1. Välj samma ISE **Integration service environments** som logikapparna använder i listan **Plats** och välj **Skapa**till exempel:

   ![Välj integrationstjänstmiljö](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Välj den nya anpassade kopplingen och välj sedan **Redigera**, till exempel:

   ![Markera och redigera anpassad koppling](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Fortsätt genom att skapa kopplingen på vanligt sätt från en [OpenAPI-definition](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) eller [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Om du vill hantera anpassade kopplingar i ISE finns i [Hantera din integrationstjänstmiljö](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera integreringstjänstmiljöer](../logic-apps/ise-manage-integration-service-environment.md)
