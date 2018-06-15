---
title: Migrera ett aktivt DNS-namn till Azure App Service | Microsoft Docs
description: Lär dig hur du migrerar en anpassad DNS-namnet har redan tilldelats en levande plats till Azure Apptjänst utan någon avbrottstid.
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
ms.openlocfilehash: cd04be2046a23901471cb7bd0da9e0ed2d514d0d
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
ms.locfileid: "27566499"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrera ett aktivt DNS-namn till Azure App Service

Den här artikeln visar hur du migrerar ett aktivt DNS-namn till [Azure App Service](../app-service/app-service-web-overview.md) utan driftavbrott.

När du migrerar en levande plats och DNS-domännamn till App Service används det DNS-namnet redan av live trafik. Du kan undvika avbrott i DNS-matchning under migreringen genom att binda aktiva DNS-namnet till din Apptjänst-app förebyggande syfte.

Om du inte oroar avbrott i DNS-matchning, se [mappa ett befintligt anpassade DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Förutsättningar

Att slutföra den här anvisningar:

- [Kontrollera att din Apptjänst-app inte är kostnadsfria nivån](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Binda domännamnet förebyggande syfte

När du binder en anpassad domän förebyggande syfte göra du följande innan du gör några ändringar i DNS-posterna:

- Verifiera domänägarskap
- Aktivera domännamnet för din app

När du migrerar slutligen din anpassade DNS-namnet från den gamla platsen till App Service-appen måste finnas det utan avbrott i DNS-matchning.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>Skapa post för verifiering av domän

Lägg till en TXT-post för att verifiera domänen ägarskap. TXT-posten mappar från _awverify.&lt; underdomän >_ till  _&lt;appname >. azurewebsites.net_. 

TXT-posten som du behöver beror på DNS-posten som du vill migrera. Exempel finns i följande tabell (`@` representerar vanligen rotdomänen):

| DNS-post-exempel | TXT-värden | TXT-värde |
| - | - | - |
| @ (rot) | _awverify_ | _&lt;programnamn >. azurewebsites.net_ |
| www (underordnade) | _awverify.www_ | _&lt;programnamn >. azurewebsites.net_ |
| \*(jokertecken) | _awverify.\*_ | _&lt;programnamn >. azurewebsites.net_ |

Observera posttyp för DNS-namn som du vill migrera på sidan DNS-poster. Apptjänst stöder mappningar från CNAME- och A-poster.

### <a name="enable-the-domain-for-your-app"></a>Aktivera domän för din app

I den [Azure-portalen](https://portal.azure.com), i det vänstra navigeringsfönstret på appsidan, Välj **anpassade domäner**. 

![Anpassade domäner-menyn](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

I den **anpassade domäner** väljer den  **+**  ikonen bredvid **lägga till värdnamnet**.

![Lägg till namnet på värddatorn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

Ange det fullständiga domännamnet som du har lagt till TXT-posten för, t.ex `www.contoso.com`. För en jokerteckendomän med (t.ex. \*. contoso.com), du kan använda DNS-namn som matchar jokertecknet domänen. 

Välj **Validera**.

Den **lägga till värdnamnet** knappen aktiveras. 

Se till att **Hostname posttyp** är inställd på DNS-posttyp du vill migrera.

Välj **lägga till värdnamnet**.

![Lägga till DNS-namn i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

Det kan ta en stund innan det nya värdnamnet återspeglas i appens **anpassade domäner** sidan. Försök att uppdatera webbläsaren för att uppdatera data.

![CNAME-post som lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

Din anpassade DNS-namn är nu aktiverad i din Azure-app. 

## <a name="remap-the-active-dns-name"></a>Mappa om det aktiva DNS-namnet

Det enda som återstår att göra ommappning din aktiva DNS-posten så att den pekar till App Service. Höger nu kan den fortfarande pekar på den gamla platsen.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Kopiera appens IP-adress (endast en post)

Om du mappa en CNAME-post, hoppar du över det här avsnittet. 

Om du vill mappa en A-post måste App Service-appen externa IP-adress som visas i den **anpassade domäner** sidan.

Stäng den **lägga till värdnamnet** sidan genom att välja **X** i det övre högra hörnet. 

I den **anpassade domäner** sidan, kopiera appens IP-adress.

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Uppdatera DNS-posten

Välj DNS-post för att mappa om tillbaka på sidan DNS-poster i din domänleverantör.

För den `contoso.com` rot domän exempel, mappa om A- eller CNAME-post som i exemplen i följande tabell: 

| FQDN-exempel | Typ av post | Värd | Värde |
| - | - | - | - |
| Contoso.com (rot) | A | `@` | IP-adress från [kopiera appens IP-adress](#info) |
| www.contoso.com (underordnade) | CNAME | `www` | _&lt;programnamn >. azurewebsites.net_ |
| \*. contoso.com (jokertecken) | CNAME | _\*_ | _&lt;programnamn >. azurewebsites.net_ |

Spara dina inställningar.

DNS-frågor ska starta matchning för din Apptjänst-app omedelbart efter DNS-spridningen händer.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att koppla en anpassad SSL-certifikatet till App Service.

> [!div class="nextstepaction"]
> [Bind ett befintligt anpassat SSL-certifikat till Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
