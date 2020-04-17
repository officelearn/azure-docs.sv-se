---
title: Köpa ett eget domännamn
description: Lär dig hur du köper en App Service-domän och använder den som en anpassad domän för din app Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 47daf4ecd034c390a1460610e78d4fffd9277ac7
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535714"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Köpa ett anpassat domännamn för Azure App Service

App Service-domäner är toppdomäner som hanteras direkt i Azure. De gör det enkelt att hantera anpassade domäner för [Azure App Service](overview.md). Den här självstudien visar hur du köper en App Service-domän och tilldelar DNS-namn till Azure App Service.

För Azure VM eller Azure Storage finns i [Tilldela App Service-domän till Azure VM eller Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). För Molntjänster finns [i Konfigurera ett anpassat domännamn för en Azure-molntjänst](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Skapa en App Service-app](/azure/app-service/), eller använd en app som du har skapat för en annan kurs.
* [Ta bort utgiftsgränsen för din prenumeration](../cost-management-billing/manage/spending-limit.md#remove). Du kan inte köpa App Service-domäner med kostnadsfria prenumerationskrediter.

## <a name="prepare-the-app"></a>Förbereda appen

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Om du vill använda anpassade domäner i Azure App Service måste appens [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) vara en betald nivå (**Delad,** **Grundläggande,** **Standard**eller **Premium**). I det här steget kontrollerar du att appen finns på prisnivån som stöds.

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

Om App Service-planen inte finns på **F1-nivån** stänger du sidan **Skala upp** och hoppar till [Köp domänen](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Använd**.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

När du ser följande meddelande har skalningsåtgärden slutförts.

![Bekräftelse av skalningsåtgärd](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Köp domänen

### <a name="pricing-information"></a>Information om priser
Prisinformation om Azure App Service Domains finns på [sidan Priser för App-tjänst](https://azure.microsoft.com/pricing/details/app-service/windows/) och bläddrar ned till App Service Domain.

### <a name="sign-in-to-azure"></a>Logga in på Azure
Öppna [Azure Portal](https://portal.azure.com/) och logga in med ditt Azure-konto.

### <a name="launch-buy-domains"></a>Starta köpdomäner
Klicka på namnet på appen på fliken **Apptjänster,** välj **Inställningar**och välj sedan **Anpassade domäner**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Klicka på **Köp domän**på sidan **Anpassade domäner.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Om du inte kan se avsnittet **App Service Domains** måste du ta bort utgiftsgränsen för ditt Azure-konto (se [Förutsättningar](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurera domänköpet

Skriv det domännamn du vill köpa och skriv `Enter`i rutan Sök efter domän i rutan Sök efter **domän.** **App Service Domain** De föreslagna tillgängliga domänerna visas precis under textrutan. Välj en eller flera domäner som du vill köpa.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Följande [toppdomäner](https://wikipedia.org/wiki/Top-level_domain) stöds av App Service-domäner: _com_, _net_, _co.uk_, _org_, _nl_, _i_, _biz_, _org.uk_och _co.in_.
>
>

Klicka på **kontaktinformationen** och fyll i domänens kontaktinformationsformulär. När du är klar klickar du på **OK** för att återgå till sidan Domän för apptjänst.

Det är viktigt att du fyller i alla obligatoriska fält med så mycket noggrannhet som möjligt. Felaktiga data för kontaktinformation kan leda till att domäner inte kan köpas.

Välj sedan önskade alternativ för din domän. Se följande tabell för förklaringar:

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|Sekretesskydd | Aktivera | Anmäl dig till "Sekretessskydd", som ingår i köpeskillingen _gratis._ Vissa toppdomäner hanteras av registratorer som inte stöder integritetsskydd, och de finns listade på sidan **Sekretessskydd.** |
| Tilldela standardvärdnamn | **www** och**\@** | Välj önskade värdnamnsbindningar om så önskas. När domänköpsåtgärden är klar kan appen nås på de valda värdnamnen. Om appen ligger bakom [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)visas inte alternativet att tilldela rotdomänen (@), eftersom Traffic Manager inte stöder A-poster. Du kan göra ändringar i värdnamnstilldelningarna när domänköpet har slutförts. |

### <a name="accept-terms-and-purchase"></a>Acceptera villkor och köp

Klicka på **Juridiska villkor** om du vill granska villkoren och avgifterna och klicka sedan på **Köp**.

> [!NOTE]
> App Service Domains använder GoDaddy för domänregistrering och Azure DNS för att vara värd för domänerna. Utöver domänregistreringsavgiften gäller användningsavgifter för Azure DNS. Mer information finns i [Azure DNS Pricing](https://azure.microsoft.com/pricing/details/dns/).
>
>

Klicka på **OK**på sidan Domän för **apptjänst.** Medan åtgärden pågår visas följande meddelanden:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testa värdnamnen

Om du har tilldelat standardvärdnamn till din app visas också ett meddelande om lyckade försök för varje valt värdnamn.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Du ser också de valda värdnamnen på sidan **Anpassade domäner** i avsnittet **Anpassade värdnamn.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> En **inte säker** etikett för din anpassade domän innebär att den ännu inte är bunden till ett TLS/SSL-certifikat, och alla HTTPS-begäranden från en webbläsare till din anpassade domän får ett felmeddelande eller en varning, beroende på webbläsaren. Information om hur du konfigurerar TLS-bindning finns i [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).
>

Om du vill testa värdnamnen navigerar du till de listade värdnamnen i webbläsaren. I exemplet i föregående skärmbild kan du prova att navigera till _kontoso.net_ och _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Tilldela värdnamn till appen

Om du väljer att inte tilldela ett eller flera standardvärdnamn till din app under inköpsprocessen, eller om du behöver tilldela ett värdnamn som inte finns med i listan, kan du tilldela ett värdnamn när som helst.

Du kan också tilldela värdnamn i App Service-domänen till alla andra appar. Stegen beror på om App Service Domain och appen tillhör samma prenumeration.

- Annan prenumeration: Mappa anpassade DNS-poster från App Service Domain till appen som en externt köpt domän. Information om hur du lägger till anpassade DNS-namn i en App Service-domän finns i [Hantera anpassade DNS-poster](#custom). Information om hur du mappar en extern köpt domän till en app finns i [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Samma prenumeration: Använd följande steg.

### <a name="launch-add-hostname"></a>Starta lägg till värdnamn
På sidan **App Services** väljer du namnet på den app som du vill tilldela värdnamn till, väljer **Inställningar**och väljer sedan **Anpassade domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Kontrollera att den köpta domänen finns med i avsnittet **App Service Domains,** men markera den inte. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Alla Apptjänstdomäner i samma prenumeration visas på appens **sida Anpassade domäner.** Om din domän finns i appens prenumeration, men du inte kan se den på appens sida **Anpassade domäner,** kan du prova att öppna sidan **Anpassade domäner** igen eller uppdatera webbsidan. Kontrollera också meddelandeklockan högst upp i Azure-portalen för att skapa fel eller skapa fel.
>
>

Välj **Lägg till värddatornamn**.

### <a name="configure-hostname"></a>Konfigurera värdnamn
I dialogrutan **Lägg till värdnamn** skriver du det fullständigt kvalificerade domännamnet för apptjänstdomänen eller någon underdomän. Ett exempel:

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

När du är klar väljer du **Validera**. Posttypen värdnamn väljs automatiskt åt dig.

Välj **Lägg till värddatornamn**.

När åtgärden är klar visas ett meddelande om lyckade värden för det tilldelade värdnamnet.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Stäng lägg till värdnamn
På sidan **Lägg till värdnamn** tilldelar du alla andra värdnamn till din app, efter behov. När du är klar stänger du sidan **Lägg till värdnamn.**

Nu bör du se de nyligen tilldelade värdnamnen på appens **sida Anpassade domäner.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testa värdnamnen

Navigera till de listade värdnamnen i webbläsaren. I exemplet i föregående skärmbild kan du prova att navigera till _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Förnya domänen

Apptjänstdomänen du köpte är giltig i ett år från inköpstillfället. Som standard är domänen konfigurerad för att förnyas automatiskt genom att debitera din betalningsmetod för nästa år. Du kan förnya domännamnet manuellt.

Om du vill inaktivera automatisk förnyelse, eller om du vill förnya domänen manuellt, följer du stegen här.

Klicka på namnet på appen på fliken **Apptjänster,** välj **Inställningar**och välj sedan **Anpassade domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Välj den domän som du vill konfigurera i avsnittet **Apptjänstdomäner.**

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Välj **Domänförnyelse**från den vänstra navigeringen i domänen . Om du vill sluta förnya domänen automatiskt väljer du **Av**och sedan **spara**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Om du vill förnya domänen manuellt väljer du **Förnya domän**. Den här knappen är dock inte aktiv förrän [90 dagar innan domänen upphör att gälla.](#when-domain-expires)

Om domänförnyelsen lyckas får du ett e-postmeddelande inom 24 timmar.

## <a name="when-domain-expires"></a>När domänen upphör att gälla

Azure hanterar utgående eller utgångna App Service-domäner enligt följande:

* Om automatisk förnyelse är inaktiverat: 90 dagar innan domänen upphör att gälla skickas ett e-postmeddelande för förnyelse till dig och knappen **Förnya domän** aktiveras i portalen.
* Om automatisk förnyelse är aktiverat: Dagen efter domänens utgångsdatum försöker Azure fakturera dig för förnyelse av domännamnet.
* Om ett fel uppstår under automatisk förnyelse (till exempel har ditt registrerade kort upphört att gälla), eller om automatisk förnyelse är inaktiverat och du tillåter att domänen upphör att gälla, meddelar Azure dig om domänens förfallodatum och parkerar ditt domännamn. Du kan förnya domänen [manuellt.](#renew-the-domain)
* Den 4: e och 12: e dagen efter utgångsdatum, Skickar Azure dig ytterligare e-postmeddelanden. Du kan förnya domänen [manuellt.](#renew-the-domain)
* Den 19:e dagen efter utgångsdatumet är din domän fortfarande spärrad men blir föremål för en inlösenavgift. Du kan ringa kundsupport för att förnya ditt domännamn, med förbehåll för eventuella tillämpliga förnyelse- och inlösenavgifter.
* Den 25:e dagen efter utgångsdatumet lägger Azure din domän på auktion med en domännamnsindustriauktionstjänst. Du kan ringa kundsupport för att förnya ditt domännamn, med förbehåll för eventuella tillämpliga förnyelse- och inlösenavgifter.
* Den 30:e dagen efter utgångsdatumet kan du inte längre lösa in domänen.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Hantera anpassade DNS-poster

I Azure hanteras DNS-poster för en App Service-domän med [Azure DNS](https://azure.microsoft.com/services/dns/). Du kan lägga till, ta bort och uppdatera DNS-poster, precis som för en externt köpt domän.

### <a name="open-app-service-domain"></a>Öppna apptjänstdomän

Välj **Alla tjänster** > **App Service Domains**på Azure-portalen på den vänstra menyn .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Välj den domän som ska hanteras. 

### <a name="access-dns-zone"></a>Access DNS-zon

Välj **DNS-zon**på domänens vänstra meny .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Den här åtgärden öppnar [DNS-zonsidan](../dns/dns-zones-records.md) för din App Service-domän i Azure DNS. Information om hur du redigerar DNS-poster finns [i Så här hanterar du DNS-zoner i Azure-portalen](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Avbryt köp (ta bort domän)

När du har köpt App Service Domain har du fem dagar på dig att avbryta ditt köp för full återbetalning. Efter fem dagar kan du ta bort App Service Domain, men kan inte få en återbetalning.

### <a name="open-app-service-domain"></a>Öppna apptjänstdomän

Välj **Alla tjänster** > **App Service Domains**på Azure-portalen på den vänstra menyn .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Markera den domän som du vill avbryta eller ta bort. 

### <a name="delete-hostname-bindings"></a>Ta bort värdnamnsbindningar

På domänens vänstra meny väljer du **Värdnamnsbindningar**. Värdnamnsbindningarna från alla Azure-tjänster visas här.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Du kan inte ta bort App Service Domain förrän alla värdnamnsbindningar har tagits bort.

Ta bort varje värdnamnsbindning genom att välja **...**  >  **Ta bort**. När alla bindningar har tagits bort väljer du **Spara**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Avbryta eller ta bort

Välj **Översikt**på domänens vänstra meny . 

Om uppsägningsperioden på den köpta domänen inte har förflutit väljer du **Avbryt inköp**. Annars visas knappen **Ta bort** i stället. Om du vill ta bort domänen utan återbetalning väljer du **Ta bort**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Om du vill bekräfta åtgärden väljer du **Ja**.

När åtgärden är klar släpps domänen från din prenumeration och är tillgänglig för alla att köpa igen. 

## <a name="direct-default-url-to-a-custom-directory"></a>Dirigera standard-URL:en till en anpassad katalog

Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Om du vill dirigera dem till `public`en underkatalog, till exempel , finns [i Direkt standard-URL till en anpassad katalog](app-service-web-tutorial-custom-domain.md#virtualdir).
