---
title: Hantera integrationstjänstmiljöer i Azure Logic Apps
description: Kontrollera nätverkshälsan och hantera logikappar, anslutningar, anpassade anslutningsappar och integrationskonton i integrationstjänstmiljön (ISE) för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: f48106be67763c093a183be01098cab74391752e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284205"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Hantera din integrationstjänstmiljö (ISE) i Azure Logic Apps

Den här artikeln visar hur du utför hanteringsuppgifter för [integrationstjänstmiljön (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)till exempel:

* Hantera resurser som logikappar, anslutningar, integrationskonton och anslutningsappar i ISE.
* Kontrollera ISE:s nätverkshälsa.
* Lägg till kapacitet, starta om ISE eller ta bort ISE: s anvisningar. Om du vill lägga till dessa artefakter i ISE finns [i Lägga till artefakter i integrationstjänstmiljön](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Visa din ISE

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. I portalens sökruta anger du "integrationstjänstmiljöer" och väljer sedan **Integrationstjänstmiljöer**.

   ![Hitta integrationstjänstmiljöer](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Välj din integrationstjänstmiljö i resultatlistan.

   ![Välj integrationstjänstmiljö](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Fortsätt till nästa avsnitt för att hitta logikappar, anslutningar, anslutningsappar eller integrationskonton i ISE.Continue to the next sections to find logic apps, connections, connectors, or integration accounts in your ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Kontrollera nätverkets hälsa

Välj **Nätverkshälsa**under **Inställningar**på ISE-menyn. I den här rutan visas hälsostatus för dina undernät och utgående beroenden för andra tjänster.

![Kontrollera nätverkets hälsa](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Hantera logikappar

Du kan visa och hantera logikappar som finns i DIN ISE.

1. Välj **Logikappar**under **Inställningar**på ISE-menyn.

   ![Visa logikappar](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Om du vill ta bort logikappar som du inte längre behöver i ISE markerar du dessa logikappar och väljer sedan **Ta bort**. Om du vill bekräfta att du vill ta bort väljer du **Ja**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Hantera API-anslutningar

Du kan visa och hantera de anslutningar som har skapats av logikapparna som körs i ISE.

1. Välj **API-anslutningar**under **Inställningar**på ISE-menyn.

   ![Visa API-anslutningar](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Om du vill ta bort anslutningar som du inte längre behöver i ISE markerar du dessa anslutningar och väljer sedan **Ta bort**. Om du vill bekräfta att du vill ta bort väljer du **Ja**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Hantera ISE-kopplingar

Du kan visa och hantera API-kopplingar som distribueras till din ISE.

1. Välj **Hanterade kopplingar**under **Inställningar**på ISE-menyn.

   ![Visa hanterade kopplingar](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Om du vill ta bort kopplingar som du inte vill ska vara tillgängliga i ISE markerar du dessa kopplingar och väljer sedan **Ta bort**. Om du vill bekräfta att du vill ta bort väljer du **Ja**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Hantera anpassade kopplingar

Du kan visa och hantera anpassade kopplingar som du har distribuerat till ISE.

1. Välj **Anpassade kopplingar**under **Inställningar**på ISE-menyn.

   ![Hitta anpassade anslutningsappar](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Om du vill ta bort anpassade kopplingar som du inte längre behöver i ISE markerar du dessa kopplingar och väljer sedan **Ta bort**. Om du vill bekräfta att du vill ta bort väljer du **Ja**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Hantera integrationskonton

1. Välj **Integrationskonton**under **Inställningar**på ISE-menyn.

   ![Hitta integrationskonton](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Om du vill ta bort integrationskonton från ISE när det inte längre behövs markerar du dessa integrationskonton och väljer sedan **Ta bort**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Lägg till ISE-kapacitet

Premium ISE-basenheten har fast kapacitet, så om du behöver mer dataflöde kan du lägga till fler skalenheter, antingen när du skapar eller efteråt. Utvecklar-SKU innehåller inte möjligheten att lägga till skalningsenheter.

1. Gå till din ISE i [Azure-portalen.](https://portal.azure.com)

1. Om du vill granska användnings- och prestandamått för DIN ISE väljer du **Översikt**på ISE-menyn .

   ![Visa användning för ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. Under **Inställningar**väljer du **Skala ut**. Välj bland följande alternativ i fönstret **Konfigurera:**

   * [**Manuell skala**](#manual-scale): Skala baserat på antalet bearbetningsenheter som du vill använda.
   * [**Anpassad automatisk skalning**](#custom-autoscale): Skala baserat på prestandamått genom att välja från olika villkor och ange tröskelvillkor för att uppfylla dessa kriterier.

   ![Välj den skalningstyp som du vill använda](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Manuell skala

1. När du har valt **Manuell skala**väljer du det antal skalningsenheter som du vill använda för **ytterligare kapacitet.**

   ![Välj den skalningstyp som du vill använda](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. När du är klar väljer du **Spara**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Anpassad automatisk skalning

1. När du har valt **Anpassad automatisk skalning**för **inställningsnamn**för automatisk skalning anger du ett namn för din inställning och väljer eventuellt den Azure-resursgrupp där inställningen hör hemma.

   ![Ange namn för automatisk skalningsinställning och välj resursgrupp](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. För **standardvillkoret** väljer du antingen **Skala baserat på ett mått** eller Skala till ett visst **instansantal**.

   * Om du väljer instansbaserad anger du numret för bearbetningsenheterna, vilket är ett värde från 0 till 10.

   * Om du väljer måttbaserad gör du så här:

     1. I avsnittet **Regler** väljer du **Lägg till en regel**.

     1. I **fönstret Skala regel** ställer du in dina villkor och åtgärder som ska vidtas när regeln utlöses.

     1. Ange följande värden för **instansgränser:**

        * **Minimum**: Det minsta antalet bearbetningsenheter som ska användas
        * **Maximum**: Det maximala antalet bearbetningsenheter som ska användas
        * **Standard:** Om några problem uppstår när resursmåtten läses och den aktuella kapaciteten ligger under standardkapaciteten skalas automatisk skalning ut till standardantalet bearbetningsenheter. Men om den aktuella kapaciteten överskrider standardkapaciteten skalas inte automatisk skalning in.

1. Om du vill lägga till ett annat villkor väljer du **Lägg till skalningsvillkor**.

1. När du är klar med inställningarna för automatisk skalning sparar du ändringarna.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Starta om ISE

Om du ändrar DNS-serverns eller DNS-serverinställningarna måste du starta om ISE:et så att ISE kan ta upp ändringarna. Om du startar om en Premium SKU ISE kan du inte göra driftstopp på grund av redundans och komponenter som startar om en i taget under återvinningen. En Utvecklare SKU ISE upplever dock driftstopp eftersom det inte finns någon redundans. Mer information finns i [ISE SKU: er](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. Gå till din ISE i [Azure-portalen.](https://portal.azure.com)

1. Välj **Översikt**på ISE-menyn . Starta **om**i verktygsfältet Översikt .

   ![Starta om integrationstjänstmiljön](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Ta bort ISE

Innan du tar bort en ISE som du inte längre behöver eller en Azure-resursgrupp som innehåller en ISE kontrollerar du att du inte har några principer eller lås i Azure-resursgruppen som innehåller dessa resurser eller i ditt virtuella Azure-nätverk eftersom dessa objekt kan blockera borttagning.

När du har tagit bort din ISE kan du behöva vänta upp till 9 timmar innan du försöker ta bort ditt virtuella Azure-nätverk eller undernät.

## <a name="next-steps"></a>Nästa steg

* [Lägga till resurser i integrationstjänstmiljöer](../logic-apps/add-artifacts-integration-service-environment-ise.md)