---
title: Lägga till artefakter i integrerings tjänst miljöer (ISEs) i Azure Logic Apps
description: Lägg till logi Kap par, anpassade anslutningar och integrations konton i integrerings tjänst miljön (ISE) för att få åtkomst till Azure Virtual Networks (virtuella nätverk) och vara privat och isolerad från offentliga eller globala Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: df43b52514eebc3216dbec01cff0d8a3b14e7940
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517662"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Lägga till artefakter i integrerings tjänst miljön (ISE) i Azure Logic Apps

När du har skapat en [integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)lägger du till artefakter som Logi Kap par, integrations konton och anpassade anslutningar så att de kan komma åt resurserna i ditt virtuella Azure-nätverk.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Den ISE som du skapade för att köra dina Logic Apps. Om du inte har någon ISE måste du [först skapa en ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps-in-an-ise"></a>Skapa Logic Apps i en ISE

Följ dessa steg om du vill skapa Logi Kap par som körs i integrerings tjänst miljön (ISE):

1. Leta upp och öppna din ISE, om den inte redan är öppen. Från menyn ISE, under **Inställningar**, väljer du **Logic Apps** > **Lägg till**.

   ![Lägg till ny Logic app i ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   ELLER

   Från huvud menyn i Azure väljer du **skapa en** > app för resurs**integrerings** > **logik**.

1. Ange namnet, Azure-prenumerationen och Azure-resurs gruppen (ny eller befintlig) som ska användas för din Logic app.

1. I listan **plats** går du till avsnittet **integrerings tjänst miljöer** och väljer din ISE, till exempel:

   ![Välj integrerings tjänst miljö](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Om du vill använda dina Logi Kap par med ett integrations konto måste dessa Logic Apps och integrations kontot använda samma ISE.

1. Fortsätt [att skapa din Logic app på vanligt sätt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Information om skillnader i hur utlösare och åtgärder fungerar och hur de märks när du använder en ISE jämfört med den globala Logic Apps tjänsten finns i [isolerade kontra globala i ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)-översikten.

1. Information om hur du hanterar Logic Apps och API-anslutningar i din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts-in-an-ise"></a>Skapa integrations konton i en ISE

Utifrån den [ISE-SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) som valts vid skapandet, innehåller din ISE en viss integrerings konto användning utan extra kostnad. Logi Kap par som finns i en integrerings tjänst miljö (ISE) kan endast referera till integrations konton som finns i samma ISE. För att ett integrations konto ska fungera med logi Kap par i en ISE måste både integrations kontot och Logic Apps använda *samma miljö* som platsen. Mer information om integrations konton och ISEs finns i [integrations konton med](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment
)ISE.

Följ dessa steg om du vill skapa ett integrations konto som använder en ISE:

1. Leta upp och öppna din ISE, om den inte redan är öppen. Från menyn ISE, under **Inställningar**, väljer du **integrerings konton** > **Lägg till**.

   ![Lägg till nytt integrations konto i ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   ELLER

   Välj **skapa ett** > **integrations konto**för resurs**integration** > på huvud menyn i Azure.

1. Ange namn, Azure-prenumeration, Azure-resurs grupp (ny eller befintlig) och pris nivå som ska användas för ditt integrations konto.

1. I listan **plats** i avsnittet integrerings **tjänst miljöer** väljer du samma ISE som din Logi Kap par använder, till exempel:

   ![Välj integrerings tjänst miljö](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Länka din Logic app till ditt integrations konto på vanligt sätt](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Fortsätt genom att lägga till artefakter till ditt integrations konto, till exempel [handels partner](../logic-apps/logic-apps-enterprise-integration-partners.md) och [avtal](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Information om hur du hanterar integrations konton i din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors-in-an-ise"></a>Skapa anpassade anslutningar i en ISE

Om du vill använda anpassade anslutningar i ISE skapar du de anpassade anslutningarna direkt inuti din ISE.

1. Leta upp och öppna din ISE, om den inte redan är öppen. I menyn ISE väljer du **anpassade anslutningar** > **Lägg till**under **Inställningar**.

   ![Skapa anpassad anslutningsapp](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Ange namnet, Azure-prenumerationen och Azure-resurs gruppen (ny eller befintlig) som ska användas för din anpassade anslutning.

1. I listan **plats** i avsnittet integrerings **tjänst miljöer** väljer du samma ISE som din Logi Kap par använder och väljer **skapa**, till exempel:

   ![Välj integrerings tjänst miljö](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Välj din nya anpassade anslutning och välj sedan **Redigera**, till exempel:

   ![Välj och redigera anpassad anslutning](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Fortsätt genom att skapa anslutningen på vanligt sätt från en [openapi-definition](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) eller [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Information om hur du hanterar anpassade anslutningar i din ISE finns i [Hantera integrerings tjänst miljön](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera integrerings tjänst miljöer](../logic-apps/ise-manage-integration-service-environment.md)
