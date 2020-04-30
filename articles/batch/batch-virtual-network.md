---
title: Etablera en pool i ett virtuellt nätverk – Azure Batch | Microsoft Docs
description: Så här skapar du en batch-pool i ett virtuellt Azure-nätverk så att Compute-noder kan kommunicera säkert med andra virtuella datorer i nätverket, till exempel en fil server.
ms.topic: article
ms.date: 04/03/2020
ms.custom: seodec18
ms.openlocfilehash: 616118d5f75f9bfa6d97d89baac9d7ea9186cd5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82111903"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Skapa en Azure Batch pool i ett virtuellt nätverk

När du skapar en Azure Batch pool kan du etablera poolen i ett undernät för ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) (VNet) som du anger. Den här artikeln förklarar hur du konfigurerar en batch-pool i ett VNet.

## <a name="why-use-a-vnet"></a>Varför ska jag använda ett VNet?

En Azure Batch pool har inställningar för att tillåta att Compute-noder kommunicerar med varandra, till exempel för att köra aktiviteter med flera instanser. De här inställningarna kräver inte ett separat VNet. Noderna kan som standard inte kommunicera med virtuella datorer som inte ingår i batch-poolen, till exempel en licens Server eller en fil server. Om du vill tillåta att datornoder i pooler kommunicerar säkert med andra virtuella datorer eller med ett lokalt nätverk kan du etablera poolen i ett undernät för ett Azure VNet.

## <a name="prerequisites"></a>Krav

* **Autentisering**. Om du vill använda ett Azure VNet-nätverk måste Batch-klientens API använda Azure Active Directory-autentisering (AD). Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory).

* **Ett Azure VNet**. Se följande avsnitt för krav och konfiguration för VNet. För att förbereda ett VNet med ett eller flera undernät i förväg, kan du använda Azure Portal, Azure PowerShell, kommando rads gränssnittet för Azure (CLI) eller andra metoder.
  * Information om hur du skapar ett Azure Resource Manager-baserat VNet finns i [skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Ett Resource Manager-baserat VNet rekommenderas för nya distributioner och stöds endast på pooler i konfigurationen för den virtuella datorn.
  * Om du vill skapa ett klassiskt VNet, se [skapa ett virtuellt nätverk (klassiskt) med flera undernät](../virtual-network/create-virtual-network-classic.md). Ett klassiskt VNet stöds bara för pooler i Cloud Services-konfigurationen.

## <a name="vnet-requirements"></a>VNet-krav

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Skapa en pool med ett VNet i portalen

När du har skapat ditt VNet och tilldelat ett undernät, kan du skapa en batch-pool med det virtuella nätverket. Följ de här stegen för att skapa en pool från Azure Portal: 

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste finnas i samma prenumeration och region som den resurs grupp som innehåller det virtuella nätverk som du vill använda.
2. I fönstret **Inställningar** till vänster väljer du meny alternativet **pooler** .
3. I fönstret **pooler** väljer du kommandot **Lägg till** .
4. I fönstret **Lägg till pool** väljer du det alternativ som du vill använda från List rutan **Bildtyp** .
5. Välj rätt **utgivare/erbjudande/SKU** för din anpassade avbildning.
6. Ange de återstående nödvändiga inställningarna, inklusive **nodens storlek**, **dedikerade noder**och **noder med låg prioritet**samt önskade valfria inställningar.
7. I **Virtual Network**väljer du det virtuella nätverk och undernät som du vill använda.

   ![Lägg till poolen med virtuellt nätverk](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade vägar för Tvingad tunnel trafik

Du kan ha krav i din organisation för att omdirigera (tvinga) Internet-baserad trafik från undernät tillbaka till din lokala plats för inspektion och loggning. Du kanske har aktiverat Tvingad tunnel trafik för undernät i ditt VNet.

Du måste lägga till följande [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) för under nätet för att säkerställa att beräknings noder för Azure Batch pool fungerar i ett VNet som har Tvingad tunnel trafik aktiverat:

* Batch-tjänsten måste kommunicera med datornoder för att schemalägga aktiviteter. Om du vill aktivera den här kommunikationen lägger du till en användardefinierad väg för varje IP-adress som används av batch-tjänsten i den region där ditt batch-konto finns. Information om hur du hämtar listan över IP-adresser för batch-tjänsten finns i [tjänst Taggar lokalt](../virtual-network/service-tags-overview.md). IP-adresserna för batch-tjänsten kommer att `BatchNodeManagement` associeras med Service Tag-numret (eller den regionala variant som matchar ditt batch-kontoområdet).

* Se till att den utgående trafiken till Azure Storage (särskilt webb adresserna `<account>.table.core.windows.net`i `<account>.queue.core.windows.net`formuläret, `<account>.blob.core.windows.net`och) inte är blockerad via den lokala nätverks enheten.

När du lägger till en användardefinierad väg definierar du vägen för varje relaterat kommando-IP-adressprefix och anger **nästa hopp typ** till **Internet**. Se följande exempel:

![Användardefinierad väg](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> IP-adresser för batch-tjänsten kan ändras med tiden. För att förhindra ett avbrott på grund av en ändring av en IP-adress rekommenderar vi att du upprättar en periodisk process för att uppdatera IP-adresser för batch-tjänsten automatiskt och hålla dem uppdaterade i routningstabellen.

## <a name="next-steps"></a>Nästa steg

- En djupgående översikt över batch finns i [utveckla storskaliga parallella beräknings lösningar med batch](batch-api-basics.md).
- Mer information om hur du skapar en användardefinierad väg finns i [Create a User-Defined Route-Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).
