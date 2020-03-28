---
title: Självstudiekurs - Skapa och ändra en krets med ExpressRoute
description: I den här självstudien kan du lära dig hur du skapar, etablerar, verifierar, uppdaterar, tar bort och avetableras en ExpressRoute-krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74813125"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Självstudiekurs: Skapa och ändra en ExpressRoute-krets

> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video - Azure-portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln hjälper dig att skapa en ExpressRoute-krets med Azure-portalen och Azure Resource Manager-distributionsmodellen. Du kan också kontrollera status, uppdatera, ta bort eller avetablera en krets.

## <a name="before-you-begin"></a>Innan du börjar

* Granska [förutsättningarna](expressroute-prerequisites.md) och [arbetsflödena](expressroute-workflows.md) innan du börjar konfigurera.
* Kontrollera att du har åtkomst till [Azure-portalen](https://portal.azure.com).
* Kontrollera att du har behörighet att skapa nya nätverksresurser. Kontakta kontoadministratören om du inte har rätt behörighet.
* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="1-sign-in-to-the-azure-portal"></a>1. Logga in på Azure-portalen

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Skapa en ny ExpressRoute-krets

> [!IMPORTANT]
> Din ExpressRoute-krets faktureras från det att en servicenyckel utfärdas. Se till att du utför den här åtgärden när anslutningsleverantören är redo att etablera kretsen.

Du kan skapa en ExpressRoute-krets genom att välja alternativet för att skapa en ny resurs. 

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**. Välj **Networking** > **ExpressRoute**, som visas i följande bild:

   ![Skapa en ExpressRoute-krets](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. När du har klickat på **ExpressRoute**visas **sidan Skapa ExpressRoute-krets.** När du fyller i värdena på den här sidan kontrollerar du att du anger rätt SKU-nivå (Standard eller Premium) och faktureringsmodell för datamätning (Obegränsad eller Metered).

   ![Konfigurera SKU-nivån och datamätningen](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Nivå** avgör om en ExpressRoute-standard eller ett ExpressRoute-premiumtillägg är aktiverat. Du kan ange **Standard** för att få standard SKU eller **Premium** för premiumtillägget.
   * **Datamätning** avgör faktureringstypen. Du kan ange **Mätavmässat** för ett dataabonnemang med datapriser och **Obegränsad** för ett obegränsat dataabonnemang. Observera att du kan ändra **faktureringstypen** från Uppmätt till **Obegränsad**.

     > [!IMPORTANT]
     > Du kan inte ändra typen från **Obegränsad** till **Metered**.

   * **Peering-plats** är den fysiska plats där du peering med Microsoft.

     > [!IMPORTANT]
     > Peering-platsen anger den [fysiska plats](expressroute-locations.md) där du peering med Microsoft. Detta är **inte** länkat till egenskapen Plats, som refererar till geografin där Azure Network Resource Provider finns. Även om de inte är relaterade är det en god idé att välja en nätverksresursprovider geografiskt nära peering-platsen för kretsen.

### <a name="3-view-the-circuits-and-properties"></a>3. Visa kretsar och egenskaper

**Visa alla kretsar**

Du kan visa alla kretsar som du har skapat genom att välja **Alla resurser** på menyn till vänster.

![Visa kretsar](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visa egenskaperna**

Du kan visa egenskaperna för kretsen genom att välja den. På sidan **Översikt** för din krets visas servicenyckeln i fältet servicenyckel. Du måste kopiera servicenyckeln för din krets och skicka den till tjänsteleverantören för att slutföra etableringsprocessen. Kretsservicenyckeln är specifik för din krets.

![Visa egenskaper](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Skicka tjänstnyckeln till din anslutningsleverantör för etablering

På den här sidan ger **providerstatus** information om det aktuella läget för etablering på tjänsteleverantörens sida. **Kretsstatus** ger tillståndet på Microsoft-sidan. Mer information om kretsetableringstillstånd finns i artikeln [Arbetsflöden.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

Providerstatus: Inte etablerad<BR>
Kretsstatus: Aktiverad

![Initiera etableringsprocess](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Kretsen ändras till följande tillstånd när anslutningsleverantören är i färd med att aktivera det åt dig:

Providerstatus: Etablering<BR>
Kretsstatus: Aktiverad

För att du ska kunna använda en ExpressRoute-krets måste den vara i följande tillstånd:

Providerstatus: Etablerad<BR>
Kretsstatus: Aktiverad

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Kontrollera regelbundet kretsnyckelns status och tillstånd

Du kan visa egenskaperna för den krets som du är intresserad av genom att välja den. Kontrollera **providerns status** och se till att den har **flyttats** till Etablerad innan du fortsätter.

![Krets- och leverantörsstatus](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Skapa din routningskonfiguration

Steg-för-steg-instruktioner finns i konfigurationsartikeln för Routning av [ExpressRoute-kretsroutning](expressroute-howto-routing-portal-resource-manager.md) för att skapa och ändra krets peerings.

> [!IMPORTANT]
> Dessa instruktioner gäller endast för kretsar som skapas med tjänsteleverantörer som erbjuder anslutningstjänster för lager 2. Om du använder en tjänsteleverantör som erbjuder hanterade layer 3-tjänster (vanligtvis en IP VPN, till exempel MPLS), konfigurerar och hanterar anslutningsleverantören routning åt dig.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Länka ett virtuellt nätverk till en ExpressRoute-krets

Länka sedan ett virtuellt nätverk till expressroutekretsen. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) när du arbetar med resurshanterarens distributionsmodell.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Hämta status för en ExpressRoute-krets

Du kan visa status för en krets genom att välja den och visa sidan Översikt.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan ändra bandbredden, SKU, faktureringsmodellen och tillåta klassiska åtgärder på **sidan Konfiguration.** Information om begränsningar och begränsningar finns i Vanliga frågor och svar om [ExpressRoute](expressroute-faqs.md).

Du kan utföra följande uppgifter utan driftstopp:

* Aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets.
* Öka bandbredden på din ExpressRoute-krets, förutsatt att det finns kapacitet tillgänglig på porten.

  > [!IMPORTANT]
  > Det går inte att nedgradera bandbredden för en krets.

* Ändra mätplanen från *datapriser* till *obegränsad data*.

  > [!IMPORTANT]
  > Det går inte att ändra avläsningsplanen från obegränsad data till datapriser.

* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.
  > [!IMPORTANT]
  > Du kan behöva återskapa ExpressRoute-kretsen om det finns otillräcklig kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
  >
  > Även om du sömlöst kan uppgradera bandbredden kan du inte minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradering bandbredd kräver att du avetablerar ExpressRoute-kretsen och sedan återetablerar en ny ExpressRoute-krets.
  >
  > Om du inaktiverar Premium-tilläggsåtgärden kan det misslyckas om du använder resurser som är större än vad som är tillåtet för standardkretsen.

Om du vill ändra en ExpressRoute-krets klickar du på **Konfiguration**.

![Ändra krets](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets

Du kan ta bort expressroutekretsen genom att välja **borttagningsikonen.** Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden misslyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
* Om tillståndet för tillhandahållare av ExpressRoute-kretstjänstleverantören **etablerar** eller **etableras** måste du samarbeta med din tjänsteleverantör för att avetablera kretsen på deras sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänsteleverantören slutför avetablering av kretsen och meddelar oss.
* Om tjänsteleverantören har avetablerat kretsen (etableringstillståndet för tjänsteleverantören är inställt på **Inte etablerat)** kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

## <a name="next-steps"></a>Nästa steg

När du har skapat kretsen fortsätter du med följande steg:

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
