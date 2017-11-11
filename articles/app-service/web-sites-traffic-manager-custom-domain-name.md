---
title: "Konfigurera ett anpassat domännamn för en webbapp i Azure App Service som använder Traffic Manager för belastningsutjämning."
description: "Använd ett anpassat domännamn för en en webbapp i Azure App Service med Traffic Manager för belastningsutjämning."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: 69c7984d0620b4a0fd40252129023093c09d6e56
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Den här artikeln innehåller allmänna anvisningar för användning av ett anpassat domännamn med ett [Apptjänst](app-service-web-overview.md) app som är integrerad med [Traffic Manager](../traffic-manager/traffic-manager-overview.md) för belastningsutjämning.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Förstå DNS-poster
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurera dina webbprogram för standardläge
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Lägga till en DNS-post för den anpassade domänen
> [!NOTE]
> Om du har köpt domänen med hjälp av Azure App Service Web Apps och hoppa över följande steg och referera till det sista steget i [köpa domän för Web Apps](custom-dns-web-site-buydomains-web-app.md) artikel.
> 
> 

Om du vill associera din anpassade domän med en webbapp i Azure App Service måste du lägga till en ny post i DNS-tabell för den anpassade domänen. Du kan göra detta med hjälp av hanteringsverktygen från din domän-providern.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Medan egenskaperna för varje domän provider variera mappa *från* ditt domännamn (t.ex **contoso.com**) *till* Traffic Manager-domännamn ( **Contoso.trafficmanager.NET**) som är integrerad med ditt webbprogram.
   
> [!NOTE]
> Om en post används redan och du måste binda pre-emptively dina appar till den, kan du skapa en ytterligare CNAME-post. Till exempel för att binda pre-emptively **www.contoso.com** till ditt webbprogram, skapa en CNAME-post från **awverify.www** till **contoso.trafficmanager.net**. Du kan sedan lägga till ”www.contoso.com” ditt webbprogram utan att ändra ”www” CNAME-post. Mer information finns i [skapa DNS-poster för ett webbprogram i en anpassad domän][CREATEDNS].
> 
> 

När du är klar med att lägga till eller ändra DNS-posterna i din domänleverantör, kan du spara ändringarna.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Aktivera Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Node.js Developer Center](/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
