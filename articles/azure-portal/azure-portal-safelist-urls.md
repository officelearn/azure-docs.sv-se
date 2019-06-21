---
title: 'Safelist Azure-portalen URL: er | Microsoft Docs'
description: 'Lägg till dessa URL: er i proxyserverundantag att kommunicera med Azure portal och dess tjänster'
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 06/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 87ec600a2f6c4a560ec7cbb064b561fa76e2b615
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305238"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Safelist Azure-portalen URL: er på din brandvägg eller proxy server

Konfigurera lokala säkerhetsenheter för att kringgå begränsningar för Azure-portalen för god prestanda och anslutning mellan ditt lokala - eller WAN-nätverk och Azure-molnet, URL: er. Administratörer distribuera ofta proxyservrar, brandväggar och andra enheter för att skydda och ger kontroll över hur användare kommer åt internet. Regler som har utformats för att skydda användare kan dock ibland blockera eller långsammare berättigade affärsrelaterade internet-trafik, inklusive kommunikation mellan dig och Azure. För att optimera anslutning mellan ditt nätverk och Azure-portalen och dess tjänster, rekommenderar vi du lägger till Azure portal-URL som din safelist.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Kringgå Azure portal-URL: er för proxy

Lägg till följande lista över URL: er i din proxyserver eller brandvägg att tillåta trafik till dessa slutpunkter att kringgå begränsningar:

* *.aadcdn.microsoftonline-p.com
* *.aimon.applicationinsights.io
* *.azure.com
* *.azuredatalakestore.net
* *.azureedge.net
* *.exp.azure.com
* *.ext.azure.com
* *.gfx.ms
* *.account.microsoft.com
* *.hosting.portal.azure.net
* *.marketplaceapi.microsoft.com
* *.microsoftonline.com
* *.msauth.net
* *.msftauth.net
* *.portal.azure.com
* *.portalext.visualstudio.com
* *.sts.microsoft.com
* *.vortex.data.microsoft.com
* *.vscommerce.visualstudio.com
* *.vssps.visualstudio.com
* *.wpc.azureedge.net

> [!NOTE]
> Trafik till dessa slutpunkter använder standard TCP-portar för HTTP (80) och HTTPS (443).
>
>
## <a name="next-steps"></a>Nästa steg

* Behöver du safelist IP-adresser? Hämta listan över [Microsoft Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).
* Andra Microsoft-tjänster används ytterligare URL: er och IP-adresser för anslutning. För att optimera nätverksanslutningar för Microsoft 365-tjänster, se [konfigurera nätverket för Office 365](/office365/enterprise/set-up-network-for-office-365).
