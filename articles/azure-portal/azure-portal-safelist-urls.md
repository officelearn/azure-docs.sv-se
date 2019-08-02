---
title: 'SAFELIST Azure Portal URL: er | Microsoft Docs'
description: 'Lägg till dessa URL: er till proxyservern kringgå för kommunikation med Azure Portal och dess tjänster'
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3747ca7504e1a8a6bbeb6237c1b3cb2e5e4afb5b
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667480"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>SAFELIST Azure Portal webb adresser på din brand vägg eller proxyserver

För bästa prestanda och anslutning mellan ditt lokala nätverk och det lokala nätverket och Azure-molnet konfigurerar du lokala säkerhetsenheter för att kringgå säkerhets begränsningar för Azure Portal-URL: er. Nätverks administratörer distribuerar ofta proxyservrar, brand väggar eller andra enheter för att skydda och styra hur användare får åtkomst till Internet. Regler som är utformade för att skydda användare kan dock ibland blockera eller sakta ned legitim affärs relaterad Internet trafik, inklusive kommunikation mellan dig och Azure. För att optimera anslutningen mellan ditt nätverk och Azure Portal och dess tjänster rekommenderar vi att du lägger till Azure Portal webb adresser i din SAFELIST.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Azure Portal-URL: er för att kringgå proxyserver

Lägg till följande lista med URL: er till proxyservern eller brand väggen för att tillåta nätverks trafik till dessa slut punkter för att kringgå begränsningar:

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
* *.windows.net
* *.wpc.azureedge.net

> [!NOTE]
> Trafik till dessa slut punkter använder standard-TCP-portar för HTTP (80) och HTTPS (443).
>
>
## <a name="next-steps"></a>Nästa steg

* Behöver du SAFELIST IP-adresser? Ladda ned listan över [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).
* Andra Microsoft-tjänster använder ytterligare URL: er och IP-adresser för anslutning. Information om hur du optimerar nätverks anslutningen för Microsoft 365 tjänster finns i [Konfigurera ditt nätverk för Office 365](/office365/enterprise/set-up-network-for-office-365).
