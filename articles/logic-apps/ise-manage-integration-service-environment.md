---
title: Hantera integrerings tjänst miljöer i Azure Logic Apps
description: Kontrol lera nätverks hälsa och hantera Logi Kap par, anslutningar, anpassade anslutningar och integrations konton i integrerings tjänst miljön (ISE) för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517467"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Hantera integrerings tjänst miljön (ISE) i Azure Logic Apps

Följ stegen i det här avsnittet om du vill kontrol lera nätverks hälsan för [integrerings tjänst miljön (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) och hantera Logi Kap par, anslutningar, integrations konton och anpassade anslutningar som finns i din ISE.

## <a name="view-your-ise"></a>Visa din ISE

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I portalens sökruta anger du "integrerings tjänst miljöer" och väljer sedan **integrerings tjänst miljöer**.

   ![Hitta integrerings tjänst miljöer](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. I resultat listan väljer du integrerings tjänst miljön.

   ![Välj integrerings tjänst miljö](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Fortsätt till nästa avsnitt för att hitta Logi Kap par, anslutningar, kopplingar eller integrations konton i din ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Kontrol lera nätverks hälsa

På menyn ISE väljer du **nätverks hälsa**under **Inställningar**. I det här fönstret visas hälso status för dina undernät och utgående beroenden för andra tjänster.

![Kontrol lera nätverks hälsa](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Hantera dina Logic Apps

1. I menyn ISE väljer du Logi Kap par under **Inställningar**.

   ![Hitta Logic Apps](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Om du vill ta bort Logi Kap par från din ISE när de inte längre behövs, väljer du dessa Logic Apps och väljer sedan **ta bort**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Hantera API-anslutningar

1. Om du vill visa de API-anslutningar som har skapats av logi Kap par som körs i din ISE går du till menyn ISE och väljer **API-anslutningar**under **Inställningar**.

   ![Hitta API-anslutningar](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Om du vill ta bort anslutningar från din ISE när de inte längre behövs väljer du dessa anslutningar och väljer sedan **ta bort**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Hantera anpassade anslutningar

1. Om du vill visa anpassade anslutningar som har skapats i din ISE väljer du **anpassade anslutningar**under **Inställningar**på menyn ISE.

   ![Hitta anpassade anslutningsappar](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Om du vill ta bort anpassade anslutningar från din ISE när de inte längre behövs väljer du dessa kopplingar och väljer sedan **ta bort**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Hantera integrationskonton

1. På menyn ISE väljer du **integrations konton**under **Inställningar**.

   ![Hitta integrations konton](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Om du vill ta bort integrations konton från din ISE när de inte längre behövs väljer du dessa integrations konton och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluter till virtuella Azure-nätverk från isolerade Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
