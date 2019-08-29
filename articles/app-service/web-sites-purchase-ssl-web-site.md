---
title: Köp och konfigurera ett SSL-certifikat från Azure-App Service | Microsoft Docs
description: Lär dig hur du köper ett App Service certifikat och binder det till din App Service-app
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: d6d3e91bef6c4f837b068d755994b2f3268600da
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074044"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Köp och konfigurera ett SSL-certifikat för Azure App Service

Den här självstudien visar hur du skyddar din [App Service app](https://docs.microsoft.com/azure/app-service/) -eller [Function-app](https://docs.microsoft.com/azure/azure-functions/) genom att skapa (köpa) ett app service certifikat i [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) och sedan binda det till en app service app.

> [!TIP]
> App Service certifikat kan användas för alla Azure-och icke-Azure-tjänster och är inte begränsade till App Services. För att göra det måste du skapa en lokal PFX-kopia av ett App Service-certifikat som du kan använda det var du vill. Mer information finns i [skapa en lokal PFX-kopia av en app service Certificate](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/).
>

## <a name="prerequisites"></a>Förutsättningar

För att följa den här instruktions guiden:

- [Skapa en App Service-app](/azure/app-service/)
- [Mappa ett domän namn till din app](app-service-web-tutorial-custom-domain.md) eller [köp och konfigurera det i Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Starta certifikat ordning

Starta en App Service certifikat ordning på <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">sidan App Service Certificate skapa</a>.

![Skapa certifikat](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Använd följande tabell som hjälp för att konfigurera certifikatet. Klicka på **Skapa** när du är klar.

| Inställning | Beskrivning |
|-|-|
| Name | Ett eget namn på ditt App Service certifikat. |
| Värdnamnets domän utan www | Ange rot domänen här. Det utfärdade certifikatet skyddar *både* rot domänen och under `www` domänen. I det utfärdade certifikatet innehåller fältet eget namn rot domänen och fältet Alternativt namn på certifikat mottagare innehåller `www` domänen. Om du bara vill skydda en under domän anger du det fullständigt kvalificerade domän namnet för under domänen här (till exempel `mysubdomain.contoso.com`).|
| Subscription | Datacenter som är värd för webbappen. |
| Resource group | Den resurs grupp som innehåller certifikatet. Du kan använda en ny resurs grupp eller välja samma resurs grupp som App Service-appen, till exempel. |
| Certifikat-SKU | Bestämmer vilken typ av certifikat som ska skapas, om ett standard certifikat eller [](https://wikipedia.org/wiki/Wildcard_certificate)ett jokertecken. |
| Juridiska villkor | Klicka för att bekräfta att du godkänner de juridiska villkoren. Certifikaten hämtas från GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Lagra i Azure Key Vault

När certifikat köpet har slutförts finns det några fler steg du måste slutföra innan du kan börja använda det här certifikatet. 

Välj certifikatet på sidan [app service certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och klicka sedan på **certifikat konfiguration** > **steg 1: Lagra**.

![Infoga bild av redo att lagra i KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) är en Azure-tjänst som hjälper till att skydda kryptografiska nycklar och hemligheter som används av moln program och-tjänster. Det är det lagrings utrymme som du väljer för App Service certifikat.

På sidan **Key Vault status** klickar du på **Key Vault lagrings plats** för att skapa ett nytt valv eller välja ett befintligt valv. Om du väljer att skapa ett nytt valv använder du följande tabell som hjälp för att konfigurera valvet och klicka på Skapa. Se för att skapa nya Key Vault i samma prenumeration och resurs grupp.

| Inställning | Beskrivning |
|-|-|
| Name | Ett unikt namn som består av alfanumeriska tecken och bindestreck. |
| Resource group | Som en rekommendation väljer du samma resurs grupp som ditt App Service certifikat. |
| Location | Välj samma plats som App Service-appen. |
| Prisnivå | Mer information finns [Azure Key Vault pris information](https://azure.microsoft.com/pricing/details/key-vault/). |
| Åtkomstprinciper| Definierar program och tillåten åtkomst till valv resurserna. Du kan konfigurera den senare genom att följa stegen i [bevilja flera program åtkomst till ett nyckel valv](../key-vault/key-vault-group-permissions-for-apps.md). |
| Virtual Network-åtkomst | Begränsa valv åtkomst till vissa virtuella Azure-nätverk. Du kan konfigurera den senare genom att följa stegen i [konfigurera Azure Key Vault brand väggar och virtuella nätverk](../key-vault/key-vault-network-security.md) |

När du har valt valvet stänger du sidan **Key Vault-lagringsplats** . **Store** -alternativet ska visa en grön bock markering för lyckad. Låt sidan vara öppen för nästa steg.

## <a name="verify-domain-ownership"></a>Verifiera domänägarskap

På sidan samma **certifikat konfiguration** som du använde i det sista steget klickar du **på steg 2: Verifiera**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Välj **App Service verifiering**. Eftersom du redan har mappat domänen till din webbapp (se [krav](#prerequisites)) är den redan verifierad. Klicka bara på **Verifiera** för att slutföra det här steget. Klicka på knappen **Uppdatera** tills meddelande **certifikatet är domän verifierat** visas.

> [!NOTE]
> Fyra typer av domän verifierings metoder stöds: 
> 
> - **App Service** – det enklaste alternativet när domänen redan är mappad till en app service-app i samma prenumeration. Det drar nytta av det faktum att App Service-appen redan har verifierat domänens ägarskap.
> - **Domän** – verifiera en [App Service-domän som du har köpt från Azure](manage-custom-dns-buy-domain.md). Azure lägger automatiskt till verifierings-TXT-posten åt dig och slutför processen.
> - **E-post** – verifiera domänen genom att skicka ett e-postmeddelande till domän administratören. Instruktioner finns när du väljer alternativet.
> - **Manuellt** – verifiera domänen genom att antingen använda en HTML-sida (endast**standard** certifikat) eller en DNS TXT-post. Instruktioner finns när du väljer alternativet.

## <a name="bind-certificate-to-app"></a>Bind certifikat till app

I **[Azure Portal](https://portal.azure.com/)** väljer du **app Services** >  **\<your_ app >** på menyn till vänster.

I den vänstra navigeringen i din app väljer **du SSL-inställningar** > **privat certifikat (. pfx)**  > **Importera App Service Certificate**.

![Infoga avbildning av import certifikat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Välj det certifikat som du just har köpt.

Nu när certifikatet har importer ATS måste du binda det till ett mappat domän namn i din app. Välj **bindningar** > **Lägg till SSL-bindning**. 

![Infoga avbildning av import certifikat](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Använd följande tabell som hjälp för att konfigurera bindningen i dialog rutan **SSL** -bindningar och klicka sedan på **Lägg till bindning**.

| Inställning | Beskrivning |
|-|-|
| Värddatornamn | Domän namnet för att lägga till SSL-bindning för. |
| Tumavtryck för privat certifikat | Certifikatet som ska bindas. |
| SSL-typ | <ul><li>**SNI SSL** -flera SNI-baserade SSL-bindningar kan läggas till. Med det här alternativet kan flera SSL-certifikat skydda flera domäner på samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (mer information om webbläsare som stöds finns i [Servernamnindikator](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP-baserad SSL** – Det går bara att lägga till en IP-baserad SSL-bindning. Med det här alternativet tillåts endast ett SSL-certifikat för att skydda en dedikerad offentlig IP-adress. När du har konfigurerat bindningen följer du stegen i [mappa om en post för IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Verifiera HTTPS-åtkomst

Besök din app med `HTTPS://<domain_name>` i stället `HTTP://<domain_name>` för för att kontrol lera att certifikatet har kon figurer ATS korrekt.

## <a name="rekey-certificate"></a>Uppdatera certifikatnyckel

Om du tror att certifikatets privata nyckel har komprometterats kan du uppdatera ditt certifikat. Välj certifikatet på sidan [app service certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och välj sedan nyckel uppdatering **och synkronisera** i det vänstra navigerings fältet.

Starta processen genom att klicka på **nyckel förnyelse** . Den här processen kan ta 1-10 minuter att slutföra.

![Infoga avbildning av uppdaterings-SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Omnyckelering av certifikatet rullar certifikatet med ett nytt certifikat utfärdat av certifikat utfärdaren.

När uppdaterings åtgärden har slutförts klickar du på **Synkronisera**. Synkroniseringsåtgärden uppdaterar automatiskt värd namns bindningarna för certifikatet i App Service utan att orsaka avbrott i dina appar.

> [!NOTE]
> Om du inte klickar på **Synkronisera**synkroniserar App Service automatiskt ditt certifikat inom 48 timmar.

## <a name="renew-certificate"></a>Förnya certifikat

Om du vill aktivera automatisk förnyelse av certifikatet väljer du certifikatet på sidan [app service certifikat](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) och klickar sedan på **Inställningar för automatisk förnyelse** i det vänstra navigerings fältet. App Service certifikat har som standard en giltighets period på 1 år.

Välj **på** och klicka på **Spara**. Certifikat kan börja förnyas automatiskt 60 dagar före förfallo datum om du har aktiverat automatisk förnyelse.

![Förnya certifikat automatiskt](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Om du vill förnya certifikatet manuellt i stället klickar du på **manuell förnyelse**. Du kan begära att förnya ditt certifikat 60 dagar manuellt innan det upphör att gälla.

När förnyelse åtgärden är klar klickar du på **Synkronisera**. Synkroniseringsåtgärden uppdaterar automatiskt värd namns bindningarna för certifikatet i App Service utan att orsaka avbrott i dina appar.

> [!NOTE]
> Om du inte klickar på **Synkronisera**synkroniserar App Service automatiskt ditt certifikat inom 48 timmar.

## <a name="automate-with-scripts"></a>Automatisera med skript

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Flera resurser

* [Använda HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Framtvinga TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Använd ett SSL-certifikat i program koden i Azure App Service](app-service-web-ssl-cert-load.md)
* [ASSURANCE App Service certifikat](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
