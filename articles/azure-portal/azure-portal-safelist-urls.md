---
title: Skydda Azure-portalens URL:er i brandväggen eller proxyservern
description: Lägg till dessa url:er i proxyserver förbikoppling för att kommunicera med Azure-portalen och dess tjänster
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5bba7296a05cfbb72698a991ece1ef298689bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900654"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Skydda Azure-portalens URL:er i brandväggen eller proxyservern

Du kan konfigurera lokala säkerhetsenheter för att kringgå säkerhetsbegränsningar för Azure-portalens URL:er. Den här konfigurationen kan förbättra prestanda och anslutning mellan ditt lokala nätverk eller nätverk i stort område och Azure-molnet.

Nätverksadministratörer distribuerar ofta proxyservrar, brandväggar eller andra enheter. Dessa enheter hjälper till att skydda och ge kontroll över hur användare ansluter till Internet. Regler som utformats för att skydda användare kan ibland blockera eller bromsa legitim affärsrelaterad internettrafik. Den här trafiken inkluderar kommunikation mellan dig och Azure. Om du vill optimera anslutningen mellan nätverket och Azure-portalen och dess tjänster rekommenderar vi att du lägger till Azure-portal-URL:er i din safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Url:er för proxy förbikoppling av Azure-portalen

URL-slutpunkterna till safelist för Azure-portalen är specifika för Azure-molnet där din organisation distribueras. Om du vill att nätverkstrafik till dessa slutpunkter ska kunna kringgå begränsningar väljer du molnet. Lägg sedan till listan med webbadresser i proxyservern eller brandväggen.

#### <a name="public-cloud"></a>[Offentligt moln](#tab/public-cloud)

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

#### <a name="us-government-cloud"></a>[Molnet för amerikanska myndigheter](#tab/us-government-cloud)

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

#### <a name="china-government-cloud"></a>[Kina regeringen Cloud](#tab/china-government-cloud)

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
> Trafik till dessa slutpunkter använder vanliga TCP-portar för HTTP (80) och HTTPS (443).
>
>
## <a name="next-steps"></a>Nästa steg

Behöver du safelist IP-adresser? Hämta listan över IP-intervall för Microsoft Azure-datacenter för molnet:

* [Världsomfattande](https://www.microsoft.com/download/details.aspx?id=56519)
* [Usa:s regering](https://www.microsoft.com/download/details.aspx?id=57063)
* [Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)
* [Kina](https://www.microsoft.com/download/details.aspx?id=57062)

Andra Microsoft-tjänster använder ytterligare webbadresser och IP-adresser för anslutning. Mer om hur du optimerar nätverksanslutningen för Microsoft 365-tjänster finns i [Konfigurera nätverket för Office 365](/office365/enterprise/set-up-network-for-office-365).
