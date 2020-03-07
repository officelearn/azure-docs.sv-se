---
title: Köp ett anpassat domän namn
description: Lär dig hur du köper en App Service domän och använder den som en anpassad domän för din app Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: afb40d0f3681bc02351e43166fccfaafe7741128
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375186"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Köp ett anpassat domän namn för Azure App Service

App Service domäner är toppnivå domäner som hanteras direkt i Azure. De gör det enkelt att hantera anpassade domäner för [Azure App Service](overview.md). Den här självstudien visar hur du köper en App Service domän och tilldelar DNS-namn till Azure App Service.

Information om virtuella Azure-datorer eller Azure Storage finns i [tilldela App Service domän till Azure VM eller Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage). Mer Cloud Services finns i [Konfigurera ett anpassat domän namn för en Azure-moln tjänst](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Skapa en App Service-app](/azure/app-service/), eller använd en app som du har skapat för en annan kurs.
* [Ta bort utgifts gränsen för din prenumeration](../cost-management-billing/manage/spending-limit.md#remove). Du kan inte köpa App Service domäner med kostnads fria prenumerations krediter.

## <a name="prepare-the-app"></a>Förbereda appen

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Om du vill använda anpassade domäner i Azure App Service måste appens [App Service plan](https://azure.microsoft.com/pricing/details/app-service/) vara en betald nivå (**delad**, **Basic**, **standard**eller **Premium**). I det här steget ser du till att appen är i den pris nivå som stöds.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigera till appen i Azure Portal

Välj **App Services** på menyn till vänster och välj sedan appens namn.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

Du ser hanteringssidan för App Service-appen.  

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfältet på appsidan bläddrar du till avsnittet **Inställningar** och väljer **Skala upp (App Service-plan)** .

![Skala upp-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är på nivån **F1**. Anpassad DNS stöds inte på nivån **F1**. 

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Om App Service plan inte finns på **F1** -nivån stänger du sidan **skala upp** och fortsätter med att [köpa domänen](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Verkställ**.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

När du ser följande meddelande har skalningsåtgärden slutförts.

![Bekräftelse av skalningsåtgärd](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Köp domänen

### <a name="pricing-information"></a>Pris information
Information om priser för Azure App Service domäner finns på [sidan med App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/windows/) och rulla ned till App Service domän.

### <a name="sign-in-to-azure"></a>Logga in på Azure
Öppna [Azure Portal](https://portal.azure.com/) och logga in med ditt Azure-konto.

### <a name="launch-buy-domains"></a>Starta köp domäner
På fliken **app Services** klickar du på namnet på din app, väljer **Inställningar**och väljer sedan **anpassade domäner**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

På sidan **anpassade domäner** klickar du på **Köp domän**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Om du inte kan se avsnittet **App Service domäner** måste du ta bort utgifts gränsen för ditt Azure-konto (se [krav](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Konfigurera domän köpet

På sidan **App Service domän** , i rutan **Sök efter domän** , anger du det domän namn som du vill köpa och skriver `Enter`. De föreslagna tillgängliga domänerna visas strax under text rutan. Välj en eller flera domäner som du vill köpa.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Följande [toppnivå domäner](https://wikipedia.org/wiki/Top-level_domain) stöds av App Service domäner: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _BIZ_, _org.uk_och _co.in_.
>
>

Klicka på **kontakt information** och fyll i domänens formulär med kontakt information. När du är färdig klickar du på **OK** för att återgå till sidan App Service domän.

Det är viktigt att du fyller i alla obligatoriska fält med så mycket precision som möjligt. Felaktiga data för kontakt information kan leda till att domäner inte kan köpas.

Välj sedan önskade alternativ för din domän. I följande tabell finns förklaringar:

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|Sekretess skydd | Aktivera | Anmäl dig till "Sekretess skydd", som ingår i inköps priset _kostnads fritt_. Vissa domäner på den översta nivån hanteras av registratorer som inte har stöd för sekretess skydd och de visas på sidan för skydds **skydd** . |
| Tilldela standard värdnamn | **www** och **\@** | Välj önskade bindningar för värdnamn, om så önskas. När du har slutfört åtgärden för domänen kan din app nås på de valda värd namnen. Om appen är bakom [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)kan du inte se alternativet för att tilldela rot domänen (@), eftersom Traffic Manager inte stöder poster. Du kan ändra värdnamnnas tilldelningar när domänen har köpts. |

### <a name="accept-terms-and-purchase"></a>Godkänn villkor och Köp

Klicka på **juridiska villkor** för att granska villkoren och avgifterna och klicka sedan på **köp**.

> [!NOTE]
> App Service domäner använder GoDaddy för domän registrering och Azure DNS som värd för domänerna. Förutom domän registrerings avgiften gäller användnings kostnader för Azure DNS. Mer information finns i [Azure DNS prissättning](https://azure.microsoft.com/pricing/details/dns/).
>
>

Gå tillbaka till sidan **App Service domän** och klicka på **OK**. När åtgärden pågår visas följande meddelanden:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testa värd namnen

Om du har tilldelat standard värd namn till din app visas även ett meddelande om att det finns ett meddelande för varje valt värdnamn.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Du kan också se de valda värd namnen på sidan **anpassade domäner** i avsnittet **anpassade värdnamn** .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> En osäker etikett för din anpassade domän innebär att **den inte har** bundits till ett SSL-certifikat och att alla https-förfrågningar från en webbläsare till din anpassade domän får ett fel eller en varning, beroende på webbläsaren. Om du vill konfigurera SSL-bindning, se [skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md).
>

Om du vill testa värd namnen navigerar du till de listade värd namnen i webbläsaren. I exemplet i föregående skärm bild kan du försöka navigera till _kontoso.net_ och _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Tilldela värdnamn till appen

Om du väljer att inte tilldela en eller flera standard värd namn till din app under inköps processen, eller om du behöver tilldela ett värdnamn som inte finns med i listan, kan du tilldela ett värdnamn när som helst.

Du kan också tilldela värdnamn i App Service-domänen till alla andra appar. Stegen beror på om App Service-domänen och appen tillhör samma prenumeration.

- Annan prenumeration: mappa anpassade DNS-poster från App Service-domänen till appen som en externt köpt domän. Information om hur du lägger till anpassade DNS-namn i en App Service domän finns i [Hantera anpassade DNS-poster](#custom). Information om hur du mappar en extern, köpt domän till en app finns i [mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Samma prenumeration: Använd följande steg.

### <a name="launch-add-hostname"></a>Starta Lägg till värdnamn
På sidan **app Services** väljer du namnet på den app som du vill tilldela värdnamn till, väljer **Inställningar**och väljer sedan **anpassade domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Se till att den köpta domänen visas i avsnittet **App Service domäner** , men Markera den inte. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Alla App Service domäner i samma prenumeration visas på sidan **anpassade domäner** för appen. Om din domän finns i appens prenumeration, men du inte kan se den på sidan **anpassade domäner** , kan du försöka öppna sidan **anpassade domäner** igen eller uppdatera webb sidan. Kontrol lera också att meddelandets Bell visas överst i Azure Portal för förlopps-eller skapande problem.
>
>

Välj **Lägg till värddatornamn**.

### <a name="configure-hostname"></a>Konfigurera värdnamn
I dialog rutan **Lägg till värdnamn** anger du det fullständigt kvalificerade domän namnet för din app service domän eller under domän. Exempel:

- kontoso.net
- www-\.kontoso.net
- abc.kontoso.net

När du är klar väljer du **Verifiera**. Post typen hostname väljs automatiskt åt dig.

Välj **Lägg till värddatornamn**.

När åtgärden har slutförts visas ett meddelande för det tilldelade värd namnet.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Stäng Lägg till värdnamn
På sidan **Lägg till värdnamn** tilldelar du ett annat värdnamn till din app efter behov. När du är färdig stänger du sidan **Lägg till värdnamn** .

Nu bör du se de nyligen tilldelade värd namnen på sidan **anpassade domäner** i appen.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testa värd namnen

Navigera till de värdnamn som visas i webbläsaren. I exemplet i föregående skärm bild kan du försöka navigera till _ABC.kontoso.net_.

## <a name="renew-the-domain"></a>Förnya domänen

Den App Service domän som du köpte är giltig i ett år från tidpunkten för köpet. Som standard är domänen konfigurerad att förnya automatiskt genom att debitera din betalnings metod för nästa år. Du kan förnya ditt domän namn manuellt.

Om du vill inaktivera automatisk förnyelse, eller om du vill förnya din domän manuellt, följer du stegen här.

På fliken **app Services** klickar du på namnet på din app, väljer **Inställningar**och väljer sedan **anpassade domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

I avsnittet **App Service domäner** väljer du den domän som du vill konfigurera.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Välj **domän förnyelse**i den vänstra navigeringen i domänen. Om du vill sluta förnya din domän automatiskt väljer du **av**och sedan **Spara**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Om du vill förnya din domän manuellt väljer du **förnya domän**. Den här knappen är dock inte aktiv förrän [90 dagar innan domänen upphör att gälla](#when-domain-expires).

Om din domän förnyelse lyckas får du ett e-postmeddelande inom 24 timmar.

## <a name="when-domain-expires"></a>När domänen upphör att gälla

Azure följer förfaller eller förfaller App Service domäner på följande sätt:

* Om automatisk förnyelse är inaktive rad: 90 dagar innan domänen upphör skickas ett e-postmeddelande med en förnyad avisering till dig och knappen **förnya domän** aktive ras i portalen.
* Om automatisk förnyelse har Aktiver ATS: efter det att din domäns giltighets tid har gått ut försöker Azure debitera dig för förnyelsen av domän namn.
* Om ett fel inträffar under den automatiska förnyelsen (till exempel om ditt kort på filen har upphört att gälla), eller om automatisk förnyelse har inaktiverats och du tillåter att domänen upphör att gälla, meddelar Azure dig om domänen upphör att gälla och parkerar ditt domän namn. Du kan [förnya](#renew-the-domain) din domän manuellt.
* På dygnet runt och tolfte dagar efter förfallo datum skickar Azure ytterligare e-postmeddelanden. Du kan [förnya](#renew-the-domain) din domän manuellt.
* På den 19: a dagen efter förfallo datum förblir din domän stoppad men blir föremål för en avgift för inlösen. Du kan ringa kund support för att förnya ditt domän namn, beroende på eventuella tillämpliga förnyelse-och inlösen avgifter.
* På den 25: a dagen efter förfallo datum placerar Azure din domän för auktion med en auktions tjänst för domän namns branschen. Du kan ringa kund support för att förnya ditt domän namn, beroende på eventuella tillämpliga förnyelse-och inlösen avgifter.
* Den 30: e dagen efter det att du har gått ut kan du inte längre lösa in din domän.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Hantera anpassade DNS-poster

I Azure hanteras DNS-poster för en App Service domän med hjälp av [Azure DNS](https://azure.microsoft.com/services/dns/). Du kan lägga till, ta bort och uppdatera DNS-poster, precis som för en externt köpt domän.

### <a name="open-app-service-domain"></a>Öppna App Service domän

I Azure Portal väljer du **alla tjänster** > **App Service domäner**på den vänstra menyn.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Välj den domän som ska hanteras. 

### <a name="access-dns-zone"></a>Åtkomst till DNS-zon

I domänens vänstra meny väljer du **DNS-zon**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Den här åtgärden öppnar sidan [DNS-zon](../dns/dns-zones-records.md) för din app service domän i Azure DNS. Information om hur du redigerar DNS-poster finns i [hantera DNS-zoner i Azure Portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Avbryt köp (ta bort domän)

När du har köpt App Service-domänen har du fem dagar på dig att avbryta köpet för en fullständig åter betalning. Efter fem dagar kan du ta bort den App Service-domänen, men får ingen åter betalning.

### <a name="open-app-service-domain"></a>Öppna App Service domän

I Azure Portal väljer du **alla tjänster** > **App Service domäner**på den vänstra menyn.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Välj den domän som du vill avbryta eller ta bort. 

### <a name="delete-hostname-bindings"></a>Ta bort värdnamn bindningar

I domänens vänstra meny väljer du **hostname-bindningar**. Värd namns bindningarna från alla Azure-tjänster visas här.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Du kan inte ta bort den App Service domänen förrän alla bindnings bindningar har tagits bort.

Ta bort varje hostname-bindning genom att välja **...**  > **ta bort**. När alla bindningar har tagits bort väljer du **Spara**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Avbryt eller ta bort

I domänens vänstra meny väljer du **Översikt**. 

Om uppsägnings perioden på den köpta domänen inte har förflutit väljer du **Avbryt köp**. Annars visas knappen **ta bort** i stället. Om du vill ta bort domänen utan åter betalning väljer du **ta bort**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Bekräfta åtgärden genom att välja **Ja**.

När åtgärden har slutförts släpps domänen från din prenumeration och är tillgänglig för alla som köper igen. 

## <a name="direct-default-url-to-a-custom-directory"></a>Dirigera standard-URL:en till en anpassad katalog

Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Om du vill dirigera dem till en under katalog, till exempel `public`, se [direkt standard-URL till en anpassad katalog](app-service-web-tutorial-custom-domain.md#virtualdir).
