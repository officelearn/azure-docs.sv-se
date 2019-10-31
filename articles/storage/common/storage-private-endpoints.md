---
title: Använda privata slut punkter med Azure Storage | Microsoft Docs
description: Översikt över privata slut punkter för säker åtkomst till lagrings konton från virtuella nätverk.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 09/25/2019
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 00de95f3b3e6eddd1f45be830202ba3ec8772bfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176164"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Använda privata slut punkter för Azure Storage (för hands version)

Du kan använda [privata slut punkter](../../private-link/private-endpoint-overview.md) för dina Azure Storage-konton om du vill tillåta klienter i ett virtuellt nätverk (VNet) att säkert få åtkomst till data via en [privat länk](../../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från VNet-adressutrymmet för din lagrings konto tjänst. Nätverks trafik mellan klienterna i VNet och lagrings kontot passerar över VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Med hjälp av privata slut punkter för ditt lagrings konto kan du:
- Skydda ditt lagrings konto genom att konfigurera lagrings brand väggen för att blockera alla anslutningar på den offentliga slut punkten för lagrings tjänsten.
- Ökar säkerheten för det virtuella nätverket (VNet) genom att göra det möjligt att blockera exfiltrering av data från VNet.
- Anslut säkert till lagrings konton från lokala nätverk som ansluter till VNet med [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../../expressroute/expressroute-locations.md) med privat peering.

## <a name="conceptual-overview"></a>Konceptuell översikt
![Privata slut punkter för Azure Storage översikt](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

En privat slut punkt är ett särskilt nätverks gränssnitt för en Azure-tjänst i din [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNet). När du skapar en privat slut punkt för ditt lagrings konto ger den säker anslutning mellan klienter i ditt VNet och ditt lagrings utrymme. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och lagrings tjänsten använder en säker privat länk.

Program i det virtuella nätverket kan ansluta till lagrings tjänsten via den privata slut punkten sömlöst med samma anslutnings strängar och auktoriseringsbeslut som de skulle använda i övrigt. Privata slut punkter kan användas med alla protokoll som stöds av lagrings kontot, inklusive REST och SMB.

När du skapar en privat slut punkt för en lagrings tjänst i ditt VNet, skickas en begäran om godkännande till lagrings kontots ägare. Om användaren som begär att den privata slut punkten ska skapas även är ägare till lagrings kontot, godkänns den här medgivande förfrågningen automatiskt.

Lagrings kontots ägare kan hantera medgivande förfrågningar och privata slut punkter via fliken "privata slut punkter" för lagrings kontot i [Azure Portal](https://portal.azure.com).

> [!TIP]
> Om du vill begränsa åtkomsten till ditt lagrings konto via enbart privat slut punkt konfigurerar du lagrings brand väggen så att all åtkomst nekas via den offentliga slut punkten.

Du kan skydda ditt lagrings konto så att det bara accepterar anslutningar från ditt VNet genom att [Konfigurera lagrings brand väggen](storage-network-security.md#change-the-default-network-access-rule) för att neka åtkomst via dess offentliga slut punkt som standard. Du behöver inte ha någon brand Väggs regel för att tillåta trafik från ett VNet som har en privat slut punkt, eftersom lagrings brand väggen bara kontrollerar åtkomst via den offentliga slut punkten. Privata slut punkter förlitar sig i stället på godkännande flödet för att ge undernät åtkomst till lagrings tjänsten.

### <a name="private-endpoints-for-storage-service"></a>Privata slut punkter för lagrings tjänst

När du skapar den privata slut punkten måste du ange det lagrings konto och den lagrings tjänst som den ansluter till. Du behöver en separat privat slut punkt för varje lagrings tjänst i ett lagrings konto som du behöver komma åt, nämligen [blobbar](../blobs/storage-blobs-overview.md), [data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [filer](../files/storage-files-introduction.md), [köer](../queues/storage-queues-introduction.md), [tabeller](../tables/table-storage-overview.md)eller [statiska webbplatser](../blobs/storage-blob-static-website.md).

> [!TIP]
> Skapa en separat privat slut punkt för den sekundära instansen av lagrings tjänsten för bättre Läs prestanda på RA-GRS-konton.

Om du vill läsa tillgänglighet på ett [Geo-redundant lagrings konto med Läs åtkomst](storage-redundancy-grs.md#read-access-geo-redundant-storage)behöver du separata privata slut punkter för både den primära och sekundära tjänstens instanser. Du behöver inte skapa en privat slut punkt för den sekundära instansen för **redundansväxling**. Den privata slut punkten ansluts automatiskt till den nya primära instansen efter redundansväxlingen. git 

#### <a name="resources"></a>Resurser

Mer detaljerad information om hur du skapar en privat slut punkt för ditt lagrings konto finns i följande artiklar:

- [Anslut privat till ett lagrings konto från lagrings konto upplevelsen i Azure Portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Skapa en privat slut punkt med hjälp av det privata länk centret i Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Skapa en privat slut punkt med Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Skapa en privat slut punkt med hjälp av Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slut punkter

Klienter i ett VNet kan använda samma anslutnings sträng för lagrings kontot även när du använder en privat slut punkt.

När du skapar en privat slut punkt uppdaterar vi DNS CNAME-resursposten för lagrings slut punkten till ett alias i en under domän med prefixet "*privatelink*". Som standard skapar vi också en [privat DNS-zon](../../dns/private-dns-overview.md) som är kopplad till det virtuella nätverket. Den här privata DNS-zonen motsvarar under domänen med prefixet "*privatelink*" och innehåller DNS A-resursposterna för de privata slut punkterna.

När du löser lagrings slut punktens URL från utanför det virtuella nätverket med den privata slut punkten matchas den offentliga slut punkten för lagrings tjänsten. Vid matchning från det VNet som är värd för den privata slut punkten matchas slut punktens URL-adress till den privata slut punktens IP-adress.

För det illustrerat exemplet ovan blir DNS-resursposterna för lagrings kontot StorageAccountA, när de löses från utanför det virtuella nätverket som är värd för den privata slut punkten,:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<offentlig slut punkt för lagrings tjänst\>                   |
| \<offentlig slut punkt för lagrings tjänst\>                   | A     | \<offentlig IP-adress för lagrings tjänst\>                 |

Som tidigare nämnts kan du neka all åtkomst via den offentliga slut punkten med hjälp av lagrings brand väggen.

DNS-resursposterna för StorageAccountA, när de löses av en klient i det VNet som är värd för den privata slut punkten, blir:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Den här metoden ger åtkomst till lagrings kontot med samma anslutnings sträng från det virtuella nätverk som är värd för privata slut punkter, samt klienter utanför VNet. Du kan använda lagrings brand väggen för att neka åtkomst till alla klienter utanför VNet.

> [!TIP]
> Om du använder en anpassad DNS-server eller en lokal DNS-server bör du använda under domänen "privatelink" för lagrings tjänsten för att konfigurera DNS-resursposter för de privata slut punkterna.

De rekommenderade DNS-zonnamn för privata slut punkter för lagrings tjänster är:

| Lagrings tjänst        | Zonnamn                            |
| :--------------------- | :----------------------------------- |
| Blob Service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Fil tjänst           | `privatelink.file.core.windows.net`  |
| Kötjänst          | `privatelink.queue.core.windows.net` |
| Table service          | `privatelink.table.core.windows.net` |
| Statiska webbplatser        | `privatelink.web.core.windows.net`   |

## <a name="pricing"></a>Prissättning

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Kända problem

### <a name="copy-blob-failures"></a>Kopiera BLOB-problem

För närvarande går det inte att [Kopiera BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) -kommandon som utfärdats till lagrings konton som nås via privata slut punkter när käll lagrings kontot skyddas av en brand vägg.

### <a name="subnets-with-service-endpoints"></a>Undernät med tjänst slut punkter
Under förhands granskningen kan du inte skapa en privat slut punkt i ett undernät som har tjänst slut punkter. Du kan skapa separata undernät i samma VNet för tjänst slut punkter och privata slut punkter.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Begränsningar för lagrings åtkomst för klienter i virtuella nätverk med privata slut punkter

Klienter i virtuella nätverk med befintliga privata slut punkter möter begränsningar vid åtkomst till andra lagrings konton som har privata slut punkter. Anta till exempel att ett VNet N1 har en privat slut punkt för ett lagrings konto a1 för, t. ex. blob service. Om lagrings konto a2 har en privat slut punkt i en VNet-N2 för Blob-tjänsten, måste klienter i VNet N1 också komma åt Blob-tjänsten för konto a2 med en privat slut punkt. Om lagrings konto a2 inte har några privata slut punkter för Blob-tjänsten, kan klienter i VNet N1 komma åt sin BLOB service utan en privat slut punkt.

Den här begränsningen beror på DNS-ändringar som gjorts när konto a2 skapar en privat slut punkt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regler för nätverks säkerhets grupper för undernät med privata slut punkter

För närvarande kan du inte konfigurera regler för [nätverks säkerhets grupper](../../virtual-network/security-overview.md) (NSG) för undernät med privata slut punkter. En begränsad lösning för det här problemet är att implementera åtkomst regler för privata slut punkter på käll under näten, även om den här metoden kan kräva en högre hanterings kostnad.
