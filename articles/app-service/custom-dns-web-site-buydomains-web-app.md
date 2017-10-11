---
title: "Köp ett anpassat domännamn för Azure-Webbappar"
description: "Lär dig hur du köper ett anpassat domännamn med en webbapp i Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.openlocfilehash: 3cb22b935624041ab51e64028a1b668fd694f9b5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="buy-a-custom-domain-name-for-azure-web-apps"></a>Köp ett anpassat domännamn för Azure-Webbappar

Apptjänst-domäner (förhandsversion) är toppnivådomäner som hanteras direkt i Azure. De gör det lättare att hantera anpassade domäner för [Azure Web Apps](app-service-web-overview.md). Den här kursen visar hur du köper en Apptjänst-domän och tilldela Azure Web Apps DNS-namn.

Den här artikeln är Azure App Service (Web Apps, API Apps, Mobilappar, Logic Apps). Azure Storage eller Azure VM finns [tilldela Apptjänst domän till Azure VM eller Azure Storage](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Cloud Services, se [konfigurera ett anpassat domännamn för en Azure-molntjänst](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Skapa en Apptjänst-app](/azure/app-service/), eller använda en app som du skapade för en annan kursen.

## <a name="prepare-the-app"></a>Förbered appen

Att använda anpassade domäner i Azure Web Apps ditt webbprograms [programtjänstplanen](https://azure.microsoft.com/pricing/details/app-service/) måste vara en betald nivå (**delade**, **grundläggande**, **Standard**, eller **Premium**). I det här steget kan se du till att webbappen är i det prisnivån.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna den [Azure-portalen](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigera till appen i Azure-portalen

I den vänstra menyn, Välj **Apptjänster**, och välj sedan namnet på appen.

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

Du ser sidan för hantering av Apptjänst-app.  

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfönstret på appsidan, bläddra till den **inställningar** avsnittet och väljer **skala upp (apptjänstplan)**.

![Skala-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är i den **lediga** nivå. Anpassad DNS stöds inte i den **lediga** nivå. 

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Om App Service-plan inte **lediga**Stäng den **Välj din prisnivå** sidan och gå vidare till [köpa domänen](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-plan

Välj någon av de icke kostnadsfria nivåerna (**delade**, **grundläggande**, **Standard**, eller **Premium**). 

Klicka på **Välj**.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

När du ser följande meddelande har åtgärden slutförts.

![Skala åtgärd-bekräftelse](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Köpa domänen

### <a name="sign-in-to-azure"></a>Logga in på Azure
Öppna den [Azure-portalen](https://portal.azure.com/) och logga in med ditt Azure-konto.

### <a name="launch-buy-domains"></a>Starta köp domäner
I den **Web Apps** klickar du på namnet på ditt webbprogram, Välj **inställningar**, och välj sedan **anpassade domäner**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

I den **anpassade domäner** klickar du på **köpa domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

### <a name="configure-the-domain-purchase"></a>Konfigurera domänen köpet

I den **Service tillämpningsdomän** sidan den **Sök efter domän** skriver domännamnet som du vill köpa och skriv `Enter`. Föreslagna tillgängliga domäner visas under textrutan. Välj en eller flera domäner som du vill köpa. 
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

Klicka på den **kontaktuppgifter** och fylla i domänens kontaktinformation formulär. När du är klar klickar du på **OK** att återgå till sidan App Service-domän.
   
Det är viktigt att du fyller i alla obligatoriska fält med så mycket noggrannhet som möjligt. Felaktiga data kontaktinformation kan resultera i misslyckande med att köpa domäner. 

Välj därefter önskade alternativ för din domän. Se följande tabell förklaringar:

| Inställning | Föreslaget värde | Beskrivning |
|-|-|-|
|Förnya automatiskt | **Aktivera** | Förnyar din App Service domän automatiskt varje år. Ditt kreditkort debiteras samma inköpspriset vid tidpunkten för förnyelsen. |
|Sekretesskydd | Aktivera | Välja att ”sekretesskydd”, som ingår i inköpspriset _gratis_ (utom toppnivådomäner vars register inte stöder sekretesskydd, t.ex _. co.in_, _. co.uk_och så vidare). |
| Tilldela standard värdnamn | **www** och**@** | Välj önskad värdnamnsbindningar om så önskas. När domänen köp åtgärden är slutförd måste kan ditt webbprogram nås på den valda värdnamnen. Om webbappen bakom [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), visas alternativet att tilldela rotdomänen (@), eftersom Traffic Manager har inte stöd för A-poster. Du kan ändra värdnamnet tilldelningar efter domän köpet har slutförts. |

### <a name="accept-terms-and-purchase"></a>Acceptera villkoren och köpa

Klicka på **juridiska villkor** att granska villkoren och kostnader och klicka sedan på **köpa**.

> [!NOTE]
> Apptjänst domäner använda DNS för Azure som värd för domänerna. Förutom domän registreringsavgift gäller användningskostnader för Azure DNS. Mer information finns i [priser för Azure DNS](https://azure.microsoft.com/pricing/details/dns/).
>
>

I den **Service tillämpningsdomän** klickar du på **OK**. Medan åtgärden pågår kan se du följande meddelanden:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testa värdnamnen

Om du har tilldelat ditt webbprogram standard värdnamn se du också en lyckad avisering för varje vald värdnamn. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Du också se valda värdnamn i den **anpassade domäner** sidan den **värdnamn** avsnitt. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Gå till listan värdnamn i webbläsaren om du vill testa värdnamnen. I exemplet i den föregående skärmbilden försök gå till _kontoso.net_ och _www.kontoso.net_.

## <a name="assign-hostnames-to-web-app"></a>Tilldela webbprogrammet värdnamn

Om du inte väljer att tilldela en eller flera standard värdnamn till ditt webbprogram under inköpsprocessen, eller om måste du tilldela ett värdnamn som inte finns, kan du tilldela ett värdnamn på när som helst.

Du kan också tilldela värdnamn i programdomänen för tjänsten till ett annat webbprogram. Stegen är beroende av om App Service-domänen och webbprogrammet tillhöra samma prenumeration.

- Annan prenumeration: mappa anpassade DNS-poster från domänen App Service till webbprogrammet som en externt köpta domän. Mer information om att lägga till anpassade DNS-namn till en App Service-domän finns [hantera anpassade DNS-poster](#custom). Om du vill mappa en extern köpta domän till en webbapp, se [mappa ett befintligt anpassade DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md). 
- Samma prenumeration: Använd följande steg.

### <a name="launch-add-hostname"></a>Starta Lägg till värdnamn
I den **Apptjänster** markerar du namnet på ditt webbprogram som du vill tilldela värdnamn, Välj **inställningar**, och välj sedan **anpassade domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Kontrollera att dina köpta domän visas i den **App Service domäner** avsnittet, men inte välja den. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Alla App Service-domäner i samma prenumeration som visas i webbappens **anpassade domäner** sidan. Om din domän finns i webbappens prenumeration, men du kan se den i webbapp **anpassade domäner** och prova att öppna den **anpassade domäner** sidan eller uppdatera webbsida. Kontrollera också notification bell överst i Azure-portalen för pågår eller skapa fel.
>
>

Välj **lägga till värdnamnet**.

### <a name="configure-hostname"></a>Konfigurera värdnamnet
I den **lägga till värdnamnet** dialogrutan Skriv fullständigt kvalificerade domännamnet för din App Service-domän eller en underdomän. Exempel:

- kontoso.NET
- www.kontoso.NET
- ABC.kontoso.NET

När du är klar väljer **verifiera**. Hostname-posttypen väljs automatiskt för dig.

Välj **lägga till värdnamnet**.

När åtgärden är klar, kan du se ett fungerande meddelande för tilldelade värdnamnet.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Stäng lägga till värdnamnet
I den **lägga till värdnamnet** anger andra värdnamnet för ditt webbprogram efter behov. När du är klar stänger du den **lägga till värdnamnet** sidan.

Du bör nu se nytilldelade hostname(s) i din app **anpassade domäner** sidan.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testa värdnamnen

Gå till listan värdnamn i webbläsaren. I exemplet i den föregående skärmbilden försök gå till _abc.kontoso.net_.

<a name="custom" />

## <a name="manage-custom-dns-records"></a>Hantera anpassade DNS-poster

I Azure, DNS-poster för en tillämpningsdomän Service hanteras med hjälp av [Azure DNS](https://azure.microsoft.com/services/dns/). Du kan lägga till, ta bort, och uppdatera DNS-poster, precis som för en externt köpta domän.

### <a name="open-app-service-domain"></a>Öppna App Service-domän

Välj i Azure-portalen från den vänstra menyn **fler tjänster** > **App Service domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Välj domänen som ska hantera. 

### <a name="access-dns-zone"></a>Åtkomst till DNS-zonen

Välj domänens vänstra menyn **DNS-zonen**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Den här åtgärden öppnar den [DNS-zonen](../dns/dns-zones-records.md) sidan i din App Service-domän i Azure DNS. Information om hur du redigerar DNS-poster finns [hur du hanterar DNS-zoner i Azure portal](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Avbryt inköp (ta bort domän)

När du har köpt tjänsten tillämpningsdomänen har fem dagar att häva köpet för en full återbetalning. Efter fem dagar du kan ta bort domänen App Service, men det går inte att få en återbetalning.

### <a name="open-app-service-domain"></a>Öppna App Service-domän

Välj i Azure-portalen från den vänstra menyn **fler tjänster** > **App Service domäner**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Välj domänen du vill avbryta eller ta bort. 

### <a name="delete-hostname-bindings"></a>Ta bort värdnamnsbindningar

Välj domänens vänstra menyn **värdnamnsbindningar**. Värdnamnsbindningar från alla Azure-tjänster i den här listan.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Du kan inte ta bort tjänsten tillämpningsdomän förrän alla värdnamnsbindningar har tagits bort.

Ta bort varje värdnamn bindning genom att välja **...**   >  **Ta bort**. När alla bindningar tas bort, Välj **spara**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Avbryt eller ta bort

Välj domänens vänstra menyn **översikt**. 

Om den annullering på domänen köpta inte har gått, väljer **Avbryt inköp**. Annars kan du se en **ta bort** knappen i stället. Om du vill ta bort domänen utan bidrag, Välj **ta bort**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Välj **OK** att bekräfta åtgärden. Om du inte vill fortsätta klickar du utanför bekräftelsedialogrutan.

När åtgärden är klar, är domänen utgivna från prenumerationen och är tillgängliga för alla att köpa igen. 
