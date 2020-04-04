---
title: Etablera en pool i ett virtuellt nätverk - Azure Batch | Microsoft-dokument
description: Så här skapar du en batchpool i ett virtuellt Azure-nätverk så att beräkningsnoder kan kommunicera säkert med andra virtuella datorer i nätverket, till exempel en filserver.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 04/03/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a653b645fb8713698e8baf283b3ab6226841dfcd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657476"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Skapa en Azure Batch-pool i ett virtuellt nätverk

När du skapar en Azure Batch-pool kan du etablera poolen i ett undernät till ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) (VNet) som du anger. I den här artikeln beskrivs hur du konfigurerar en batchpool i ett virtuella nätverk.

## <a name="why-use-a-vnet"></a>Varför använda ett virtuella nätverk?

En Azure Batch-pool har inställningar för att tillåta beräkningsnoder att kommunicera med varandra , till exempel för att köra uppgifter med flera instanser. Dessa inställningar kräver inte ett separat virtuella nätverk. Som standard kan noderna dock inte kommunicera med virtuella datorer som inte ingår i batchpoolen, till exempel en licensserver eller en filserver. Om du vill att poolberäkningsnoder ska kunna kommunicera säkert med andra virtuella datorer eller med ett lokalt nätverk kan du etablera poolen i ett undernät till ett Azure-virtuellt nätverk.

## <a name="prerequisites"></a>Krav

* **Autentisering**. Om du vill använda ett Azure VNet-nätverk måste Batch-klientens API använda Azure Active Directory-autentisering (AD). Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory).

* **Ett Azure-nätverk**. Se följande avsnitt för VNet-krav och konfiguration. Om du vill förbereda ett virtuella nätverk med ett eller flera undernät i förväg kan du använda Azure-portalen, Azure PowerShell, Azure Command-Line Interface (CLI) eller andra metoder.
  * Information om hur du skapar ett Azure Resource Manager-baserat virtuellt nätverk finns i [Skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Ett Resource Manager-baserat virtuellt nätverk rekommenderas för nya distributioner och stöds endast på pooler i konfigurationen för virtuella datorer.
  * Om du vill skapa ett klassiskt virtuellt nätverk finns i [Skapa ett virtuellt nätverk (klassiskt) med flera undernät](../virtual-network/create-virtual-network-classic.md). Ett klassiskt virtuella nätverk stöds endast på pooler i Cloud Services-konfigurationen.

## <a name="vnet-requirements"></a>VNet-krav

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Skapa en pool med ett virtuella nätverk i portalen

När du har skapat ditt virtuella nätverk och tilldelat ett undernät till det kan du skapa en batchpool med det virtuella nätverket. Så här skapar du en pool från Azure-portalen: 

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste finnas i samma prenumeration och region som resursgruppen som innehåller det virtuella nätverk som du tänker använda.
2. Välj menyalternativet **Pooler** i fönstret **Inställningar** till vänster.
3. Välj kommandot **Lägg till** i fönstret **Pooler.**
4. I fönstret **Lägg till pool** väljer du det alternativ som du tänker använda i listrutan **Bildtyp.**
5. Välj rätt **Utgivare/Erbjudande/Sku** för din anpassade bild.
6. Ange de återstående nödvändiga inställningarna, inklusive **nodstorleken,** **måldedicerade noder**och **noder med låg prioritet**, samt eventuella önskade valfria inställningar.
7. I **Virtuellt nätverk**väljer du det virtuella nätverk och det undernät som du vill använda.

   ![Lägga till pool med virtuellt nätverk](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade rutter för påtvingad tunnelning

Du kan ha krav i organisationen på att omdirigera (tvinga) Internet-bunden trafik från undernätet tillbaka till din lokala plats för inspektion och loggning. Du kan ha aktiverat tvångstunneler för undernäten i ditt virtuella nätverk.

Om du vill vara säkra på att dina Azure Batch-poolberäkningsnoder fungerar i ett virtuella nätverk som har aktiverat tunnelning måste du lägga till följande [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) för det undernätet:

* Batch-tjänsten måste kommunicera med poolberäkningsnoder för schemaläggning av aktiviteter. Om du vill aktivera den här kommunikationen lägger du till en användardefinierad väg för varje IP-adress som används av batch-tjänsten i den region där ditt batchkonto finns. Mer information om hur du hämtar listan över [IP-adresser för batch-tjänsten](../virtual-network/service-tags-overview.md)finns i Service-taggar lokalt . IP-adresserna för batchtjänsten `BatchNodeManagement` associeras med servicetaggen (eller den regionala varianten som matchar din batchkontoregion).

* Kontrollera att utgående trafik till Azure Storage (särskilt `<account>.table.core.windows.net` `<account>.queue.core.windows.net`webbadresser `<account>.blob.core.windows.net`i formuläret , och ) inte blockeras via din lokala nätverksinstallation.

När du lägger till en användardefinierad väg definierar du vägen för varje relaterad batch-IP-adressprefix och anger **Nästa hopptyp** på **Internet**. Se följande exempel:

![Användardefinierad väg](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Batch-tjänst IP-adresser kan ändras med tiden. För att förhindra ett avbrott på grund av en IP-adressändring föreslår vi att du upprättar en periodisk process för att uppdatera IP-adresser för batchtjänster automatiskt och hålla dem uppdaterade i din vägtabell.

## <a name="next-steps"></a>Nästa steg

- En djupgående översikt över Batch finns i [Utveckla storskaliga parallella beräkningslösningar med Batch](batch-api-basics.md).
- Mer information om hur du skapar en användardefinierad väg finns i [Skapa en användardefinierad väg - Azure-portal](../virtual-network/tutorial-create-route-table-portal.md).
