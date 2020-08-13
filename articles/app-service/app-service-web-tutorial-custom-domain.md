---
title: 'Självstudie: mappa ett befintligt anpassat DNS-namn'
description: Lär dig hur du lägger till ett befintligt anpassat DNS-domännamn (anpassad domän) i en webbapp, mobilappserverdel eller API-app i Azure App Service.
keywords: app service, azure app service, domain mapping, domain name, existing domain, hostname
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/13/2020
ms.custom: mvc, seodec18
ms.openlocfilehash: c301876a57b3be4a112c7df2706bf17389a5af44
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190073"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Självstudie: mappa ett befintligt anpassat DNS-namn till Azure App Service

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst. I den här självstudien visar vi hur du mappar ett befintligt anpassat DNS-namn till Azure App Service.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Mappa en underdomän (till exempel `www.contoso.com`) med hjälp av en CNAME-post
> * Mappa en rotdomän (till exempel `contoso.com`) med hjälp av en A-post
> * Mappa en domän med jokertecken (till exempel `*.contoso.com`) med hjälp av en CNAME-post
> * Omdirigera standard-URL:en till en anpassad katalog
> * Automatisera domänmappning med skript

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Skapa en App Service-app](/azure/app-service/), eller använd en app som du har skapat för en annan kurs.
* Köp ett domännamn och se till att du har åtkomst till DNS-registret för din domänleverantör (till exempel GoDaddy).

  För att till exempel lägga till DNS-poster för `contoso.com` och `www.contoso.com` måste du kunna konfigurera DNS-inställningarna för rotdomänen `contoso.com`.

  > [!NOTE]
  > Om du inte har något domännamn kan du [köpa en domän i Azure Portal](manage-custom-dns-buy-domain.md).

## <a name="prepare-the-app"></a>Förbereda appen

För att kunna mappa ett anpassat DNS-namn till en webbapp måste webbappens [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) vara en betalplan (**Delad**, **Basic**, **Standard**, **Premium** eller **Förbrukning** för Azure Functions). I det här steget ser du till att App Service-appen har en prisnivå som stöds.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="select-the-app-in-the-azure-portal"></a>Välj appen i Azure Portal

Sök efter och välj **app Services**.

![Välj App Services](./media/app-service-web-tutorial-custom-domain/app-services.png)

På sidan **app Services** väljer du namnet på din Azure-App.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

Du ser hanteringssidan för App Service-appen.  

<a name="checkpricing" aria-hidden="true"></a>

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfältet på appsidan bläddrar du till avsnittet **Inställningar** och väljer **Skala upp (App Service-plan)**.

![Skala upp-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är på nivån **F1**. Anpassad DNS stöds inte på nivån **F1**. 

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Om App Service-planen inte är på nivån **F1** stänger du sidan **Skala upp** och hoppar till [Mappa en CNAME-post](#cname).

<a name="scaleup" aria-hidden="true"></a>

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-planen

Välj någon av betalnivåerna (**D1**, **B1**, **B2**, **B3** eller en nivå i kategorin **Produktion**). Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Godkänn**.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

När du ser följande meddelande har skalningsåtgärden slutförts.

![Bekräftelse av skalningsåtgärd](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname" aria-hidden="true"></a>

## <a name="get-domain-verification-id"></a>Hämta ID för domän verifiering

Om du vill lägga till en anpassad domän i din app måste du verifiera din ägande av domänen genom att lägga till ett verifierings-ID som en TXT-post med din domän leverantör. Klicka på **anpassade domäner**i det vänstra navigerings fältet på din app-sida. Kopiera **ID för anpassad domän verifiering** på sidan **anpassade domäner** för nästa steg.

![Hämta ID för anpassad domän verifiering](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

> [!WARNING]
> Om du lägger till domän verifierings-ID: n i din anpassade domän kan du förhindra Dangling DNS-poster och för att undvika under domäner. Mer information om det här vanliga hot med hög allvarlighets grad finns i avsnittet övertag ande av [under domäner](../security/fundamentals/subdomain-takeover.md).

## <a name="map-your-domain"></a>Mappa din domän

Du kan mappa ett anpassat DNS-namn till App Service med antingen en **CNAME-post** eller en **A-post**. Följ respektive steg:

- [Mappa en CNAME-post](#map-a-cname-record)
- [Mappa en A-post](#map-an-a-record)
- [Mappa en domän med jokertecken (med en CNAME-post)](#map-a-wildcard-domain)

> [!NOTE]
> Du bör använda CNAME-poster för alla anpassade DNS-namn förutom rotdomäner (till exempel `contoso.com`). För rotdomäner använder du A-poster.

### <a name="map-a-cname-record"></a>Mappa en CNAME-post

I kursexemplet lägger du till en CNAME-post för `www`-underdomänen (till exempel `www.contoso.com`).

Om du har en annan under domän än `www` ersätter du `www` med under domänen (till exempel med `sub` om din anpassade domän är `sub.constoso.com` ).

#### <a name="access-dns-records-with-domain-provider"></a>Använda DNS-poster med domänleverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Mappa en under domän till appens standard domän namn ( `<app-name>.azurewebsites.net` där `<app-name>` är namnet på din app). Skapa en CNAME-mappning för under `www` domänen genom att skapa två poster:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `www` | `<app-name>.azurewebsites.net` | Själva domän mappningen. |
| TXT | `asuid.www` | [Verifierings-ID: t som du fick tidigare](#get-domain-verification-id) | App Service använder txt- `asuid.<subdomain>` posten för att verifiera din ägande av den anpassade domänen. |

När du har lagt till CNAME-och TXT-posterna ser sidan DNS-poster ut som i följande exempel:

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/cname-record.png)

#### <a name="enable-the-cname-record-mapping-in-azure"></a>Aktivera CNAME-postmappning i Azure

Välj **Anpassade domäner** i det vänstra navigeringsfönstret på appsidan i Azure Portal.

![Meny för anpassad domän](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

På sidan **Anpassade domäner** för appen lägger du till det fullständigt kvalificerade DNS-namnet (`www.contoso.com`) i listan.

Välj **+** ikonen bredvid **Lägg till anpassad domän**.

![Lägg till värddatornamn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Skriv det fullständigt kvalificerade domännamnet som du lade till en CNAME-post för, till exempel `www.contoso.com`.

Välj **Verifiera**.

Sidan **Lägg till anpassad domän** visas.

Se till att **post typen hostname** är inställd på **CNAME (www- \. example.com eller någon under domän)**.

Välj **Lägg till anpassad domän**.

![Lägg till DNS-namnet i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Försök att uppdatera webbläsaren så att informationen uppdateras.

![CNAME-posten har lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

> [!NOTE]
> En **osäker etikett för** din anpassade domän innebär att den inte har bundits till ett TLS/SSL-certifikat, och att alla https-förfrågningar från en webbläsare till din anpassade domän får och fel eller varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).

Om du har missat något steg eller stavat fel på något ord visas ett verifieringsfel längst ned på sidan.

![Verifieringsfel](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a" aria-hidden="true"></a>

### <a name="map-an-a-record"></a>Mappa en A-post

I kursexemplet lägger du till en A-post för rotdomänen (till exempel `contoso.com`).

<a name="info"></a>

#### <a name="copy-the-apps-ip-address"></a>Kopiera appens IP-adress

För att kunna mappa A-posten behöver du appens externa IP-adress. Du hittar IP-adressen på sidan **Anpassade domäner** för appen i Azure Portal.

Välj **Anpassade domäner** i det vänstra navigeringsfönstret på appsidan i Azure Portal.

![Meny för anpassad domän](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

På sidan **Anpassade domäner** kopierar du appens IP-adress.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

#### <a name="access-dns-records-with-domain-provider"></a>Använda DNS-poster med domänleverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-a-record"></a>Skapa en A-post

För att mappa en A-post till en app, vanligt vis till rot domänen, skapar du två poster:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| A | `@` | IP-adress från [Kopiera appens IP-adress](#info) | Själva domän mappningen ( `@` representerar vanligt vis rot domänen). |
| TXT | `asuid` | [Verifierings-ID: t som du fick tidigare](#get-domain-verification-id) | App Service använder txt- `asuid.<subdomain>` posten för att verifiera din ägande av den anpassade domänen. Använd för rot domänen `asuid` . |

> [!NOTE]
> Om du vill lägga till en underdomän (t.ex. `www.contoso.com`) med en A-post i stället för en rekommenderad [CNAME-post](#map-a-cname-record) bör A-posten och TXT-posten se ut som följande tabell i stället:
>
> | Posttyp | Värd | Värde |
> | - | - | - |
> | A | `www` | IP-adress från [Kopiera appens IP-adress](#info) |
> | TXT | `asuid.www` | `<app-name>.azurewebsites.net` |
>

När posterna har lagts till ser sidan för DNS-poster ut som i följande exempel:

![Sida för DNS-poster](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a" aria-hidden="true"></a>

#### <a name="enable-the-a-record-mapping-in-the-app"></a>Aktivera A-postmappning i appen

Gå till sidan **Anpassade domäner** för appen i Azure Portal, lägg till det fullständigt kvalificerade DNS-namnet (till exempel `contoso.com`) i listan.

Välj **+** ikonen bredvid **Lägg till anpassad domän**.

![Lägg till värddatornamn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Skriv det fullständigt kvalificerade domännamnet som du konfigurerade A-posten för, till exempel `contoso.com`.

Välj **Verifiera**.

Sidan **Lägg till anpassad domän** visas.

Se till att **Posttyp för värddatornamn** har värdet **A-post (example.com)**.

Välj **Lägg till anpassad domän**.

![Lägg till DNS-namnet i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Försök att uppdatera webbläsaren så att informationen uppdateras.

![A-posten har lagts till](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

> [!NOTE]
> En **osäker etikett för** din anpassade domän innebär att den inte har bundits till ett TLS/SSL-certifikat, och att alla https-förfrågningar från en webbläsare till din anpassade domän får och fel eller varning, beroende på webbläsaren. Om du vill lägga till en TLS-bindning, se [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).

Om du har missat något steg eller stavat fel på något ord visas ett verifieringsfel längst ned på sidan.

![Verifieringsfel](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard" aria-hidden="true"></a>

### <a name="map-a-wildcard-domain"></a>Mappa en domän med jokertecken

I kursexemplet mappar du ett [DNS-namn med jokertecken](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (till exempel `*.contoso.com`) till App Service-appen genom att lägga till en CNAME-post.

#### <a name="access-dns-records-with-domain-provider"></a>Använda DNS-poster med domänleverantör

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

#### <a name="create-the-cname-record"></a>Skapa CNAME-posten

Mappa ett jokertecken `*` till appens standard domän namn ( `<app-name>.azurewebsites.net` där `<app-name>` är namnet på din app). Skapa två poster för att mappa namnet på jokertecken:

| Posttyp | Värd | Värde | Kommentarer |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Själva domän mappningen. |
| TXT | `asuid` | [Verifierings-ID: t som du fick tidigare](#get-domain-verification-id) | App Service använder txt- `asuid` posten för att verifiera din ägande av den anpassade domänen. |

För `*.contoso.com`-domänexemplet mappar CNAME-posten namnet `*` till `<app-name>.azurewebsites.net`.

När CNAME har lagts till ser sidan för DNS-poster ut som i följande exempel:

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

#### <a name="enable-the-cname-record-mapping-in-the-app"></a>Aktivera CNAME-postmappning i appen

Nu kan du lägga till alla under domäner som matchar jokertecknets namn i appen (till exempel `sub1.contoso.com` och `sub2.contoso.com` båda matchningarna `*.contoso.com` ).

Välj **Anpassade domäner** i det vänstra navigeringsfönstret på appsidan i Azure Portal.

![Meny för anpassad domän](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Välj **+** ikonen bredvid **Lägg till anpassad domän**.

![Lägg till värddatornamn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Ange ett fullständigt kvalificerat domännamn som matchar domänen med jokertecken (till exempel `sub1.contoso.com`) och välj sedan **Verifiera**.

Knappen **Lägg till anpassad domän** är aktive rad.

Se till att **post typen hostname** är inställd på **CNAME-post (www- \. example.com eller under domän)**.

Välj **Lägg till anpassad domän**.

![Lägg till DNS-namnet i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Det kan ta lite tid innan den nya anpassade domänen visas på sidan **anpassade domäner** för appen. Försök att uppdatera webbläsaren så att informationen uppdateras.

Välj **+** ikonen igen för att lägga till en annan anpassad domän som matchar domänen med jokertecken. Lägg exempelvis till `sub2.contoso.com`.

![CNAME-posten har lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

> [!NOTE]
> En **säker** etikett för den anpassade domänen innebär att den inte har bundits till ett TLS/SSL-certifikat och att alla https-förfrågningar från en webbläsare till din anpassade domän får och fel eller varning, beroende på webbläsare. Om du vill lägga till en TLS-bindning, se [skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md).

## <a name="test-in-browser"></a>Testa i webbläsaren

Bläddra till DNS-namnet (eller namnen) som du konfigurerade tidigare (till exempel `contoso.com`,  `www.contoso.com`, `sub1.contoso.com` och `sub2.contoso.com`).

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-not-found"></a>Lös 404 "hittades inte"

Om du får ett HTTP 404-fel (Hittades inte) när du går till URL:en för den anpassade domänen, kontrollerar du att domänen matchar appens IP-adress med hjälp av <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Om inte, kan det bero på något av följande:

- Den anpassade domänen som har konfigurerats saknar en A-post och/eller en CNAME-post.
- Webbläsarklienten har cachat din domäns gamla IP-adress. Rensa cachen och testa DNS-matchningen på nytt. På en Windows-dator rensar du cachen med `ipconfig /flushdns`.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="migrate-an-active-domain"></a>Migrera en aktiv domän

Om du vill migrera en live-webbplats och dess DNS-domännamn till App Service utan avbrott kan du läsa [Migrera ett aktivt DNS-namn till Azure App Service](manage-custom-dns-migrate-domain.md).

## <a name="redirect-to-a-custom-directory"></a>Omdirigera till en anpassad katalog

Som standard dirigerar App Service webbegäranden till rotkatalogen för din appkod. Men vissa webbramverk startar inte i rotkatalogen. [Laravel](https://laravel.com/) startar till exempel i underkatalogen `public`. Om du fortsatte med `contoso.com`-DNS-exemplet skulle en sådan app vara tillgänglig på `http://contoso.com/public`, men det vore att föredra att dirigera `http://contoso.com` till katalogen `public` i stället. Det här steget innefattar anpassning av den virtuella katalogen, inte DNS-matchning.

Det gör du genom att välja **Programinställningar** i det vänstra navigeringsfältet på sidan för webbappen. 

Längst ned på sidan pekar den virtuella rotkatalogen `/` till `site\wwwroot` som standard, vilket är rotkatalogen för din appkod. Ändra den så att den pekar till exempelvis `site\wwwroot\public` i stället, och spara ändringarna.

![Anpassa virtuell katalog](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

När åtgärden har slutförts ska din app returnera rätt sida på rot Sök vägen (till exempel `http://contoso.com` ).

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hanteringen av anpassade domäner med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/). 

### <a name="azure-cli"></a>Azure CLI 

Följande kommando lägger till ett konfigurerat anpassat DNS-namn i en App Service-app. 

```bash 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Mer information finns i [Mappa en anpassad domän till en webbapp](scripts/cli-configure-custom-domain.md).

### <a name="azure-powershell"></a>Azure PowerShell 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando lägger till ett konfigurerat anpassat DNS-namn i en App Service-app.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Mer information finns i [Tilldela en anpassad domän till en webbapp](scripts/powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Mappa en underdomän med hjälp av en CNAME-post
> * Mappa en rotdomän med hjälp av en A-post
> * Mappa en domän med jokertecken med hjälp av en CNAME-post
> * Omdirigera standard-URL:en till en anpassad katalog
> * Automatisera domänmappning med skript

Gå vidare till nästa självstudie för att lära dig att binda ett anpassat TLS/SSL-certifikat till en webbapp.

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service](configure-ssl-bindings.md)
