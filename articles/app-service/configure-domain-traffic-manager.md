---
title: Konfigurera DNS-namn med Traffic Manager
description: Lär dig hur du konfigurerar en anpassad domän för en Azure App Service-app som integreras med Traffic Manager för belastningsutjämning.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944133"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurera ett anpassat domännamn i Azure App Service med Traffic Manager-integrering

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> För Molntjänster finns [i Konfigurera ett anpassat domännamn för en Azure-molntjänst](../cloud-services/cloud-services-custom-domain-name.md).

När du använder [Azure Traffic Manager](/azure/traffic-manager/) för att läsa in aldotrafik till Azure App [Service](overview.md)kan App Service-appen nås med ** \<trafik-manager-slutpunkt>.trafficmanager.net**. Du kan tilldela ett anpassat domännamn, till exempel www\.contoso.com, med appens tjänst för att ge användarna ett mer igenkännligt domännamn.

Den här artikeln visar hur du konfigurerar ett anpassat domännamn med en App Service-app som är integrerad med [Traffic Manager](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Endast [CNAME-poster](https://en.wikipedia.org/wiki/CNAME_record) stöds när du konfigurerar ett domännamn med slutpunkten Traffic Manager. Eftersom A-poster inte stöds stöds inte heller en rotdomänmappning, till exempel contoso.com.
> 

## <a name="prepare-the-app"></a>Förbereda appen

Om du vill mappa ett anpassat DNS-namn till en app som är integrerad med Azure Traffic Manager måste webbappens [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) vara på **standardnivå** eller högre. I det här steget ser du till att App Service-appen har en prisnivå som stöds.

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

Sök efter och välj **App Services**i [Azure-portalen](https://portal.azure.com).

På sidan **App Services** väljer du namnet på din Azure-app.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

I den vänstra navigeringen på appsidan väljer du **Skala upp (App Service-plan).**

![Skala upp-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen är på **standardnivå** eller högre (valfri nivå i kategorin **Produktion** eller **Isolerad).** Om ja stänger du sidan **Skala upp** och går till [Skapa CNAME-mappningen](#create-the-cname-mapping).

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

Om du behöver skala upp appen väljer du någon av prisnivåerna i kategorin **Produktion.** Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Använd**.

## <a name="create-traffic-manager-endpoint"></a>Skapa Slutpunkt för Traffic Manager

Om du följer stegen i [Lägg till eller ta bort slutpunkter](../traffic-manager/traffic-manager-endpoints.md)lägger du till appen App Service som en slutpunkt i Traffic Manager-profilen.

När apptjänstappen har en prisnivå som stöds visas den i listan över tillgängliga App Service-mål när du lägger till slutpunkten. Om appen inte finns med i listan [kontrollerar du prisnivån för din app](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Skapa CNAME-mappningen
> [!NOTE]
> Om du vill konfigurera en [App Service-domän som du har köpt](manage-custom-dns-buy-domain.md)hoppar du över det här avsnittet och går till Aktivera anpassad [domän](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Även om detaljerna för varje domänleverantör varierar, mappar du *från* det anpassade domännamnet (till exempel **contoso.com)** *till* Traffic Manager-domännamnet (**contoso.trafficmanager.net**) som är integrerat med din app.

> [!NOTE]
> Om en post redan används och du måste i förebyggande syfte binda dina appar till den kan du skapa ytterligare en CNAME-post. Om du till exempel i förebyggande syfte vill binda **www\.contoso.com** till din app skapar du en CNAME-post från **awverify.www** för att **contoso.trafficmanager.net**. Du kan sedan\.lägga till "www contoso.com" i din app utan att behöva ändra "www" CNAME-posten. Mer information finns i [Migrera ett aktivt DNS-namn till Azure App Service](manage-custom-dns-migrate-domain.md).

När du har lagt till eller ändrat DNS-poster hos domänleverantören sparar du ändringarna.

## <a name="enable-custom-domain"></a>Aktivera anpassad domän
När posterna för ditt domännamn har spridits använder du webbläsaren för att kontrollera att ditt anpassade domännamn matchas till appen App Service.

> [!NOTE]
> Det kan ta lite tid innan CNAME sprids via DNS-systemet. Du kan använda en <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> tjänst som för att kontrollera att CNAME är tillgängligt.
> 
> 

1. När domänlösningen har slutförts, för att gå tillbaka till din appsida i [Azure Portal](https://portal.azure.com)
2. Välj **Anpassade domäner** > **Lägg till värdnamn**från den vänstra navigeringen .
4. Skriv det anpassade domännamnet som du har mappat tidigare och välj **Validera**.
5. Kontrollera att **posttypen Hostname** är inställd **på CNAME (www\.example.com eller någon underdomän)**.

6. Eftersom App Service-appen nu är integrerad med en Traffic Manager-slutpunkt bör du se Traffic Manager-domännamnet under **CNAME-konfiguration**. Markera den och klicka på **Lägg till anpassad domän**.

    ![Lägg till DNS-namnet i appen](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md)
