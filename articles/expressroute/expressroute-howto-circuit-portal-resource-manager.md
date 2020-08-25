---
title: Självstudie – Skapa och ändra en krets med ExpressRoute
description: I den här självstudien får du lära dig hur du skapar, etablerar, kontrollerar, uppdaterar, tar bort och avetablerar en ExpressRoute-krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "74813125"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Självstudie: skapa och ändra en ExpressRoute-krets

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-mall](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln hjälper dig att skapa en ExpressRoute-krets med hjälp av Azure Portal och Azure Resource Manager distributions modell. Du kan också kontrol lera status, uppdatera, ta bort eller avetablera en krets.

## <a name="before-you-begin"></a>Innan du börjar

* Granska [nödvändiga komponenter](expressroute-prerequisites.md) och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Se till att du har åtkomst till [Azure Portal](https://portal.azure.com).
* Se till att du har behörighet att skapa nya nätverks resurser. Kontakta konto administratören om du inte har rätt behörigheter.
* Du kan [Visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Skapa och etablera en ExpressRoute-krets

### <a name="1-sign-in-to-the-azure-portal"></a>1. Logga in på Azure Portal

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="2-create-a-new-expressroute-circuit"></a>2. skapa en ny ExpressRoute-krets

> [!IMPORTANT]
> Din ExpressRoute-krets debiteras från den tidpunkt då en tjänst nyckel utfärdas. Se till att du utför den här åtgärden när anslutnings leverantören är redo att etablera kretsen.

Du kan skapa en ExpressRoute-krets genom att välja alternativet för att skapa en ny resurs. 

1. I menyn i Azure-portalen eller på sidan **Start** väljer du **Skapa en resurs**. Välj **nätverks**  >  -**ExpressRoute**, som du ser i följande bild:

   ![Skapa en ExpressRoute-krets](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. När du har klickat på **ExpressRoute**visas sidan **skapa ExpressRoute-krets** . När du fyller i värdena på den här sidan, se till att du anger rätt SKU-nivå (standard eller Premium) och fakturerings modell för data mätning (obegränsad eller uppmätt).

   ![Konfigurera SKU-nivån och data mätning](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **Nivån** avgör om en ExpressRoute-standard eller ett ExpressRoute Premium-tillägg är aktiverat. Du kan ange **standard** för att hämta standard-SKU eller **Premium** för Premium-tillägget.
   * **Data mätningen** fastställer fakturerings typen. Du kan ange **Mät** värde för en dataplan med datapriser och **obegränsade** för ett obegränsat data abonnemang. Observera att du kan ändra fakturerings typen från **avgiftsbelagd** till **obegränsad**.

     > [!IMPORTANT]
     > Du kan inte ändra typen från **obegränsad** till **mätning**.

   * **Peering-platsen** är den fysiska plats där du peer-koppla med Microsoft.

     > [!IMPORTANT]
     > Peering-platsen anger den [fysiska plats](expressroute-locations.md) där du peer-koppla med Microsoft. Detta är **inte** länkat till "location"-egenskap som refererar till den geografi där Azure Network Resource-providern finns. Även om de inte är relaterade är det en bra idé att välja en nätverks resurs-Provider geografiskt nära peering-platsen för kretsen.

### <a name="3-view-the-circuits-and-properties"></a>3. Visa kretsar och egenskaper

**Visa alla kretsar**

Du kan visa alla kretsar som du har skapat genom att välja **alla resurser** på den vänstra menyn.

![Visa kretsar](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visa egenskaperna**

Du kan visa egenskaperna för kretsen genom att markera den. På **översikts** sidan för din krets visas tjänst nyckeln i fältet tjänst nyckel. Du måste kopiera tjänst nyckeln för din krets och skicka den till tjänst leverantören för att slutföra etablerings processen. Krets tjänst nyckeln är speciell för din krets.

![Visa egenskaper](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. skicka tjänst nyckeln till din anslutnings leverantör för etablering

På den här sidan tillhandahåller **leverantörs status** information om det aktuella tillståndet för etablering på tjänstens leverantörs sida. **Krets status** tillhandahåller tillståndet på Microsoft-sidan. Mer information om krets etablerings tillstånd finns i artikeln om [arbets flöden](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

Leverantörs status: inte etablerad<BR>
Krets status: aktive rad

![Initiera etablerings processen](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

-Kretsen ändras till följande tillstånd när anslutnings leverantören håller på att aktivera den åt dig:

Leverantörs status: etablering<BR>
Krets status: aktive rad

För att du ska kunna använda en ExpressRoute-krets måste den vara i följande tillstånd:

Leverantörs status: etablerad<BR>
Krets status: aktive rad

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. kontrol lera regelbundet status och tillståndet för krets nyckeln

Du kan visa egenskaperna för den krets som du är intresse rad av genom att markera den. Kontrol lera **providerns status** och se till att den har flyttats till **etablerad** innan du fortsätter.

![Status för krets och Provider](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. skapa konfigurationen för routning

Stegvisa instruktioner finns i [ExpressRoute-kretsens konfigurations](expressroute-howto-routing-portal-resource-manager.md) artikel för att skapa och ändra krets-peering.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med tjänst leverantörer som erbjuder Layer 2-anslutningstjänster. Om du använder en tjänst leverantör som erbjuder hanterade Layer 3-tjänster (vanligt vis ett IP-VPN, som MPLS) konfigurerar och hanterar din anslutnings leverantör routning åt dig.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. länka ett virtuellt nätverk till en ExpressRoute-krets

Länka sedan ett virtuellt nätverk till din ExpressRoute-krets. Använd artikeln [Länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) när du arbetar med distributions modellen för Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>Hämta status för en ExpressRoute-krets

Du kan visa statusen för en krets genom att markera den och Visa översikts sidan.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Ändra en ExpressRoute-krets

Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan ändra bandbredd, SKU, fakturerings modell och tillåta klassisk drift på **konfigurations** sidan. Information om begränsningar och begränsningar finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera ett ExpressRoute Premium-tillägg för din ExpressRoute-krets.
* Öka bandbredden för din ExpressRoute-krets, förutsatt att kapaciteten är tillgänglig på porten.

  > [!IMPORTANT]
  > Nedgradering av en kretss bandbredd stöds inte.

* Ändra Mät planen från *datapriser* till *obegränsade data*.

  > [!IMPORTANT]
  > Det finns inte stöd för att ändra Mät planen från obegränsade data till datapriser.

* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.
  > [!IMPORTANT]
  > Du kan behöva återskapa ExpressRoute-kretsen om det inte finns tillräckligt med kapacitet på den befintliga porten. Du kan inte uppgradera kretsen om det inte finns någon ytterligare kapacitet tillgänglig på den platsen.
  >
  > Även om du sömlöst kan uppgradera bandbredden kan du inte minska bandbredden för en ExpressRoute-krets utan avbrott. Med degradering av bandbredd måste du avetablera ExpressRoute-kretsen och sedan etablera om en ny ExpressRoute-krets.
  >
  > Det går inte att inaktivera Premium-tilläggsen om du använder resurser som är större än vad som tillåts för standard kretsen.

Om du vill ändra en ExpressRoute-krets klickar du på **konfiguration**.

![Ändra krets](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets

Du kan ta bort ExpressRoute-kretsen genom att välja ikonen **ta bort** . Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden Miss lyckas kontrollerar du om några virtuella nätverk är länkade till kretsen.
* Om etablerings statusen för ExpressRoute-kretsen **etableras** eller **etableras** måste du arbeta med tjänst leverantören för att avetablera kretsen på sin sida. Vi fortsätter att reservera resurser och fakturera dig tills tjänste leverantören har slutfört avetableringen av kretsen och meddelar oss oss.
* Om tjänst leverantören har avetablerat kretsen (etablerings statusen för tjänst leverantören är inställd på **inte etablerad**) kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

## <a name="next-steps"></a>Nästa steg

När du har skapat din krets fortsätter du med följande steg:

* [Skapa och ändra routning för din ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ditt virtuella nätverk till din ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
