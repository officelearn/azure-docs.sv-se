---
title: Köp anpassade domännamn i Azure - App Service
description: Lär dig hur du köper ett anpassat domännamn med en webbapp i Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c59e892c8fd5a8bcc74d23e16eaabf1dc1a08f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121544"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Köp ett anpassat domännamn för Azure App Service

App Service-domäner (förhandsversion) är toppnivådomäner som hanteras direkt i Azure. De gör det enkelt att hantera anpassade domäner för [Azure App Service](overview.md). Den här självstudien visar hur du köper en App Service-domän och kopplar DNS-namn till Azure App Service.

Virtuell dator i Azure eller Azure Storage finns i [tilldela App Service-domän till Azure-VM eller Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Cloud Services, se [konfigurera ett anpassat domännamn för en Azure cloud Services](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

* [Skapa en App Service-app](/azure/app-service/), eller använd en app som du har skapat för en annan kurs.
* [Ta bort utgiftsgränsen för prenumerationen](../billing/billing-spending-limit.md#remove). Du kan inte köpa App Service-domäner med kostnadsfria krediter.

## <a name="prepare-the-app"></a>Förbereda appen

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Använda anpassade domäner i Azure App Service, din app [apptjänstplan](https://azure.microsoft.com/pricing/details/app-service/) måste vara en betald nivå (**delad**, **grundläggande**, **Standard**, eller  **Premium**). I det här steget ska se du till att appen är stöds prisnivå.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigera till appen i Azure Portal

Välj **App Services** på menyn till vänster och välj sedan appens namn.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

Du ser hanteringssidan för App Service-appen.  

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfältet på appsidan bläddrar du till avsnittet **Inställningar** och väljer **Skala upp (App Service-plan)**.

![Skala upp-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är på nivån **F1**. Anpassad DNS stöds inte på nivån **F1**. 

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Om App Service-planen inte är i den **F1** tier, Stäng den **skala upp** och hoppar till [köper domänen](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Verkställ**.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

När du ser följande meddelande har skalningsåtgärden slutförts.

![Bekräftelse av skalningsåtgärd](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Köp domänen

### <a name="pricing-information"></a>Prisinformation
Information på Azure App Service-domäner om prissättning finns på den [prissättning för App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) och rullar ned till App Service-domänen.

### <a name="sign-in-to-azure"></a>Logga in på Azure
Öppna [Azure Portal](https://portal.azure.com/) och logga in med ditt Azure-konto.

### <a name="launch-buy-domains"></a>Starta köp domäner
I den **Apptjänster** klickar du på namnet på din app, väljer **inställningar**, och välj sedan **anpassade domäner**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

I den **anpassade domäner** klickar du på **köp domän**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Om du inte ser den **App Service-domäner** avsnittet som du vill ta bort utgiftsgränsen för ditt Azure-konto (se [krav](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurera domän-köp

I den **App Service-domänen** sidan den **Sök domän** skriver du domännamnet som du vill köpa och ange `Enter`. Föreslagna tillgängliga domäner visas nedanför textrutan. Välj en eller flera domäner som du vill köpa.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Följande [toppnivådomäner](https://wikipedia.org/wiki/Top-level_domain) stöds av App Service-domäner: _com_, _net_, _co.uk_, _org_, _nl_, _i_, _biz_, _org.se_, och _co.in_.
>
>

Klicka på den **kontaktuppgifter** och fylla i formuläret för domänens kontaktinformation. När du är klar klickar du på **OK** att återgå till sidan för App Service-domänen.

Det är viktigt att du fyller i alla obligatoriska fält med så mycket noggrannhet som möjligt. Det gick inte att köpa domäner kan leda till felaktiga data kontaktinformation.

Välj sedan önskat alternativ för din domän. Se tabellen nedan förklaringar:

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|Sekretesskydd | Aktivera | Välj att ”sekretesskydd”, som ingår i priset och köp _kostnadsfritt_. Vissa toppnivådomäner som hanteras av registratorer som inte stöder sekretesskydd och de visas på den **sekretesskydd** sidan. |
| Tilldela standard värdnamn | **www** och **\@** | Välj önskade värdnamnet-bindningar, om så önskas. Din app kan nås på den valda värdnamnen när domänen köp åtgärden är klar. Om appen är bakom [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), ser du alternativet för att tilldela rotdomänen (@), eftersom Traffic Manager har inte stöd för A-poster. Du kan ändra värdnamnet tilldelningar efter köp domän har slutförts. |

### <a name="accept-terms-and-purchase"></a>Acceptera villkoren och köp

Klicka på **juridiska villkor** att granska villkoren och kostnaderna och klicka sedan på **köpa**.

> [!NOTE]
> App Service-domäner kan du använda GoDaddy för domänregistrering och Azure DNS som värd för domäner. Förutom avgiften domän registrering gäller avgifter för användning för Azure DNS. Mer information finns i [priser för Azure DNS](https://azure.microsoft.com/pricing/details/dns/).
>
>

I den **App Service-domänen** klickar du på **OK**. Medan åtgärden pågår kan se du följande meddelanden:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testa värdnamnen

Om du har tilldelat standard värdnamn till din app kan se du även ett meddelande för lyckade för varje valda värddatornamnet. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Du kan också se den valda värdnamnen i den **anpassade domäner** sidan den **anpassade värdnamn** avsnittet. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Om du vill testa värdnamnen, navigerar du till listan värdnamn i webbläsaren. I exemplet i föregående skärmbild, försök att gå till _kontoso.net_ och _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Tilldela värdnamn till appen

Om du väljer att inte tilldela en eller flera standard-värdnamn till appen under inköpsprocessen, eller om du måste tilldela ett värdnamn som inte visas, kan du tilldela ett värdnamn på när som helst.

Du kan också tilldela värdnamn i App Service-domänen till andra appar. Stegen är beroende av om App Service-domänen och appen hör till samma prenumeration.

- Annan prenumeration: Kartan anpassade DNS-poster från App Service-domänen till appen som en externt köpta domän. Information om att lägga till anpassade DNS-namn till en App Service-domän finns i [hantera anpassade DNS-poster](#custom). Om du vill mappa en extern köpta domän till en app, se [mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Samma prenumeration: Använd följande steg.

### <a name="launch-add-hostname"></a>Starta Lägg till värddatornamn
I den **Apptjänster** markerar du namnet på din app som du vill tilldela värdnamn, Välj **inställningar**, och välj sedan **anpassade domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Se till att dina köpta domän finns med i den **App Service-domäner** avsnittet, men inte välja den. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Alla App Service-domäner i samma prenumeration visas i appens **anpassade domäner** sidan. Om din domän är i appens prenumeration, men du kan se den i appens **anpassade domäner** kan prova att öppna den **anpassade domäner** sidan eller uppdatera webbsidan. Kontrollera också meddelandeikonen överst i Azure portal för pågår eller skapa fel.
>
>

Välj **Lägg till värddatornamn**.

### <a name="configure-hostname"></a>Konfigurera värdnamn
I den **Lägg till värddatornamn** dialogrutan Skriv det fullständigt kvalificerade domännamnet för din App Service-domänen eller en underdomän. Exempel:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

När du är klar väljer **verifiera**. Posttyp för värddatornamn väljs automatiskt åt dig.

Välj **Lägg till värddatornamn**.

När åtgärden har slutförts visas ett meddelande för lyckade för tilldelade värdnamnet.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Stäng Lägg till värddatornamn
I den **Lägg till värddatornamn** kan tilldela andra värdnamn till appen, som du vill. När du är klar stänger den **Lägg till värddatornamn** sidan.

Du bör nu se de nyligen tilldelade hostname(s) i din app **anpassade domäner** sidan.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testa värdnamnen

Gå till listan värdnamn i webbläsaren. I exemplet i föregående skärmbild, försök att gå till _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Förnya domänen

App Service-domän som du har köpt är giltig i ett år från inköpstillfället. Som standard konfigureras domänen om du vill förnya automatiskt genom att debitera din betalningsmetod för nästa år. Om du vill inaktivera automatisk förnyelse, eller om du vill manuellt förnya din domän, följa de här stegen.

I den **Apptjänster** klickar du på namnet på din app, väljer **inställningar**, och välj sedan **anpassade domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

I den **App Service-domäner** väljer du den domän du vill konfigurera.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

I det vänstra navigeringsfönstret på domänen, Välj **domänförnyelse**. Om du vill stoppa automatiskt förnya din domän, Välj **av**, och sedan **spara**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Om du vill manuellt förnya din domän, Välj **förnya domän**. Men är den här knappen inte aktiv förrän 90 dagar före domänens förfallodatum.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Hantera anpassade DNS-poster

I Azure DNS-poster för en App Service-domänen hanteras med hjälp av [Azure DNS](https://azure.microsoft.com/services/dns/). Du kan lägga till, ta bort, och uppdatera DNS-poster, precis som för en externt köpta domän.

### <a name="open-app-service-domain"></a>Öppna App Service-domän

Azure-portalen från den vänstra menyn, Välj **alla tjänster** > **App Service-domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Välj domänen som ska hantera. 

### <a name="access-dns-zone"></a>Åtkomst till DNS-zon

I vänstermenyn för domänens väljer **DNS-zon**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Den här åtgärden öppnar den [DNS-zon](../dns/dns-zones-records.md) sidan i din App Service-domänen i Azure DNS. Information om hur du redigerar DNS-poster finns i [hur du hanterar DNS-zoner i Azure-portalen](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Avbryta köpet (ta bort domän)

När du har köpt App Service-domänen har fem dagar att avbryta köpet återförsäljaren. Efter fem dagar, du kan ta bort App Service-domänen, men det går inte att få en återbetalning.

### <a name="open-app-service-domain"></a>Öppna App Service-domän

Azure-portalen från den vänstra menyn, Välj **alla tjänster** > **App Service-domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Välj domänen som ska du vill avbryta eller ta bort. 

### <a name="delete-hostname-bindings"></a>Ta bort värddatornamnsbindningar

I vänstermenyn för domänens väljer **värdnamnsbindningar**. Värdnamnsbindningar från alla Azure-tjänster visas här.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Du kan inte ta bort App Service-domänen förrän alla värdnamnsbindningar har tagits bort.

Ta bort varje bindning för värddatornamn genom att välja **...**   >  **Ta bort**. När alla bindningar tas bort, väljer **spara**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Avbryt- eller delete

I vänstermenyn för domänens väljer **översikt**. 

Om annullering perioden för den köpta domänen inte har gått ut, väljer **avbryta köpet**. Annars ser du en **ta bort** knappen i stället. Om du vill ta bort domänen utan en återbetalning, Välj **ta bort**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

För att bekräfta åtgärden Välj **Ja**.

När åtgärden har slutförts, är domänen utgivna från din prenumeration och är tillgängliga för alla köp igen. 

## <a name="direct-default-url-to-a-custom-directory"></a>Dirigera standard-URL:en till en anpassad katalog

Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Att dirigera dem till en underkatalog, till exempel `public`, se [dirigera standard-URL till en anpassad katalog](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>Fler resurser

[VANLIGA FRÅGOR OCH SVAR: App Service-domänen (förhandsversion) och anpassade domäner](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)
