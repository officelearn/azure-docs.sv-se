---
title: Hantera integrerings tjänst miljöer i Azure Logic Apps
description: Kontrol lera nätverks hälsa och hantera Logi Kap par, anslutningar, anpassade anslutningar och integrations konton i integrerings tjänst miljön (ISE) för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1d91813e0f39207bcf7768de89600a6bdee0fc53
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358899"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Hantera integrerings tjänst miljön (ISE) i Azure Logic Apps

Följ stegen i det här avsnittet för att kontrol lera nätverks hälsan för [integrerings tjänst miljön (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) och hantera Logi Kap par, anslutningar, integrations konton och anslutningar som finns i din ISE. Information om hur du lägger till dessa artefakter i ISE finns i [lägga till artefakter i integrerings tjänst miljön](../logic-apps/add-artifacts-integration-service-environment-ise.md).

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

Du kan visa och hantera de Logic Apps som finns i din ISE.

1. I menyn ISE väljer du Logi Kap par under **Inställningar**.

   ![Visa Logic Apps](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Om du vill ta bort Logi Kap par som du inte längre behöver i din ISE väljer du dessa Logic Apps och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Hantera API-anslutningar

Du kan visa och hantera de anslutningar som har skapats av Logic Apps som körs i din ISE.

1. I menyn ISE väljer du **API-anslutningar**under **Inställningar**.

   ![Visa API-anslutningar](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Om du vill ta bort anslutningar som du inte längre behöver i din ISE väljer du dessa anslutningar och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Hantera ISE-anslutningar

Du kan visa och hantera API-kopplingar som distribueras till din ISE.

1. På menyn ISE väljer du **hanterade anslutningar**under **Inställningar**.

   ![Visa hanterade anslutningar](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Om du vill ta bort anslutningar som du inte vill ska vara tillgängliga i din ISE väljer du de kopplingarna och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Hantera anpassade anslutningar

Du kan visa och hantera de anpassade anslutnings program som du har distribuerat till din ISE.

1. På menyn ISE väljer du **anpassade anslutningar**under **Inställningar**.

   ![Hitta anpassade anslutningsappar](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Om du vill ta bort anpassade anslutningar som du inte längre behöver i din ISE väljer du de kopplingarna och väljer sedan **ta bort**. Bekräfta att du vill ta bort genom att välja **Ja**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Hantera integrationskonton

1. På menyn ISE väljer du **integrations konton**under **Inställningar**.

   ![Hitta integrations konton](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Om du vill ta bort integrations konton från din ISE när de inte längre behövs väljer du dessa integrations konton och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ansluter till virtuella Azure-nätverk från isolerade Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
