---
title: "Skapa och ändra en ExpressRoute-krets: Azure-portalen | Microsoft Docs"
description: "Den här artikeln beskriver hur du skapar, etablera, verifiera, uppdatera, ta bort och ta bort etableringen av en ExpressRoute-krets."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: be97f441d6eb2e97d85d598b0ed66718aec97b14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Skapa och ändra en ExpressRoute-krets
> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portalen](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-circuit-classic.md)
>

Den här artikeln beskriver hur du skapar en Azure ExpressRoute-krets med hjälp av Azure-portalen och Azure Resource Manager-distributionsmodellen. Följande steg visar även hur du kontrollerar statusen för kretsen, uppdatera eller ta bort och ta bort etableringen av den.


## <a name="before-you-begin"></a>Innan du börjar
* Granska de [krav](expressroute-prerequisites.md) och [arbetsflöden](expressroute-workflows.md) innan du börjar konfigurera.
* Kontrollera att du har åtkomst till den [Azure-portalen](https://portal.azure.com).
* Se till att du har behörighet att skapa nya nätverksresurser. Kontakta din kontoadministratör om du inte har rätt behörigheter.
* Du kan [visa en video](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) innan du påbörjar för att bättre förstå steg.

## <a name="create"></a>Skapa och etablera en ExpressRoute-krets
### <a name="1-sign-in-to-the-azure-portal"></a>1. Logga in på Azure Portal
Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Skapa en ny ExpressRoute-krets
> [!IMPORTANT]
> ExpressRoute-krets faktureras från den tidpunkt då en nyckel har utfärdats. Se till att utföra den här åtgärden när anslutningen providern är redo att etablera kretsen.
> 
> 

1. Du kan skapa en ExpressRoute-krets genom att välja alternativet för att skapa en ny resurs. Klicka på **skapar du en resurs** > **nätverk** > **ExpressRoute**, enligt följande bild:

  ![Skapa en ExpressRoute-krets](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. När du klickar på **ExpressRoute**, ser du den **skapa ExpressRoute-krets** sidan. När du fyller i värden på den här sidan kan du kontrollera att du anger rätt SKU-nivån (Standard eller Premium) och data avläsning faktureringsmodell som tillämpas (obegränsat eller Metered).

  ![Konfigurera SKU-nivå och datamätning](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Nivån** bestämmer om en ExpressRoute-standard eller premium-tillägg ett ExpressRoute är aktiverat. Du kan ange **Standard** att hämta standard SKU: N eller **Premium** för premium-tillägg.
  * **Datamätning** avgör vilken faktureringsinformation. Du kan ange **Metered** för en plan för förbrukade data och **obegränsad** för en obegränsad plan. Observera att du kan ändra fakturering typen från **Metered** till **obegränsad**, men du kan inte ändra typen från **obegränsad** till **Metered**.
  * **Peeringplatsen** är den fysiska platsen där du peering med Microsoft.

    > [!IMPORTANT]
    > Peering-platsen anger den [fysisk plats](expressroute-locations.md) där du peering med Microsoft. Detta är **inte** kopplad till ”plats”-egenskap som refererar till geografi där Azure Nätverksresursprovidern finns. När de inte är relaterade är det en bra idé att välja en Nätverksresursprovidern geografiskt nära Peering-platsen för kretsen.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. Visa kretsar och egenskaper
**Visa alla kretsar**

Du kan visa alla kretsar som du skapade genom att välja **alla resurser** på den vänstra menyn.

![Visa kretsar](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visa egenskaper**

Du kan visa egenskaperna för kretsen genom att välja den. På den **översikt** sidan för kretsen, service-nyckeln visas i fältet tjänst. Du måste kopiera nyckeln för tjänsten för kretsen och skickar den till leverantören för att slutföra etableringen. Kretstjänstnyckeln är specifika för kretsen.

![Visa egenskaper](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Skicka tjänstnyckeln till anslutningsleverantören för etablering
På den här sidan **Providerstatus** innehåller information om det aktuella tillståndet för etablering på sida-leverantör. **Kretsen status** ger tillståndet på Microsoft-sida. Mer information om krets etablering tillstånd finns det [arbetsflöden](expressroute-workflows.md#expressroute-circuit-provisioning-states) artikel.

När du skapar en ny ExpressRoute-krets är kretsen i följande tillstånd:

Providerstatus: inte etablerats<BR>
Kretsen status: aktiverat

![Initiera etableringsprocessen](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Kretsen ändras till följande tillstånd när anslutningen providern håller på att du:

Providerstatus: etablering<BR>
Kretsen status: aktiverat

Du kan använda en ExpressRoute-krets, måste den vara i följande tillstånd:

Providerstatus: etablerats<BR>
Kretsen status: aktiverat

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Regelbundet kontrollera status och tillståndet för nyckeln krets
Du kan visa egenskaperna för den krets du är intresserad av genom att välja den. Kontrollera den **Providerstatus** och se till att den har flyttats till **etablerad** innan du fortsätter.

![Kretsen och providern status](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Skapa din routningskonfiguration
Stegvisa anvisningar finns i den [ExpressRoute-krets routningskonfiguration](expressroute-howto-routing-portal-resource-manager.md) artikeln om du vill skapa och ändra krets peerkopplingar.

> [!IMPORTANT]
> Dessa anvisningar gäller endast för kretsar som skapats med leverantörer som erbjuder layer 2 tjänster. Om du använder en tjänstprovider som erbjuder Hanterade layer 3-tjänster (vanligtvis en IP VPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning för dig.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Länka ett virtuellt nätverk till en ExpressRoute-krets
Därefter länka ett virtuellt nätverk till ExpressRoute-kretsen. Använd den [länka virtuella nätverk för ExpressRoute-kretsar](expressroute-howto-linkvnet-arm.md) artikeln när du arbetar med Resource Manager-distributionsmodellen.

## <a name="status"></a>Hämta status för en ExpressRoute-krets
Du kan visa statusen för en krets genom att markera det och visa sidan Översikt. 

## <a name="modify"></a>Ändra en ExpressRoute-krets
Du kan ändra vissa egenskaper för en ExpressRoute-krets utan att påverka anslutningen. Du kan ändra bandbredd, SKU och fakturering modellen och tillåter klassiska åtgärder på den **Configuration** sidan. Information om gränser och begränsningar finns i [ExpressRoute vanliga frågor och svar](expressroute-faqs.md). 

Du kan utföra följande uppgifter utan avbrott:

* Aktivera eller inaktivera tillägget ExpressRoute Premium för ExpressRoute-kretsen.
* Öka bandbredden för ExpressRoute-krets, under förutsättning att det finns tillgänglig kapacitet på porten. Nedgradera bandbredden för en krets stöds inte. 
* Ändra avläsning planen från *förbrukade Data* till *obegränsade Data*. Om du ändrar avläsning planen från obegränsad till förbrukade Data stöds inte.
* Du kan aktivera och inaktivera *Tillåt klassiska åtgärder*.

> [!IMPORTANT]
> Du kan behöva återskapa ExpressRoute-kretsen om det finns för lite kapacitet på befintliga porten. Du kan inte uppgradera kretsen om det finns inga ytterligare kapacitet på den platsen.
>
> Även om sömlöst kan du uppgradera bandbredden, kan du minska bandbredden för en ExpressRoute-krets utan avbrott. Nedgradera bandbredd måste du ta bort etableringen av ExpressRoute-kretsen och etablera en ny ExpressRoute-krets.
> 
> Inaktivera åtgärden Premium-tillägg kan misslyckas om du använder resurser som är större än vad som tillåts för standard-kretsen.
> 
> 

Om du vill ändra en ExpressRoute-krets, klickar du på **Configuration**.

![Ändra krets](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>Borttagning och tar bort en ExpressRoute-krets
Du kan ta bort ExpressRoute-krets genom att välja den **ta bort** ikon. Observera följande information:

* Du måste ta bort länken alla virtuella nätverk från ExpressRoute-kretsen. Om den här åtgärden misslyckas, kan du kontrollera om något virtuellt nätverk är kopplade till kretsen.
* Om leverantören för ExpressRoute-kretsen Etableringsläge är **etablering** eller **etablerad** du måste arbeta med tjänstleverantören för att ta bort etableringen krets på sidan. Vi kan fortsätta att reservera resurser och debitera dig tills tjänstleverantör har slutförts avetablering kretsen och meddela oss.
* Om tjänstleverantör har avetableras kretsen (Etableringsstatus tjänstleverantör har angetts till **inte etablerats**), kan du ta bort kretsen. Detta stoppar faktureringen för kretsen.

## <a name="next-steps"></a>Nästa steg
När du har skapat kretsen fortsätter du med följande steg:

* [Skapa och ändra routning för ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md)
* [Länka ditt virtuella nätverk till ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
