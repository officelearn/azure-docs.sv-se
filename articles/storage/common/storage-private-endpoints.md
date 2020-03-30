---
title: Använda privata slutpunkter
titleSuffix: Azure Storage
description: Översikt över privata slutpunkter för säker åtkomst till lagringskonton från virtuella nätverk.
services: storage
author: santoshc
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: santoshc
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c51f2db698f30368c9d4090d3d571fa0c131178a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299064"
---
# <a name="use-private-endpoints-for-azure-storage"></a>Använda privata slutpunkter för Azure Storage

Du kan använda [privata slutpunkter](../../private-link/private-endpoint-overview.md) för dina Azure Storage-konton för att tillåta klienter i ett virtuellt nätverk (VNet) att på ett säkert sätt komma åt data via en [privat länk](../../private-link/private-link-overview.md). Den privata slutpunkten använder en IP-adress från VNet-adressutrymmet för din lagringskontotjänst. Nätverkstrafik mellan klienterna på det virtuella nätverket och lagringskontot passerar över det virtuella nätverket och en privat länk i Microsofts stamnätsnätverk, vilket eliminerar exponering från det offentliga internet.

Med hjälp av privata slutpunkter för ditt lagringskonto kan du:

- Skydda ditt lagringskonto genom att konfigurera lagringsbrandväggen så att alla anslutningar blockeras på den offentliga slutpunkten för lagringstjänsten.
- Öka säkerheten för det virtuella nätverket (VNet) genom att du kan blockera exfiltration av data från det virtuella nätverket.
- Anslut till lagringskonton från lokala nätverk som ansluter till det virtuella nätverket med HJÄLP AV [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) eller [ExpressRoutes](../../expressroute/expressroute-locations.md) med privat peering.

## <a name="conceptual-overview"></a>Konceptuell översikt

![Översikt över privata slutpunkter för Azure Storage](media/storage-private-endpoints/storage-private-endpoints-overview.jpg)

En privat slutpunkt är ett speciellt nätverksgränssnitt för en Azure-tjänst i ditt [virtuella nätverk](../../virtual-network/virtual-networks-overview.md) (VNet). När du skapar en privat slutpunkt för ditt lagringskonto ger den säker anslutning mellan klienter på ditt virtuella nätverk och din lagring. Den privata slutpunkten tilldelas en IP-adress från IP-adressintervallet för ditt virtuella nätverk. Anslutningen mellan den privata slutpunkten och lagringstjänsten använder en säker privat länk.

Program i det virtuella nätverket kan ansluta till lagringstjänsten via den privata slutpunkten sömlöst, **med samma anslutningssträngar och auktoriseringsmekanismer som de annars skulle använda**. Privata slutpunkter kan användas med alla protokoll som stöds av lagringskontot, inklusive REST och SMB.

Privata slutpunkter kan skapas i undernät som använder [Tjänstslutpunkter](../../virtual-network/virtual-network-service-endpoints-overview.md). Klienter i ett undernät kan på så sätt ansluta till ett lagringskonto med hjälp av privat slutpunkt, medan du använder tjänstslutpunkter för att komma åt andra.

När du skapar en privat slutpunkt för en lagringstjänst i ditt VNet skickas en begäran om godkännande till lagringskontots ägare. Om användaren som begär att den privata slutpunkten ska skapas också är ägare till lagringskontot godkänns den här begäran om medgivande automatiskt.

Lagringskontoägare kan hantera begäranden om medgivande och de privata slutpunkterna, via fliken *" Privata slutpunkter*" för lagringskontot i [Azure-portalen](https://portal.azure.com).

> [!TIP]
> Om du bara vill begränsa åtkomsten till ditt lagringskonto via den privata slutpunkten konfigurerar du lagringsbrandväggen så att den nekar eller styr åtkomsten via den offentliga slutpunkten.

Du kan skydda ditt lagringskonto så att det bara accepterar anslutningar från ditt virtuella nätverk genom att [konfigurera lagringsbrandväggen](storage-network-security.md#change-the-default-network-access-rule) så att den neka åtkomst via dess offentliga slutpunkt som standard. Du behöver inte en brandväggsregel för att tillåta trafik från ett virtuella nätverk som har en privat slutpunkt, eftersom lagringsbrandväggen bara styr åtkomsten via den offentliga slutpunkten. Privata slutpunkter förlitar sig i stället på medgivandeflödet för att bevilja undernät åtkomst till lagringstjänsten.

### <a name="private-endpoints-for-azure-storage"></a>Privata slutpunkter för Azure Storage

När du skapar den privata slutpunkten måste du ange lagringskontot och lagringstjänsten som den ansluter till. Du behöver en separat privat slutpunkt för varje lagringstjänst i ett lagringskonto som du behöver komma åt, nämligen [Blobbar,](../blobs/storage-blobs-overview.md) [Datasjölagringsgenm2](../blobs/data-lake-storage-introduction.md), [Filer,](../files/storage-files-introduction.md) [Köer,](../queues/storage-queues-introduction.md) [Tabeller](../tables/table-storage-overview.md)eller [Statiska webbplatser](../blobs/storage-blob-static-website.md).

> [!TIP]
> Skapa en separat privat slutpunkt för den sekundära instansen av lagringstjänsten för bättre läsprestanda på RA-GRS-konton.

För läsåtkomst till den sekundära regionen med ett lagringskonto konfigurerat för geoupptundssanta lagring behöver du separata privata slutpunkter för både tjänstens primära och sekundära instanser. Du behöver inte skapa en privat slutpunkt för den sekundära instansen för **redundans**. Den privata slutpunkten ansluter automatiskt till den nya primära instansen efter redundans. Mer information om lagringsredundansalternativ finns i [Azure Storage redundans](storage-redundancy.md).

Mer detaljerad information om hur du skapar en privat slutpunkt för ditt lagringskonto finns i följande artiklar:

- [Ansluta privat till ett lagringskonto från lagringskontoupplevelsen i Azure-portalen](../../private-link/create-private-endpoint-storage-portal.md)
- [Skapa en privat slutpunkt med private link center i Azure-portalen](../../private-link/create-private-endpoint-portal.md)
- [Skapa en privat slutpunkt med Azure CLI](../../private-link/create-private-endpoint-cli.md)
- [Skapa en privat slutpunkt med Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Ansluta till privata slutpunkter

Klienter på ett virtuella nätverk som använder den privata slutpunkten bör använda samma anslutningssträng för lagringskontot, som klienter som ansluter till den offentliga slutpunkten. Vi förlitar oss på DNS-lösning för att automatiskt dirigera anslutningarna från det virtuella nätverket till lagringskontot via en privat länk.

> [!IMPORTANT]
> Använd samma anslutningssträng för att ansluta till lagringskontot med hjälp av privata slutpunkter, som du skulle använda annars. Anslut inte till lagringskontot med hjälp av dess underdomänadress för*privatelink.*

Vi skapar en [privat DNS-zon](../../dns/private-dns-overview.md) kopplad till det virtuella nätverket med nödvändiga uppdateringar för de privata slutpunkterna, som standard. Om du använder din egen DNS-server kan du dock behöva göra ytterligare ändringar i DNS-konfigurationen. I avsnittet om [DNS-ändringar](#dns-changes-for-private-endpoints) nedan beskrivs de uppdateringar som krävs för privata slutpunkter.

## <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slutpunkter

När du skapar en privat slutpunkt uppdateras DNS CNAME-resursposten för lagringskontot till ett alias i en underdomän med prefixet '*privatelink*'. Som standard skapar vi också en [privat DNS-zon](../../dns/private-dns-overview.md), som motsvarar underdomänen '*privatelink*, med DNS A-resursposterna för de privata slutpunkterna.

När du matchar url:en för lagringsslutpunkt utanför det virtuella nätverket med den privata slutpunkten, matchas den till lagringstjänstens offentliga slutpunkt. När den matchas från det virtuella nätverket som är värd för den privata slutpunkten matchas url-url:en för lagring till den privata slutpunktens IP-adress.

För det illustrerade exemplet ovan kommer DNS-resursposterna för lagringskontot StorageAccountA, när de matchas utanför det virtuella nätverket som är värd för den privata slutpunkten, att vara:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | CNAME | \<offentliga slutpunkten för lagringstjänster\>                   |
| \<offentliga slutpunkten för lagringstjänster\>                   | A     | \<public IP-adress för lagringstjänster\>                 |

Som tidigare nämnts kan du neka eller kontrollera åtkomst för klienter utanför det virtuella nätverket via den offentliga slutpunkten med hjälp av lagringsbrandväggen.

DNS-resursposterna för StorageAccountA, när de matchas av en klient i det virtuella nätverket som är värd för den privata slutpunkten, kommer att vara:

| Namn                                                  | Typ  | Värde                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``StorageAccountA.blob.core.windows.net``             | CNAME | ``StorageAccountA.privatelink.blob.core.windows.net`` |
| ``StorageAccountA.privatelink.blob.core.windows.net`` | A     | 10.1.1.5                                              |

Den här metoden ger åtkomst till lagringskontot **med samma anslutningssträng** för klienter på det virtuella nätverket som är värd för de privata slutpunkterna, samt klienter utanför det virtuella nätverket.

Om du använder en anpassad DNS-server i nätverket måste klienter kunna matcha FQDN för slutpunkten för lagringskontot till den privata slutpunkts-IP-adressen. Du bör konfigurera DNS-servern så att den delegerar den privata länkunderdomänen till den privata DNS-zonen för det virtuella nätverket, eller konfigurera A-posterna för *' StorageAccountA.privatelink.blob.core.windows.net*' med den privata slutpunkts-IP-adressen.

> [!TIP]
> När du använder en anpassad eller lokal DNS-server bör du konfigurera DNS-servern så att lagringskontonamnet i underdomänen "privatelink" matchas till den privata slutpunkts-IP-adressen. Du kan göra detta genom att delegera underdomänen "privatelink" till den privata DNS-zonen i det virtuella nätverket, eller konfigurera DNS-zonen på DNS-servern och lägga till DNS A-posterna.

De rekommenderade DNS-zonnamnen för privata slutpunkter för lagringstjänster är:

| Lagringstjänst        | Zonnamn                            |
| :--------------------- | :----------------------------------- |
| Blob Service           | `privatelink.blob.core.windows.net`  |
| Data Lake Storage Gen2 | `privatelink.dfs.core.windows.net`   |
| Filtjänst           | `privatelink.file.core.windows.net`  |
| Kötjänst          | `privatelink.queue.core.windows.net` |
| Tabelltjänst          | `privatelink.table.core.windows.net` |
| Statiska webbplatser        | `privatelink.web.core.windows.net`   |

Mer information om hur du konfigurerar din egen DNS-server för att stödja privata slutpunkter finns i följande artiklar:

- [Namnmatchning för resurser i virtuella nätverk i Azure](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [DNS-konfiguration för privata slutpunkter](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="pricing"></a>Prissättning

Information om priser finns i [Azure Private Link-priser](https://azure.microsoft.com/pricing/details/private-link).

## <a name="known-issues"></a>Kända problem

Tänk på följande kända problem om privata slutpunkter för Azure Storage.

### <a name="copy-blob-support"></a>Stöd för kopiera blob

Om lagringskontot skyddas av en brandvägg och kontot nås via privata slutpunkter kan det kontot inte fungera som källa till en [kopierablobåtgärd.](/rest/api/storageservices/copy-blob)

### <a name="storage-access-constraints-for-clients-in-vnets-with-private-endpoints"></a>Åtkomstbegränsningar för lagring för klienter i virtuella nätverk med privata slutpunkter

Klienter i virtuella nätverk med befintliga privata slutpunkter har begränsningar när de kommer åt andra lagringskonton som har privata slutpunkter. Anta till exempel att en VNet N1 har en privat slutpunkt för ett lagringskonto A1 för Blob-lagring. Om lagringskonto A2 har en privat slutpunkt i en VNet N2 för Blob-lagring måste klienter i VNet N1 också komma åt Blob-lagring i konto A2 med hjälp av en privat slutpunkt. Om lagringskonto A2 inte har några privata slutpunkter för Blob-lagring kan klienter i VNet N1 komma åt Blob-lagring på det kontot utan en privat slutpunkt.

Det här villkoret är ett resultat av de DNS-ändringar som gjorts när konto A2 skapar en privat slutpunkt.

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>Regler för nätverkssäkerhetsgrupper för undernät med privata slutpunkter

För närvarande kan du inte konfigurera NSG-regler [(Network Security Group)](../../virtual-network/security-overview.md) och användardefinierade vägar för privata slutpunkter. NSG-regler som tillämpas på undernätet som är värd för den privata slutpunkten tillämpas på den privata slutpunkten. En begränsad lösning på problemet är att implementera dina åtkomstregler för privata slutpunkter i källundernäten, även om den här metoden kan kräva en högre hanteringskostnader.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure Storage-brandväggar och virtuella nätverk](storage-network-security.md)
- [Säkerhetsrekommendationer för Blob-lagring](../blobs/security-recommendations.md)
