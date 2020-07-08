---
title: Konfigurera virtuell nätverks installation i Azure HDInsight
description: Lär dig hur du konfigurerar ett antal ytterligare funktioner för din virtuella nätverks installation i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 805be8d5c9ab4f6316251adbb9bce3e99f4fa01d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086678"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Konfigurera virtuell nätverks installation i Azure HDInsight

> [!Important]
> Följande information krävs **bara** om du vill konfigurera en annan virtuell nätverks installation (NVA) än Azure Firewall.

Azure-brandväggen konfigureras automatiskt för att tillåta trafik för många av de vanliga viktiga scenarierna. Om du använder en annan virtuell nätverks installation måste du konfigurera ett antal ytterligare funktioner. Tänk på följande faktorer när du konfigurerar din virtuella nätverks installation:

* Tjänster för tjänst slut punkt som kan konfigureras med tjänst slut punkter som leder till att NVA kringgås, vanligt vis för kostnads-eller prestanda överväganden.
* IP-adress beroenden är för trafik som inte är HTTP/S (både TCP-och UDP-trafik).
* FQDN HTTP/HTTPS-slutpunkter kan vit listas i din NVA-enhet.
* Tilldela den routningstabell som du skapar till ditt HDInsight-undernät.

## <a name="service-endpoint-capable-dependencies"></a>Tjänst slut punkt kompatibla beroenden

Du kan välja att aktivera en eller flera av följande tjänst slut punkter, vilket leder till att NVA ignoreras. Det här alternativet kan vara användbart för stora mängder data överföringar för att spara pengar och även för prestanda optimering. 

| **Slutpunkt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP-adress beroenden

| **Slutpunkt** | **Detaljer** |
|---|---|
| IP-adresser publicerade [här](hdinsight-management-ip-addresses.md) | De här IP-adresserna är för HDInsight-kontroll plats och bör ingå i UDR för att undvika asymmetrisk routning |
| AAD – DS privata IP-adresser | Krävs endast för ESP-kluster|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden

> [!Important]
> Listan nedan innehåller bara några av de viktigaste FQDN-namnen. Du kan hämta hela listan över FQDN (de flesta Azure Storage och Azure Service Bus) för att konfigurera din NVA [i den här filen](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json). Dessa beroenden används av HDInsights kontroll Plans åtgärder för att skapa ett kluster.

| **Slutpunkt**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Nästa steg

* [Använda brandväggen för att begränsa utgående trafik](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)
