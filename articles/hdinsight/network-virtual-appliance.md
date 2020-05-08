---
title: Konfigurera virtuell nätverks installation i Azure HDInsight
description: Lär dig hur du konfigurerar ett antal ytterligare funktioner för din virtuella nätverks installation i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864713"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurera virtuell nätverks installation i Azure HDInsight

> [!Important]
> Följande information krävs **bara** om du vill konfigurera en annan virtuell nätverks installation (NVA) än Azure Firewall.

Azure-brandväggen konfigureras automatiskt för att tillåta trafik för många av de vanliga viktiga scenarierna. Om du använder en annan virtuell nätverks installation måste du konfigurera ett antal ytterligare funktioner. Tänk på följande faktorer när du konfigurerar din virtuella nätverks installation:

* Tjänst slut punkts tjänster som stöder tjänster måste konfigureras med tjänst slut punkter.
* IP-adress beroenden är för trafik som inte är HTTP/S (både TCP-och UDP-trafik).
* FQDN HTTP/HTTPS-slutpunkter kan placeras i din NVA-enhet.
* HTTP/HTTPS-slutpunkter med jokertecken är beroenden som kan variera beroende på ett antal kvalificerare.
* Tilldela den routningstabell som du skapar till ditt HDInsight-undernät.

## <a name="service-endpoint-capable-dependencies"></a>Tjänst slut punkt kompatibla beroenden

| **Endpoint** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP-adress beroenden

| **Endpoint** | **Information** |
|---|---|
| \*: 123 | Kontroll av NTP-klocka. Trafiken kontrol leras på flera slut punkter på port 123 |
| IP-adresser publicerade [här](hdinsight-management-ip-addresses.md) | De här IP-adresserna är HDInsight-tjänsten |
| AAD – DS privata IP-adresser för ESP-kluster |
| \*: 16800 för Windows-aktivering i KMS |
| \*12000 för Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

> [!Important]
> Listan nedan innehåller bara några av de viktigaste FQDN-namnen. Du kan hämta ytterligare FQDN (Azure Storage och Azure Service Bus) för att konfigurera din NVA [i den här filen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpoint**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Nästa steg

* [Använda brandväggen för att begränsa utgående trafik](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
