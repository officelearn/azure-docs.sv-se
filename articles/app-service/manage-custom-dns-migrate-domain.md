---
title: Migrera ett aktivt DNS-namn – Azure App Service | Microsoft Docs
description: Lär dig hur du migrerar ett anpassat DNS-domännamn som redan har tilldelats till en live-webbplats till Azure App Service utan någon avbrottstid.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6215230a52bcb5c44f54747b447dc5f64e6af650
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57999087"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrera ett aktivt DNS-namn till Azure App Service

Den här artikeln visar hur du migrerar ett aktivt DNS-namn till [Azure App Service](../app-service/overview.md) utan någon avbrottstid.

När du migrerar en live-webbplats och dess DNS-domännamn till App Service, fungerar det DNS-namnet redan live-trafik. Du kan undvika avbrott i DNS-matchningen under migreringen genom att binda aktivt DNS-namn till App Service-appen förebyggande syfte.

Om du inte oroar avbrott i DNS-matchning, se [mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här anvisningen:

- [Se till att din App Service-app inte är i den kostnadsfria nivån](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Binda domännamnet förebyggande syfte

När du binder en anpassad domän förebyggande syfte, utföra båda av följande innan du gör några ändringar i DNS-poster:

- Verifiera domänägarskap
- Aktivera domännamnet för din app

När du migrerar slutligen din anpassade DNS-namnet från den gamla platsen till App Service-appen, kommer att göras utan avbrott i DNS-matchning.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Skapa post för verifiering av domän

Du kan kontrollera domänägarskapet genom att lägga till en TXT-post. TXT-posten mappar från _awverify.&lt; underdomän >_ till  _&lt;appname >. azurewebsites.net_. 

The TXT record you need depends on the DNS record you want to migrate. Exempel finns i följande tabell (`@` representerar vanligtvis rotdomänen):

| DNS-post-exempel | TXT Host | TXT Value |
| - | - | - |
| \@ (rot) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www (under) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \* (med jokertecken) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

Observera postens typ av DNS-namnet som du vill migrera på sidan DNS-poster. App Service stöder mappningar från CNAME- och A-poster.

> [!NOTE]
> För vissa leverantörer, till exempel CloudFlare, `awverify.*` är inte en giltig post. Använd `*` endast i stället.

> [!NOTE]
> Jokertecken `*` poster inte att valideras underdomäner med ett befintligt CNAME-post. Du kan behöva skapa en TXT-post för varje underdomän explicit.


### <a name="enable-the-domain-for-your-app"></a>Aktivera domänen för din app

I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret på appsidan väljer **anpassade domäner**. 

![Meny för anpassad domän](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

I den **anpassade domäner** väljer den **+** ikonen bredvid **Lägg till värddatornamn**.

![Lägg till värddatornamn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Skriv det fullständigt kvalificerade domännamnet att du har lagt till TXT-posten för, till exempel `www.contoso.com`. För en domän med jokertecken (t.ex. \*. contoso.com), kan du använda valfri DNS-namn som matchar domänen med jokertecken. 

Välj **Verifiera**.

Knappen **Lägg till värddatornamn** aktiveras. 

Se till att **posttyp för värddatornamn** är inställd på DNS-posttypen som du vill migrera.

Välj **Lägg till värddatornamn**.

![Lägg till DNS-namnet i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Det kan ta en stund innan det nya värdnamnet återspeglas på sidan **Anpassade domäner** för appen. Försök att uppdatera webbläsaren så att informationen uppdateras.

![CNAME-posten har lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Din anpassade DNS-namn är nu aktiverad i din Azure-app. 

## <a name="remap-the-active-dns-name"></a>Mappa om aktivt DNS-namn

Den enda som återstår att göra att mappa om din aktiva DNS-post som pekar på App Service. Höger nu kan den fortfarande pekar mot din gamla platsen.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Kopiera appens IP-adress (endast en post)

Om du på genom att mappa om en CNAME-post, kan du hoppa över det här avsnittet. 

Om du vill mappa om en A-post, behöver du App Service-appen extern IP-adress, som visas i den **anpassade domäner** sidan.

Stäng den **Lägg till värddatornamn** genom att välja **X** i det övre högra hörnet. 

På sidan **Anpassade domäner** kopierar du appens IP-adress.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Uppdatera DNS-post

Välj DNS-post att mappa om tillbaka på sidan för DNS-poster i din domänleverantör.

För den `contoso.com` rot domänexemplet, mappa om A- eller CNAME-post som i exemplen i följande tabell: 

| FQDN-exempel | Posttyp | Värd | Värde |
| - | - | - | - |
| Contoso.com (rot) | A | `@` | IP-adress från [Kopiera appens IP-adress](#info) |
| www\.contoso.com (sub) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*. contoso.com (med jokertecken) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

Spara dina inställningar.

DNS-frågor ska börja matcha till din App Service-app direkt efter DNS-spridningen händer.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du binder ett anpassat SSL-certifikat till App Service.

> [!div class="nextstepaction"]
> [Binda ett befintligt anpassat SSL-certifikat till Azure App Service](app-service-web-tutorial-custom-ssl.md)
