---
title: 'SAFELIST Azure Portal URL: er | Microsoft Docs'
description: 'Lägg till dessa URL: er till proxyservern kringgå för kommunikation med Azure Portal och dess tjänster'
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9618d99495e65231de22eb719355e2ca75a17600
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218036"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>SAFELIST Azure Portal webb adresser på din brand vägg eller proxyserver

För bästa prestanda och anslutning mellan ditt lokala nätverk och det lokala nätverket och Azure-molnet konfigurerar du lokala säkerhetsenheter för att kringgå säkerhets begränsningar för Azure Portal-URL: er. Nätverks administratörer distribuerar ofta proxyservrar, brand väggar eller andra enheter för att skydda och styra hur användare får åtkomst till Internet. Regler som är utformade för att skydda användare kan dock ibland blockera eller sakta ned legitim affärs relaterad Internet trafik, inklusive kommunikation mellan dig och Azure. För att optimera anslutningen mellan ditt nätverk och Azure Portal och dess tjänster rekommenderar vi att du lägger till Azure Portal webb adresser i din SAFELIST.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal-URL: er för att kringgå proxyserver

URL-slutpunkterna till SAFELIST för Azure Portal är bara för det Azure-moln där organisationen har distribuerats. Välj molnet och Lägg sedan till listan med URL: er till proxyservern eller brand väggen för att tillåta nätverks trafik till dessa slut punkter för att kringgå begränsningar.

#### <a name="public-cloudtabpublic-cloud"></a>[Offentligt moln](#tab/public-cloud)
```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloudtabus-government-cloud"></a>[USA Myndighets moln](#tab/us-government-cloud)
```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Kinas regerings moln](#tab/china-government-cloud)
```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Trafik till dessa slut punkter använder standard-TCP-portar för HTTP (80) och HTTPS (443).
>
>
## <a name="next-steps"></a>Nästa steg

Behöver du SAFELIST IP-adresser? Ladda ned listan över Microsoft Azure Data Center IP-intervall för ditt moln:

* [Över hela världen](https://www.microsoft.com/download/details.aspx?id=56519)
* [USA Stat](https://www.microsoft.com/download/details.aspx?id=57063)
* [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)
* [Kina](https://www.microsoft.com/download/details.aspx?id=57062)

Andra Microsoft-tjänster använder ytterligare URL: er och IP-adresser för anslutning. Information om hur du optimerar nätverks anslutningen för Microsoft 365 tjänster finns i [Konfigurera ditt nätverk för Office 365](/office365/enterprise/set-up-network-for-office-365).
