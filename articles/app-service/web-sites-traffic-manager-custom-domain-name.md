---
title: Konfigurera DNS-namn med Traffic Manager
description: Lär dig hur du konfigurerar en anpassad domän för en Azure App Service-app som integreras med Traffic Manager för belastnings utjämning.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 08/17/2016
ms.custom: seodec18
ms.openlocfilehash: 9139b83f1f2920da47b4a0d440f622626d41c938
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689277"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurera ett anpassat domän namn för en webbapp i Azure App Service att använda Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Den här artikeln innehåller allmänna instruktioner för att använda ett anpassat domän namn med en [App Service](overview.md) -app som är integrerad med [Traffic Manager](../traffic-manager/traffic-manager-overview.md) för belastnings utjämning.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Förstå DNS-poster
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurera dina webb program för standard läge
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Lägg till en DNS-post för din anpassade domän
> [!NOTE]
> Om du har köpt domän via Azure App Service Web Apps kan du hoppa över följande steg och se det sista steget i [köpa domänen för Web Apps](manage-custom-dns-buy-domain.md) artikeln.
> 
> 

Om du vill associera en anpassad domän med en webbapp i Azure App Service måste du lägga till en ny post i DNS-tabellen för din anpassade domän. Du gör detta med hjälp av hanterings verktygen från din domän leverantör.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Även om de olika domän leverantörerna skiljer sig åt, mappar du *från* ditt anpassade domän namn (t. ex. **contoso.com**) *till* det Traffic Manager domän namn (**contoso.trafficmanager.net**) som är integrerat med din webbapp.

> [!NOTE]
> Om en post redan används och du behöver förebyggande syfte för att binda dina appar till den, kan du skapa ytterligare en CNAME-post. Om du till exempel vill förebyggande syfte bind **www\.contoso.com** till din webbapp skapar du en CNAME-post från **awverify. www** till **contoso.trafficmanager.net**. Du kan sedan lägga till "www\.contoso.com" i din webbapp utan att ändra "www"-CNAME-posten. Mer information finns i [Skapa DNS-poster för en webbapp i en anpassad domän][CREATEDNS].

När du har lagt till eller ändrat DNS-poster i din domän leverantör sparar du ändringarna.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Aktivera Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
