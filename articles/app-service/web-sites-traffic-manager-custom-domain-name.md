---
title: Konfigurera DNS-namn för appar som använder Traffic Manager – Azure App Service
description: Använd ett anpassat domännamn för en en webbapp i Azure App Service med Traffic Manager för Utjämning av nätverksbelastning.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2b2efbbccedc2478b30a05fe2cd60c063de2a64d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309492"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Den här artikeln innehåller allmänna anvisningar för att använda ett anpassat domännamn med ett [Apptjänst](app-service-web-overview.md) app som är integrerad med [Traffic Manager](../traffic-manager/traffic-manager-overview.md) för belastningsutjämning.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Så här fungerar DNS-poster
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Konfigurera web apps för standardläge
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Lägga till en DNS-post för din anpassade domän
> [!NOTE]
> Om du har köpt domänen med hjälp av Azure App Service Web Apps och sedan hoppar du över följande steg och referera till det sista steget i [köp domän för Web Apps](custom-dns-web-site-buydomains-web-app.md) artikeln.
> 
> 

Om du vill associera din anpassade domän med en webbapp i Azure App Service, måste du lägga till en ny post i tabellen DNS för din anpassade domän. Du kan göra detta med hjälp av hanteringsverktyg från din domänleverantör.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Medan egenskaperna för varje domänleverantör variera du mappa *från* ditt domännamn (till exempel **contoso.com**) *till* Traffic Manager-domännamn ( **Contoso.trafficmanager.NET**) som är integrerade med ditt webbprogram.
   
> [!NOTE]
> Om en post används redan och du måste binda förebyggande syfte dina appar till den, kan du skapa en ytterligare CNAME-post. Till exempel för att binda förebyggande syfte **www.contoso.com** till din webbapp, skapa en CNAME-post från **awverify.www** till **contoso.trafficmanager.net**. Du kan sedan lägga till ”www.contoso.com” till din Webbapp utan att ändra CNAME-post ”www”. Mer information finns i [skapa DNS-poster för en webbapp i en anpassad domän][CREATEDNS].
> 
> 

När du är klar med att lägga till eller ändra DNS-posterna i din domänleverantör, kan du spara ändringarna.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Aktivera Traffic Manager
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/).

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
