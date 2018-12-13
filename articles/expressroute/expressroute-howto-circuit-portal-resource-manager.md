---
title: 'Skapa och ändra en ExpressRoute-krets - portal: Azure | Microsoft Docs'
description: Skapa, etablera, verifiera, uppdatera, ta bort och Avetablerar en ExpressRoute-krets.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc;ganesr
ms.custom: seodec18
ms.openlocfilehash: 974421662a33cd9167d3c39b31d8da20db9f505f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091535"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Skapa och ändra en ExpressRoute-krets
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln hjälper dig att skapa en ExpressRoute-krets med hjälp av Azure portal och distributionsmodellen Azure Resource Manager. Du kan också kontrollera status, uppdatera, ta bort eller avetablera en krets.


## <a name="before-you-begin"></a>Innan du börjar
* Granska den [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du påbörjar konfigurationen.
* Kontrollera att du har åtkomst till den [Azure-portalen](https://portal.azure.com).
* Kontrollera att du har behörighet att skapa nya nätverksresurser. Kontakta din kontoadministratör om du inte har rätt behörigheter.
* Du kan [visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du påbörjar för att bättre förstå stegen.

## <a name="create"></a>Skapa och etablera en ExpressRoute-krets
### <a name="1-sign-in-to-the-azure-portal"></a>1. Logga in på Azure Portal
Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Skapa en ny ExpressRoute-krets
> [!IMPORTANT]
> ExpressRoute-kretsen debiteras från den tidpunkt då en Tjänstnyckel utfärdas. Se till att du utför den här åtgärden när anslutningsleverantören är redo att lägga till kretsen.
> 
> 

1. Du kan skapa en ExpressRoute-krets genom att välja alternativet för att skapa en ny resurs. Klicka på **skapa en resurs** > **nätverk** > **ExpressRoute**, enligt följande bild:

  ![Skapa en ExpressRoute-krets](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. När du klickar på **ExpressRoute**, ser du den **skapa ExpressRoute-krets** sidan. När du fyller i värden på den här sidan kan du kontrollera att du anger rätt SKU-nivån (Standard eller Premium) och data för Avläsning av faktureringsmodellen (obegränsat eller förbrukade).

  ![Konfigurera SKU-nivån och datamätning](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Nivån** bestämmer om en ExpressRoute-standard eller en ExpressRoute-premiumtillägget är aktiverat. Du kan ange **Standard** att hämta standard-SKU eller **Premium** premium-tillägg.
  * **Datamätning** avgör vilken fakturering. Du kan ange **förbrukade** för ett abonnemang med förbrukade data och **obegränsad** för en obegränsad dataplan. Observera att du kan ändra typen fakturering från **förbrukade** till **obegränsad**, men du kan inte ändra typen från **obegränsad** till **förbrukade**.
  * **Peeringplatsen** är den fysiska platsen där du peer med Microsoft.

    > [!IMPORTANT]
    > Peering-platsen anger den [fysisk plats](expressroute-locations.md) där du peer med Microsoft. Det här är **inte** länkad till ”plats”-egenskap som refererar till geografiska plats där Azure-nätverkets Resursprovider finns. Även om de inte är relaterade är en bra idé att välja en Provider för nätverksresurser geografiskt nära platsen Peering för kretsen.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. Visa kretsar och egenskaper
**Visa alla kretsar**

Du kan visa alla kretsar som du skapade genom att välja **alla resurser** på menyn till vänster.

![Visa kretsar](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visa egenskaper**

Du kan visa egenskaperna för kretsen genom att välja den. På den **översikt** sidan för din krets med tjänstnyckeln visas i fältet tjänst. Du måste kopiera tjänstnyckeln för din krets och skickar den till tjänstleverantör för att slutföra etableringen. Kretstjänstnyckeln är specifik för din krets.

![Visa egenskaper](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Skicka tjänstnyckeln till din anslutningsleverantör för etablering
På den här sidan **Providerstatus** innehåller information om det aktuella tillståndet för etablering på tjänstleverantör sida. **Status-krets** ger tillståndet på Microsoft-sida. Mer information om krets Etableringsstatus finns i den [arbetsflöden](expressroute-workflows.md#expressroute-circuit-provisioning-states) artikeln.

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

Providerstatus: inte etablerats<BR>
Circuit status: aktiverat

![Starta etableringsprocessen](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Kretsen ändras till följande tillstånd när anslutningsleverantören håller på att aktivera den för du:

Providerstatus: etablering<BR>
Circuit status: aktiverat

Du kan använda en ExpressRoute-krets, måste den vara i följande tillstånd:

Providerstatus: etablerats<BR>
Circuit status: aktiverat

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Regelbundet kontrollera status och tillståndet för nyckeln krets
Du kan visa egenskaperna för kretsen som du är intresserad av genom att välja den. Kontrollera den **Providerstatus** och se till att den har flyttats till **etablerad** innan du fortsätter.

![Kretsen och provider-status](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Skapa din routningskonfiguration
Stegvisa instruktioner finns i den [ExpressRoute-krets routningskonfiguration](expressroute-howto-routing-portal-resource-manager.md) artikeln om du vill skapa och ändra krets peerings.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapas med tjänstleverantörer som erbjuder tjänster för layer-2-anslutning. Om du använder en leverantör av tjänster som erbjuder Hanterade layer 3-tjänster (vanligtvis en IP VPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning åt dig.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Länka ett virtuellt nätverk till en ExpressRoute-krets
Därefter länka ett virtuellt nätverk till ExpressRoute-kretsen. Använd den [länka virtuella nätverk till ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) artikel när du arbetar med Resource Manager-distributionsmodellen.

## <a name="status"></a>Hämta status för en ExpressRoute-krets
Du kan visa statusen för en krets genom att markera det och visa sidan Översikt. 

## <a name="modify"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan ändra faktureringsmodellen för bandbredd, SKU, och Tillåt klassiska åtgärder på den **Configuration** sidan. Information om gränser och begränsningar finns i den [ExpressRoute vanliga frågor och svar](expressroute-faqs.md). 

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera en ExpressRoute Premium-tillägget för ExpressRoute-krets.
* Öka bandbredden för ExpressRoute-kretsen, förutsatt att det finns tillgänglig kapacitet på porten. Nedgradera bandbredden för en krets stöds inte. 
* Ändra Avläsning av planen från *förbrukade Data* till *obegränsade Data*. Prisplanen ändras Avläsning av programvara från obegränsade Data för förbrukade Data stöds inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-krets om det finns lite kapacitet på befintliga porten. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Även om du sömlöst uppgradera bandbredd, kan du minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera om en ny ExpressRoute-krets.
> 
> Inaktivera Premium-tillägg-åtgärden kan misslyckas om du använder resurser som är större än vad som tillåts för standard-krets.
> 
> 

Om du vill ändra en ExpressRoute-krets, klickar du på **Configuration**.

![Ändra krets](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>Avetablera och ta bort en ExpressRoute-krets
Du kan ta bort ExpressRoute-kretsen genom att välja den **ta bort** ikon. Notera följande information:

* Du måste ta bort länken till alla virtuella nätverk från ExpressRoute-kretsen. Om åtgärden misslyckas, kan du kontrollera om alla virtuella nätverken är länkade till kretsen.
* Om leverantören för ExpressRoute-krets Etableringsstatus är **etablering** eller **etablerad** måste du samarbeta med din tjänstleverantör för att avetablera kretsen på sidan. Vi fortsätter att reservera resurser och debitera dig tills tjänstleverantören har slutförts avetablera kretsen och meddelar oss.
* Om tjänsteleverantören har tagit bort etableringen kretsen (tjänstleverantören Etableringsstatus är inställd på **inte etablerats**), kan du ta bort kretsen. Därmed avbryts faktureringen för kretsen.

## <a name="next-steps"></a>Nästa steg
När du har skapat din krets fortsätter du med följande steg:

* [Skapa och ändra routning för ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
