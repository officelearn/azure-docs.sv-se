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
ms.openlocfilehash: aec12cee7466e59389b28742bf66247751a0d22b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949474"
---
# <a name="using-private-endpoints-for-azure-storage-preview"></a>Använda privata slut punkter för Azure Storage (för hands version)

Azure Storage möjliggör användning av [privata slut punkter](../../private-link/private-endpoint-overview.md) för klienter i ett virtuellt nätverk (VNet) för säker åtkomst till data i ett lagrings konto via en [privat länk](../../private-link/private-link-overview.md). Den privata slut punkten använder en IP-adress från VNet-adressutrymmet för din lagrings konto tjänst. Nätverks trafik mellan klienterna i VNet och lagrings kontot passerar över VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Med hjälp av privata slut punkter för ditt lagrings konto kan du:
- Skydda ditt lagrings konto genom att konfigurera lagrings brand väggen för att blockera alla anslutningar på den offentliga slut punkten för lagrings tjänsten.
- Ökar säkerheten för det virtuella nätverket (VNet) genom att göra det möjligt att blockera exfiltrering av data från VNet.
- Anslut säkert till lagrings konton från lokala nätverk som ansluter till VNet med [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoute](../../expressroute/expressroute-locations.md) med privat peering.

## <a name="conceptual-overview"></a>Konceptuell översikt
![Privata slut punkter för Azure Storage översikt](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

En privat slut punkt är ett särskilt nätverks gränssnitt i din [Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNet) för en Azure-tjänst. Den ger säker anslutning mellan klienter i ditt VNet och ditt lagrings konto. Den privata slut punkten tilldelas en IP-adress från det virtuella nätverkets IP-adressintervall. Anslutningen mellan den privata slut punkten och lagrings tjänsten använder en säker privat länk.

Program i det virtuella nätverket kan ansluta till lagrings tjänsten via den privata slut punkten sömlöst med samma anslutnings strängar och auktoriseringsbeslut som de skulle använda i övrigt. Privata slut punkter kan användas med alla protokoll som stöds av lagrings kontot, inklusive REST och SMB.

När du skapar en privat slut punkt för en lagrings tjänst i ditt VNet, skickas en begäran om godkännande till lagrings kontots ägare. Om användaren som begär att den privata slut punkten ska skapas även är ägare till lagrings kontot, godkänns den här medgivande förfrågningen automatiskt.

Lagrings kontots ägare kan godkänna eller avvisa begäran om godkännande, samt Visa eller hantera privata slut punkter, via fliken "privata slut punkter" för lagrings kontot i [Azure Portal](https://portal.azure.com).

Du kan skydda ditt lagrings konto så att det bara accepterar anslutningar från ditt VNet genom att [Konfigurera lagrings brand väggen](storage-network-security.md#change-the-default-network-access-rule) för att neka åtkomst via dess offentliga slut punkt som standard. Du behöver ingen lagrings brand Väggs regel för att tillåta trafik från ett VNet som har en privat slut punkt, eftersom lagrings brand Väggs reglerna endast gäller för dess offentliga slut punkt. Privata slut punkter förlitar sig i stället på godkännande flödet för att ge undernät åtkomst till lagrings tjänsten.

### <a name="private-endpoints-for-storage-service"></a>Privata slut punkter för lagrings tjänst

När du skapar den privata slut punkten måste du ange det lagrings konto och den lagrings tjänst som den ansluter till. Du behöver en privat slut punkt för varje lagrings tjänst i ett lagrings konto som behöver åtkomst, nämligen [blobbar](../blobs/storage-blobs-overview.md), [data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md), [filer](../files/storage-files-introduction.md), [köer](../queues/storage-queues-introduction.md), [tabeller](../tables/table-storage-overview.md)eller [statiska webbplatser](../blobs/storage-blob-static-website.md).

För att säkerställa Läs tillgänglighet för ett [Geo-redundant lagrings konto med Läs åtkomst](storage-redundancy-grs.md#read-access-geo-redundant-storage)behöver du separata privata slut punkter för både den primära och sekundära tjänstens instanser.

#### <a name="resources"></a>Resurser

Mer detaljerad information om hur du skapar en privat slut punkt för ditt lagrings konto finns i följande artiklar:

- [Anslut privat till ett lagrings konto från lagrings konto upplevelsen i Azure Portal](../../private-link/create-private-endpoint-storage-portal.md)
- [Skapa en privat slut punkt med hjälp av det privata länk centret i Azure Portal](../../private-link/create-private-endpoint-portal.md)
- [Skapa en privat slut punkt med Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Skapa en privat slut punkt med hjälp av Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slut punkter

När du skapar en privat slut punkt för en lagrings tjänst uppdaterar vi DNS CNAME-resursposten för lagrings slut punkten till ett alias i en under domän med prefixet "*privatelink*". Som standard skapar vi också en [privat DNS-zon](../../dns/private-dns-overview.md) som är kopplad till det virtuella nätverket. Den här privata DNS-zonen motsvarar under domänen med prefixet "*privatelink*" och innehåller DNS A-resursposterna för de privata slut punkterna.

När du löser slut punktens slut punkts-URL från utanför det virtuella nätverk där den privata slut punkten skapas, löses den fortfarande till den offentliga slut punkten för lagrings tjänsten. Vid matchning från det VNet som är värd för den privata slut punkten matchas slut punktens URL-adress till den privata slut punktens IP-adress.

För det illustrerat exemplet ovan blir DNS-resursposterna för lagrings kontot StorageAccountA, när de löses från utanför det virtuella nätverket som är värd för den privata slut punkten,:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<offentlig slut punkt\>                                   |
| \<offentlig slut punkt\>                                   | A     | \<offentlig IP-adress för lagrings tjänst\>                 |

Som tidigare nämnts kan du neka all åtkomst via den offentliga slut punkten med hjälp av lagrings brand väggen.

DNS-resursposterna för StorageAccountA, när de löses av en klient i det VNet som är värd för den privata slut punkten, blir:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Den här metoden ger åtkomst till lagrings kontot med samma anslutnings sträng från det virtuella nätverk som är värd för privata slut punkter, samt klienter utanför VNet. Du kan använda lagrings brand väggen för att neka åtkomst till alla klienter utanför VNet.

## <a name="pricing"></a>Prissättning

Pris information finns i [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Kända problem

### <a name="copy-blob-failures"></a>Kopiera BLOB-problem

För närvarande går det inte att [Kopiera BLOB](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) -kommandon som utfärdats till lagrings konton som nås via privata slut punkter när käll lagrings kontot skyddas av en brand vägg.

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Begränsningar för lagrings åtkomst för klienter i virtuella nätverk med privata slut punkter

Klienter i virtuella nätverk som har en befintlig lagrings plats för privat slut punkts begränsningar vid åtkomst till andra lagrings konton med privata slut punkter. Anta till exempel att ett VNet N1 har en privat slut punkt för ett lagrings konto a1 för, t. ex. blob service. Om lagrings konto a2 har en privat slut punkt i en VNet-N2 för Blob-tjänsten, måste klienter i VNet N1 även komma åt Blob-tjänsten i konto a2 med en privat slut punkt. Om lagrings konto a2 inte har några privata slut punkter för Blob-tjänsten, kan klienter i VNet N1 komma åt sin BLOB service utan en privat slut punkt.

Den här begränsningen beror på DNS-ändringar som gjorts när konto a2 skapar en privat slut punkt.

### <a name="nsg-rules-on-subnets-with-private-endpoints"></a>NSG-regler för undernät med privata slut punkter

Regler för [nätverks säkerhets gruppen](../../virtual-network/security-overview.md) (NSG) kan inte konfigureras för undernät med privata slut punkter, för tillfället. En begränsad lösning för det här problemet är att implementera åtkomst regler för privata slut punkter på käll under näten, även om den här metoden kan kräva en högre hanterings kostnad.
