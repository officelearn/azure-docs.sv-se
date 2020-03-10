---
title: Konfigurera DNS-namn med Traffic Manager
description: Lär dig hur du konfigurerar en anpassad domän för en Azure App Service-app som integreras med Traffic Manager för belastnings utjämning.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: f8322c12669e41fc7c9aa88e99f95cf1b26ea87d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944133"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Konfigurera ett anpassat domän namn i Azure App Service med Traffic Manager-integrering

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Mer Cloud Services finns i [Konfigurera ett anpassat domän namn för en Azure-moln tjänst](../cloud-services/cloud-services-custom-domain-name.md).

När du använder [Azure Traffic Manager](/azure/traffic-manager/) för att belastningsutjämna trafik till [Azure App Service](overview.md), kan App Service-appen nås med **\<Traffic-Manager-Endpoint >. trafficmanager. net**. Du kan tilldela ett anpassat domän namn, till exempel www\.contoso.com, med din App Service-app för att ge användarna ett mer igenkännbart domän namn.

Den här artikeln visar hur du konfigurerar ett anpassat domän namn med en App Service-app som är integrerad med [Traffic Manager](../traffic-manager/traffic-manager-overview.md).

> [!NOTE]
> Endast [CNAME](https://en.wikipedia.org/wiki/CNAME_record) -poster stöds när du konfigurerar ett domän namn med hjälp av Traffic Manager slut punkten. Eftersom det inte finns stöd för poster stöds inte heller en rot domän mappning, till exempel contoso.com.
> 

## <a name="prepare-the-app"></a>Förbereda appen

För att mappa ett anpassat DNS-namn till en app som är integrerad med Azure Traffic Manager, måste webbappens [App Service plan](https://azure.microsoft.com/pricing/details/app-service/) vara på **standard** -eller högre nivå. I det här steget ser du till att App Service-appen har en prisnivå som stöds.

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I [Azure Portal](https://portal.azure.com)söker du efter och väljer **app Services**.

På sidan **app Services** väljer du namnet på din Azure-App.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

I det vänstra navigerings fönstret på sidan app väljer du **skala upp (App Service plan)** .

![Skala upp-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Appens aktuell nivå markeras med en blå kantlinje. Kontrol lera att appen är på **standard** nivå eller högre (vilken nivå som helst i **produktions miljön** eller den **isolerade** kategorin). Om ja, Stäng sidan **skala upp** och gå vidare till [skapa CNAME-mappningen](#create-the-cname-mapping).

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

Om du behöver skala upp din app väljer du någon av pris nivåerna i **produktions** kategorin. Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Verkställ**.

## <a name="create-traffic-manager-endpoint"></a>Skapa Traffic Manager-slutpunkt

Följ stegen i [Lägg till eller ta bort slut punkter](../traffic-manager/traffic-manager-endpoints.md), Lägg till din app service-app som en slut punkt i din Traffic Managers profil.

När App Service-appen har en pris nivå som stöds visas den i listan över tillgängliga App Service mål när du lägger till slut punkten. Om din app inte visas i listan [kontrollerar du pris nivån för din app](#prepare-the-app).

## <a name="create-the-cname-mapping"></a>Skapa CNAME-mappning
> [!NOTE]
> Om du vill konfigurera en [App Service domän som du har köpt](manage-custom-dns-buy-domain.md)hoppar du över det här avsnittet och går till [Aktivera anpassad domän](#enable-custom-domain).
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Även om de olika domän leverantörernas information varierar kan du mappa *från* det anpassade domän namnet (t. ex. **contoso.com**) *till* det Traffic Manager domän namn (**contoso.trafficmanager.net**) som är integrerat med din app.

> [!NOTE]
> Om en post redan används och du behöver förebyggande syfte för att binda dina appar till den, kan du skapa ytterligare en CNAME-post. Om du till exempel vill förebyggande syfte bind **www\.contoso.com** till din app skapar du en CNAME-post från **awverify. www** till **contoso.trafficmanager.net**. Du kan sedan lägga till "www-\.contoso.com" i appen utan att behöva ändra "www" CNAME-posten. Mer information finns i [Migrera ett aktivt DNS-namn till Azure App Service](manage-custom-dns-migrate-domain.md).

När du har lagt till eller ändrat DNS-poster i din domän leverantör sparar du ändringarna.

## <a name="enable-custom-domain"></a>Aktivera anpassad domän
När posterna för ditt domän namn har spridits använder du webbläsaren för att kontrol lera att ditt anpassade domän namn matchar din App Service-app.

> [!NOTE]
> Det kan ta lite tid för din CNAME att spridas via DNS-systemet. Du kan använda en tjänst som <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> för att kontrol lera att CNAME är tillgängligt.
> 
> 

1. När domän upplösningen lyckades kan du gå tillbaka till din app-sida i [Azure-portalen](https://portal.azure.com)
2. I det vänstra navigerings fönstret väljer du **anpassade domäner** > **Lägg till värdnamn**.
4. Skriv det anpassade domän namnet som du mappade tidigare och välj **Verifiera**.
5. Se till att **post typen hostname** är inställd på **CNAME (www\.example.com eller någon under domän)** .

6. Eftersom App Service-appen nu är integrerad med en Traffic Manager-slutpunkt bör du se Traffic Manager domän namnet under **CNAME-konfiguration**. Markera den och klicka på **Lägg till anpassad domän**.

    ![Lägg till DNS-namnet i appen](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md)
