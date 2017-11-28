---
title: Mappa ett befintligt anpassade DNS-namn till Azure Web Apps | Microsoft Docs
description: "Lär dig hur du lägger till en befintlig anpassad DNS-domännamn (alternativa domänen) till en webbapp, mobilappsserverdel eller API-app i Azure App Service."
keywords: "App service, azure app service, domänmappning, domännamn, befintlig domän, värdnamn"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1a0b54e75bd6356ba7ba351d51d5f4a59bd64c75
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2017
---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mappa ett befintligt anpassade DNS-namn till Azure Web Apps

Med [Azure Web Apps](app-service-web-overview.md) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. Den här kursen visar hur du mappar en befintlig anpassad DNS-namn till Azure Web Apps.

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Mappa en underdomän (till exempel `www.contoso.com`) med hjälp av en CNAME-post
> * Mappa en rotdomän (till exempel `contoso.com`) med hjälp av en A-post
> * Mappa en jokerteckendomän med (till exempel `*.contoso.com`) med hjälp av en CNAME-post
> * Automatisera domänmappning med skript

Du kan använda antingen en **CNAME-post** eller en **en post** att mappa ett anpassat DNS-namn till App Service. 

> [!NOTE]
> Vi rekommenderar att du använder en CNAME-post för alla anpassade DNS-namn utom en rotdomän (till exempel `contoso.com`).

Om du vill migrera en levande plats och DNS-domännamn till App Service, se [Migrera ett aktivt DNS-namn till Azure App Service](app-service-custom-domain-name-migrate.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* [Skapa en Apptjänst-app](/azure/app-service/), eller använda en app som du skapade för en annan kursen.
* Köpa ett domännamn och kontrollera att du har åtkomst till DNS-registret för din domän-provider (till exempel GoDaddy).

  Till exempel för att lägga till DNS-poster för `contoso.com` och `www.contoso.com`, du måste kunna konfigurera DNS-inställningarna för den `contoso.com` rotdomänen.

  > [!NOTE]
  > Om du inte har en befintlig domän namn bör du överväga att [köper en domän med hjälp av Azure portal](custom-dns-web-site-buydomains-web-app.md). 

## <a name="prepare-the-app"></a>Förbered appen

Mappa ett anpassat DNS-namn till ett webbprogram, webbappen [programtjänstplanen](https://azure.microsoft.com/pricing/details/app-service/) måste vara en betald nivå (**delade**, **grundläggande**, **Standard**, eller **Premium**). I det här steget kan se du till att Apptjänst-app är i det prisnivån.

### <a name="sign-in-to-azure"></a>Logga in på Azure

Öppna den [Azure-portalen](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navigera till appen i Azure-portalen

I den vänstra menyn, Välj **Apptjänster**, och välj sedan namnet på appen.

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/select-app.png)

Du ser sidan för hantering av Apptjänst-app.  

<a name="checkpricing"></a>

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfönstret på appsidan, bläddra till den **inställningar** avsnittet och väljer **skala upp (apptjänstplan)**.

![Skala-menyn](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Appens aktuell nivå markeras med en blå kantlinje. Kontrollera att appen inte är i den **lediga** nivå. Anpassad DNS stöds inte i den **lediga** nivå. 

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Om App Service-plan inte **lediga**Stäng den **Välj din prisnivå** sidan och gå vidare till [mappa en CNAME-post](#cname).

<a name="scaleup"></a>

### <a name="scale-up-the-app-service-plan"></a>Skala upp App Service-plan

Välj någon av de icke kostnadsfria nivåerna (**delade**, **grundläggande**, **Standard**, eller **Premium**). 

Klicka på **Välj**.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

När du ser följande meddelande har åtgärden slutförts.

![Skala åtgärd-bekräftelse](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>Mappa en CNAME-post

I kursen exempel du lägger till en CNAME-post för den `www` underdomänen (till exempel `www.contoso.com`).

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Skapa CNAME-post

Lägg till en CNAME-post för att mappa en underdomän till appens standard värdnamn (`<app_name>.azurewebsites.net`, där `<app_name>` är namnet på din app).

För den `www.contoso.com` domän exempelvis lägga till en CNAME-post som mappar namnet `www` till `<app_name>.azurewebsites.net`.

När du har lagt till CNAME sidan DNS-poster som ser ut som i följande exempel:

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Aktivera mappning för CNAME-post i Azure

I det vänstra navigeringsfönstret på appsidan i Azure portal väljer **anpassade domäner**. 

![Anpassade domäner-menyn](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

I den **anpassade domäner** sida i appen, lägga till det fullständigt kvalificerade anpassade DNS-namnet (`www.contoso.com`) i listan.

Välj den  **+**  ikonen bredvid **lägga till värdnamnet**.

![Lägg till namnet på värddatorn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Ange det fullständiga domännamnet som du har lagt till en CNAME-post för, t.ex `www.contoso.com`. 

Välj **Validera**.

Den **lägga till värdnamnet** knappen aktiveras. 

Se till att **Hostname posttyp** är inställd på **CNAME (www.example.com eller alla underdomäner)**.

Välj **lägga till värdnamnet**.

![Lägga till DNS-namn i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Det kan ta en stund innan det nya värdnamnet återspeglas i appens **anpassade domäner** sidan. Försök att uppdatera webbläsaren för att uppdatera data.

![CNAME-post som lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Om du har missat ett steg eller stavat någonstans tidigare kan du se ett verifieringsfel längst ned på sidan.

![Verifieringsfel](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>Mappa en A-post

Lägg till en A-post för rotdomänen i självstudiekursen exempel (till exempel `contoso.com`). 

<a name="info"></a>

### <a name="copy-the-apps-ip-address"></a>Kopiera appens IP-adress

Om du vill mappa en A-post, måste appens extern IP-adress. Du hittar den här IP-adressen i appens **anpassade domäner** sida i Azure-portalen.

I det vänstra navigeringsfönstret på appsidan i Azure portal väljer **anpassade domäner**. 

![Anpassade domäner-menyn](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

I den **anpassade domäner** sidan, kopiera appens IP-adress.

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-a-record"></a>Skapa A-post

Om du vill mappa en A-post till en app kräver Apptjänst **två** DNS-poster:

- En **A** post mappas till appens IP-adress.
- En **TXT** post att mappa till appens standard värdnamn `<app_name>.azurewebsites.net`. Apptjänst använder den här posten endast vid konfigurationen, för att verifiera att du äger den anpassade domänen. När din anpassade domän har verifierats och konfigurerat i App Service kan du ta bort TXT-posten. 

För den `contoso.com` domän exempelvis skapa en och TXT-poster enligt tabellen nedan (`@` representerar vanligen rotdomänen). 

| Typ av post | Värd | Värde |
| - | - | - |
| A | `@` | IP-adress från [kopiera appens IP-adress](#info) |
| TXT | `@` | `<app_name>.azurewebsites.net` |

När posterna läggs sidan DNS-poster som ser ut som i följande exempel:

![DNS-poster sidan](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-the-app"></a>Aktivera mappning för A-post i appen

Tillbaka i appens **anpassade domäner** i Azure-portalen, Lägg till fullständiga anpassade DNS-namn (till exempel `contoso.com`) i listan.

Välj den  **+**  ikonen bredvid **lägga till värdnamnet**.

![Lägg till namnet på värddatorn](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

Ange det fullständiga domännamnet som du har konfigurerat A-post för, t.ex `contoso.com`.

Välj **Validera**.

Den **lägga till värdnamnet** knappen aktiveras. 

Se till att **Hostname posttyp** är inställd på **en post (example.com)**.

Välj **lägga till värdnamnet**.

![Lägga till DNS-namn i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

Det kan ta en stund innan det nya värdnamnet återspeglas i appens **anpassade domäner** sidan. Försök att uppdatera webbläsaren för att uppdatera data.

![En post som lagts till](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

Om du har missat ett steg eller stavat någonstans tidigare kan du se ett verifieringsfel längst ned på sidan.

![Verifieringsfel](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>Mappa en jokerteckendomän med

I kursen exempel du mappa en [jokertecken DNS-namnet](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (till exempel `*.contoso.com`) till App Service-appen genom att lägga till en CNAME-post. 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-the-cname-record"></a>Skapa CNAME-post

Lägg till en CNAME-post för att mappa ett jokertecken namn till appens standard värdnamn (`<app_name>.azurewebsites.net`).

För den `*.contoso.com` domän exempelvis CNAME-post mappas namnet `*` till `<app_name>.azurewebsites.net`.

När CNAME läggs sidan DNS-poster som ser ut som i följande exempel:

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)

### <a name="enable-the-cname-record-mapping-in-the-app"></a>Aktivera mappning för CNAME-post i appen

Nu kan du lägga till alla underdomäner som matchar namnet på appen med jokertecken (till exempel `sub1.contoso.com` och `sub2.contoso.com` matchar `*.contoso.com`). 

I det vänstra navigeringsfönstret på appsidan i Azure portal väljer **anpassade domäner**. 

![Anpassade domäner-menyn](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Välj den  **+**  ikonen bredvid **lägga till värdnamnet**.

![Lägg till namnet på värddatorn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Ange ett fullständigt kvalificerat domännamn som matchar jokertecknet domänen (till exempel `sub1.contoso.com`), och välj sedan **verifiera**.

Den **lägga till värdnamnet** knappen aktiveras. 

Se till att **Hostname posttyp** är inställd på **CNAME-post (www.example.com eller alla underdomäner)**.

Välj **lägga till värdnamnet**.

![Lägga till DNS-namn i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

Det kan ta en stund innan det nya värdnamnet återspeglas i appens **anpassade domäner** sidan. Försök att uppdatera webbläsaren för att uppdatera data.

Välj den  **+**  ikonen igen om du vill lägga till ett annat värdnamn som matchar jokertecknet domänen. Lägg exempelvis till `sub2.contoso.com`.

![CNAME-post som lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>Testa i webbläsaren

Bläddra till DNS-namn som du tidigare har konfigurerat (till exempel `contoso.com`, `www.contoso.com`, `sub1.contoso.com`, och `sub2.contoso.com`).

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="resolve-404-error-web-site-not-found"></a>Åtgärda 404 felet ”Det gick inte att hitta webbplatsen”

Om du får felmeddelandet HTTP 404 (inget hittas) när du bläddrar till URL: en för den anpassade domänen kontrollerar du att din domän matchas till din app IP-adress med <a href="https://www.whatsmydns.net/" target="_blank">WhatsmyDNS.net</a>. Om inte, det kan bero på något av följande skäl:

- Den anpassade domänen som konfigurerats saknar en A-post och/eller en CNAME-post.
- Klientens webbläsare har cachelagrat gamla IP-adressen för din domän. Rensa cache och testa DNS-matchningen igen. På en Windows-dator du har rensat med `ipconfig /flushdns`.

<a name="virtualdir"></a>

## <a name="direct-default-url-to-a-custom-directory"></a>Direkt standard-URL till en anpassad katalog

Som standard gör Apptjänst webbegäranden till rotkatalogen för din Appkod. Vissa web ramverk starta men inte i rotkatalogen. Till exempel [Laravel](https://laravel.com/) startar i den `public` underkatalog. Fortsätta den `contoso.com` DNS exempelvis sådana en app som skulle vara tillgängligt vid `http://contoso.com/public`, men du vill verkligen vill dirigera `http://contoso.com` till den `public` katalog i stället. Det här steget inbegriper inte DNS-matchning, men anpassa den virtuella katalogen.

Om du vill göra det, Välj **programinställningar** i det vänstra navigeringsfönstret på webbsidan för appen. 

Längst ned på sidan, den virtuella rotkatalogen `/` pekar på `site\wwwroot` som standard, vilket är rotkatalogen för din Appkod. Ändra den att peka mot den `site\wwwroot\public` i stället, till exempel och spara ändringarna. 

![Anpassa virtuell katalog](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

När åtgärden har slutförts ska du app returnera högra sidan vid rotsökvägen (till exempel http://contoso.com).

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hanteringen av anpassade domäner med skript, med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview). 

### <a name="azure-cli"></a>Azure CLI 

Följande kommando lägger till en konfigurerad anpassade DNS-namnet till en Apptjänst-app. 

```bash 
az webapp config hostname add \
    --webapp-name <app_name> \
    --resource-group <resource_group_name> \ 
    --hostname <fully_qualified_domain_name> 
``` 

Mer information finns i [mappa en anpassad domän till en webbapp](scripts/app-service-cli-configure-custom-domain.md). 

### <a name="azure-powershell"></a>Azure PowerShell 

Följande kommando lägger till en konfigurerad anpassade DNS-namnet till en Apptjänst-app. 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

Mer information finns i [tilldela en anpassad domän till en webbapp](scripts/app-service-powershell-configure-custom-domain.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Mappa en underdomän genom att använda en CNAME-post
> * Mappa en rotdomän med en A-post
> * Mappa en jokerteckendomän med genom att använda en CNAME-post
> * Automatisera domänmappning med skript

Gå vidare till nästa kursen lär dig hur du binda ett anpassat SSL-certifikat till ett webbprogram.

> [!div class="nextstepaction"]
> [Bind ett befintligt anpassat SSL-certifikat till Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
