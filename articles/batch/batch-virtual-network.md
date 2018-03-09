---
title: "Etablera Azure Batch-pool i ett virtuellt nätverk | Microsoft Docs"
description: "Du kan skapa en Batch-pool i ett virtuellt nätverk så att beräkningsnoderna kan kommunicera på ett säkert sätt med andra virtuella datorer i nätverket, till exempel en filserver."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/05/2018
ms.author: danlep
ms.openlocfilehash: 9748ae5cc58c64412ab6e51e6cd502abc3505c51
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Skapa en Azure Batch-pool i ett virtuellt nätverk


När du skapar en Azure Batch-pool kan du etablera poolen i ett undernät för ett [virtuella Azure-nätverket](../virtual-network/virtual-networks-overview.md) (VNet) som du anger. Den här artikeln förklarar hur du ställer in en Batch-pool i ett VNet. 



## <a name="why-use-a-vnet"></a>Varför ska jag använda ett virtuellt nätverk?


Azure Batch-pool har inställningar som tillåter att beräkningsnoderna kan kommunicera med varandra – till exempel, för att köra flera instanser uppgifter. Inställningarna kräver inte ett separat virtuellt nätverk. Som standard kan dock noder kommunicerar med virtuella datorer som inte är del av Batch-pool, till exempel en server eller en filserver. Du kan etablera poolen i ett undernät för ett virtuellt Azure-nätverk för att tillåta poolen compute-noder att kommunicera säkert med andra virtuella datorer eller med ett lokalt nätverk. 



## <a name="prerequisites"></a>Förutsättningar

* **Autentisering**. Om du vill använda ett Azure VNet-nätverk måste Batch-klientens API använda Azure Active Directory-autentisering (AD). Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory). 

* **Ett Azure-VNet**. Du kan använda Azure-portalen, Azure PowerShell, Azure-kommandoradsgränssnittet (CLI) eller andra metoder för att förbereda ett VNet med ett eller flera undernät i förväg. Om du vill skapa en Azure Resource Manager-baserade virtuella nätverk finns [skapa ett virtuellt nätverk med flera undernät](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). För att skapa ett klassiskt virtuellt nätverk, se [skapa ett virtuellt nätverk (klassiskt) med flera undernät](../virtual-network/create-virtual-network-classic.md).

### <a name="vnet-requirements"></a>VNet-krav
[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]
    
## <a name="create-a-pool-with-a-vnet-in-the-portal"></a>Skapa en pool med ett VNet i portalen

När du har skapat ditt VNet och tilldelats ett undernät, du kan skapa en Batch-pool med det virtuella nätverket. Följ dessa steg om du vill skapa en pool i Azure Portal: 



1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste vara i samma prenumeration och region som den resursgrupp som innehåller det VNet som du tänker använda. 
2. I den **inställningar** fönstret till vänster, Välj den **pooler** menyalternativet.
3. I den **pooler** väljer den **Lägg till** kommando.
4. På den **Lägg till Pool för** och väljer alternativet som du tänker använda från den **bildtypen** listrutan. 
5. Välj rätt **Publisher-erbjudande-Sku** för den anpassade avbildningen.
6. Ange de återstående nödvändiga inställningar, inklusive den **nodstorlek**, **mål dedikerade noder**, och **med låg prioritet noder**, liksom alla önskade valfria inställningar.
7. I **virtuellt nätverk**, Välj det virtuella nätverk och undernät som du vill använda.
  
  ![Lägg till poolen med virtuella nätverk](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade vägar för Tvingad tunneltrafik

Du kan behöva kraven i din organisation omdirigering (force) Internet-bunden trafik från undernätet tillbaka till den lokala platsen för kontroll och loggning. Du kan aktivera Tvingad tunneling för undernät i ditt VNet. 

För att säkerställa att det fungerar Azure Batch-pool compute-noder i ett virtuellt nätverk som har Tvingad tunneling aktiverat, måste du lägga till följande [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) för undernätet:

* Batch-tjänsten måste kommunicera med poolen compute-noder för schemalagda aktiviteter. Lägg till en användardefinierad väg för varje IP-adress som används av Batch-tjänsten i den region där Batch-kontot finns om du vill aktivera den här kommunikationen. Kontakta Azure-supporten om du vill hämta listan över IP-adresser för Batch-tjänsten.

* Se till att all utgående trafik till Azure Storage (särskilt URL: er för formuläret `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, och `<account>.blob.core.windows.net`) inte blockeras via din lokala nätverksenhet.

När du lägger till en användardefinierad väg definiera vägen för varje relaterade Batch IP-adressprefixet och ange **nästa hopptyp** till **Internet**. Se följande exempel:

![Användardefinierad väg](./media/batch-virtual-network/user-defined-route.png)

## <a name="next-steps"></a>Nästa steg

- En detaljerad översikt över Batch finns [utveckla storskaliga parallell compute lösningar med Batch](batch-api-basics.md).
- Mer information om hur du skapar en användardefinierad väg finns [skapa en användardefinierad väg - Azure-portalen](../virtual-network/tutorial-create-route-table-portal.md).
