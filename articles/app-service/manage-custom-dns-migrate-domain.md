---
title: Migrera ett aktivt DNS-namn
description: Lär dig hur du migrerar ett anpassat DNS-domännamn som redan har tilldelats en live-webbplats till Azure App Service utan avbrott.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 79bd0a19a9bd8ebd100ed80ca0206656d73ef76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672362"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrera ett aktivt DNS-namn till Azure App Service

Den här artikeln visar hur du migrerar ett aktivt DNS-namn till [Azure App Service](../app-service/overview.md) utan avbrott.

När du migrerar en live-webbplats och dess DNS-domännamn till App Service betjänar det DNS-namnet redan direktsänd trafik. Du kan undvika driftstopp i DNS-upplösningen under migreringen genom att binda det aktiva DNS-namnet till apptjänstappen i förebyggande syfte.

Om du inte är orolig för driftstopp i DNS-upplösning läser du [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Krav

Så här slutför du den här in-

- [Kontrollera att apptjänstappen inte är på den kostnadsfria nivån](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Bind domännamnet förebyggande

När du binder en anpassad domän i förebyggande syfte utför du båda följande innan du gör några ändringar i DNS-posterna:

- Verifiera domänägarskap
- Aktivera appens domännamn

När du slutligen migrerar ditt anpassade DNS-namn från den gamla webbplatsen till App Service-appen blir det inga driftstopp i DNS-upplösningen.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Skapa domänverifieringspost

Om du vill verifiera domänägarskapet lägger du till en TXT-post. TXT-skivan kartlägger från _awverify.&lt; underdomän>_ till _ &lt;appnamn>.azurewebsites.net_. 

Den TXT-post du behöver beror på vilken DNS-post du vill migrera. Exempel på följande tabell`@` ( representerar vanligtvis rotdomänen):

| Exempel på DNS-post | TXT-värd | TXT-värde |
| - | - | - |
| \@-Jag är inte så bra som jag vet. | _awverify (awverify)_ | _&lt;appnamn>.azurewebsites.net_ |
| www (sub) | _awverify.www_ | _&lt;appnamn>.azurewebsites.net_ |
| \*(jokertecken) | _awverify.\*_ | _&lt;appnamn>.azurewebsites.net_ |

På sidan DNS-poster lägger du till posttypen för det DNS-namn som du vill migrera. App Service stöder mappningar från CNAME- och A-poster.

> [!NOTE]
> För vissa leverantörer, till exempel `awverify.*` CloudFlare, är inte en giltig post. Använd `*` bara i stället.

> [!NOTE]
> Jokerteckenposter `*` validerar inte underdomäner med en befintlig CNAME-post. Du kan behöva uttryckligen skapa en TXT-post för varje underdomän.


### <a name="enable-the-domain-for-your-app"></a>Aktivera domänen för din app

I [Azure-portalen](https://portal.azure.com)väljer du **Anpassade domäner**i den vänstra navigeringen på appsidan . 

![Meny för anpassad domän](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

På sidan **Anpassade domäner** **+** väljer du ikonen bredvid **Lägg till värdnamn**.

![Lägg till värddatornamn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Skriv det fullständigt kvalificerade domännamnet som du lade till `www.contoso.com`TXT-posten för, till exempel . För en jokerteckendomän (t.contoso.com) \*kan du använda alla DNS-namn som matchar jokerteckendomänen. 

Välj **Verifiera**.

Knappen **Lägg till värddatornamn** aktiveras. 

Kontrollera att **posttypen Hostname** är inställd på den DNS-posttyp som du vill migrera.

Välj **Lägg till värddatornamn**.

![Lägg till DNS-namnet i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Det kan ta en stund innan det nya värdnamnet återspeglas på sidan **Anpassade domäner** för appen. Försök att uppdatera webbläsaren så att informationen uppdateras.

![CNAME-posten har lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Ditt anpassade DNS-namn är nu aktiverat i din Azure-app. 

## <a name="remap-the-active-dns-name"></a>Mappa om det aktiva DNS-namnet

Det enda som återstår att göra är att mappa om din aktiva DNS-post så att den pekar på App Service. Just nu pekar det fortfarande på din gamla webbplats.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Kopiera appens IP-adress (endast en post)

Om du mappar om en CNAME-post hoppar du över det här avsnittet. 

Om du vill mappa om en A-post behöver du App Service-appens externa IP-adress, som visas på sidan **Anpassade domäner.**

Stäng sidan **Lägg till värdnamn** genom att välja **X** i det övre högra hörnet. 

På sidan **Anpassade domäner** kopierar du appens IP-adress.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Uppdatera DNS-posten

På sidan DNS-poster i domänleverantören väljer du den DNS-post som ska mappas om.

För `contoso.com` rotdomänexemplet mappa om A- eller CNAME-posten så som exemplen i följande tabell: 

| FQDN-exempel | Posttyp | Värd | Värde |
| - | - | - | - |
| contoso.com (rot) | A | `@` | IP-adress från [Kopiera appens IP-adress](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;appnamn>.azurewebsites.net_ |
| \*.contoso.com (jokertecken) | CNAME | _\*_ | _&lt;appnamn>.azurewebsites.net_ |

Spara inställningarna.

DNS-frågor bör börja matcha till apptjänstappen direkt efter att DNS-spridning har inträffar.

## <a name="active-domain-in-azure"></a>Aktiv domän i Azure

Du kan migrera en aktiv anpassad domän i Azure, mellan prenumerationer eller inom samma prenumeration. En sådan migrering utan driftstopp kräver dock att källappen och målappen tilldelas samma anpassade domän vid en viss tidpunkt. Därför måste du se till att de två apparna inte distribueras till samma distributionsenhet (internt känt som ett webbutrymme). Ett domännamn kan tilldelas endast en app i varje distributionsenhet.

Du hittar distributionsenheten för din app genom att titta på `<deployment-unit>.ftp.azurewebsites.windows.net`domännamnet för FTP/S-URL:en . Kontrollera och kontrollera att distributionsenheten skiljer sig mellan källappen och målappen. Distributionsenheten för en app bestäms av [apptjänstplanen](overview-hosting-plans.md) den finns i. Det väljs slumpmässigt av Azure när du skapar planen och kan inte ändras. Azure ser bara till att två planer finns i samma distributionsenhet när du [skapar dem i samma resursgrupp *och* samma region,](app-service-plan-manage.md#create-an-app-service-plan)men det har ingen logik för att se till att planerna finns i olika distributionsenheter. Det enda sättet för dig att skapa en plan i en annan distributionsenhet är att fortsätta skapa en plan i en ny resursgrupp eller region tills du får en annan distributionsenhet.

## <a name="next-steps"></a>Nästa steg

Läs om hur du binder ett anpassat SSL-certifikat till App Service.

> [!div class="nextstepaction"]
> [Binda ett SSL-certifikat till Azure App Service](configure-ssl-bindings.md)
