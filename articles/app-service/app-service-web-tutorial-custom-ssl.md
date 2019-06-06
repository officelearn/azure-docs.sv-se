---
title: Ladda upp och binda SSL-certifikat – Azure App Service | Microsoft Docs
description: Lär dig hur du binder ett anpassat SSL-certifikat till din webbapp, din mobila serverdelstjänst för appar eller en API-app i Azure App Service.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 32e6311a8796e708119f3e1df813b6ebb2ed0673
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743011"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Självstudier: Ladda upp och binda SSL-certifikat till Azure App Service

Med [Azure App Service](overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här självstudien visar hur du skyddar en anpassad domän i App Service med ett certifikat som du har köpt från en betrodd certifikatutfärdare. Det får också lära dig hur du överför alla privata och offentliga certifikat behoven för dina appar. När du är klar så kommer du att ha åtkomst till din app på HTTPS-slutpunkten för din anpassade DNS-domän.

![Webbapp med anpassat SSL-certifikat](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppgradera appens prisnivå
> * Skydda en anpassad domän med ett certifikat
> * Ladda upp ett privat certifikat
> * Ladda upp ett offentligt certifikat
> * Förnya certifikat
> * Använda HTTPS
> * Använda TLS 1.1/1.2
> * Automatisera hantering av TLS med skript

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

- [Skapa en App Service-app](/azure/app-service/)
- [Mappa ett anpassat DNS-namn till din App Service-app](app-service-web-tutorial-custom-domain.md) (om skydda en anpassad domän)
- Skaffa ett certifikat från en betrodd certifikatutfärdare
- Har den privata nyckeln som du använde för att registrera certifikatbegäran (för privata certifikat)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Förbereda ett privat certifikat

Om du vill skydda en domän, måste certifikatet uppfylla följande krav:

* Konfigurerad för serverautentisering
* Vara signerat av en betrodd certifikatutfärdare
* Ha exporterats som en lösenordsskyddad PFX-fil
* Innehålla en privat nyckel som är minst 2 048 bitar lång
* Innehålla alla mellanliggande certifikat i certifikatkedjan

> [!TIP]
> Om du vill få ett anpassat SSL-certifikat kan du skaffa ett i Azure portal direkt och importera den till din app. Följ [Självstudie för App Service Certificate](web-sites-purchase-ssl-web-site.md).

> [!NOTE]
> **ECC-certifikat (Elliptic Curve Cryptography)** kan fungera med App Service, men behandlas inte i den här artikeln. Din certifikatutfärdare kan visa hur du skapar ECC-certifikat.

När du har fått ett certifikat från certifikat-providern, följer du stegen i det här avsnittet för att förbereda för App Service.

### <a name="merge-intermediate-certificates"></a>Sammanfoga mellanliggande certifikat

Om du får flera certifikat av din certifikatutfärdare i certifikatkedjan, måste du sammanfoga certifikaten i ordning.

Gör detta genom att öppna varje certifikat som du fick i ett redigeringsprogram.

Skapa en fil för det sammanfogade certifikatet med namnet _mergedcertificate.crt_. I redigeringsprogrammet kopierar du innehållet i varje certifikat till den här filen. Ordningen på dina certifikat ska följa ordningen i certifikatkedjan, först med ditt certifikat och sist med rotcertifikatet. Det ser ut som i följande exempel:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportera certifikat till PFX

Exportera det sammanfogade SSL-certifikatet med den privata nyckel som certifikatbegärandet genererades med.

Om du genererade din certifikatbegäran med hjälp av OpenSSL, har du skapat en privat nyckelfil. Kör följande kommando för att exportera certifikatet till PFX. Ersätt platshållarna _&lt;private-key-file>_ och _&lt;merged-certificate-file>_ med sökvägarna till din privata nyckel och din sammanfogade certifikatfil.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

När du uppmanas till det anger du ett exportlösenord. Det här lösenordet används när du senare överför ditt SSL-certifikat till App Service.

Om du använder IIS eller _Certreq.exe_ till att generera din certifikatbegäran, installerar du certifikatet på den lokala datorn och [exporterar sedan certifikatet till PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

Du är nu klar överför certifikatet till App Service.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Skydda en anpassad domän

> [!TIP]
> Om du behöver ett anpassat SSL-certifikat kan du skaffa ett i Azure Portal direkt och binda det till din app. Följ [Självstudie för App Service Certificate](web-sites-purchase-ssl-web-site.md).

Att skydda en [anpassad domän](app-service-web-tutorial-custom-domain.md) med ett certifikat från tredje part, som du överför den [förberett privata certifikat](#prepare-a-private-certificate) och kopplar den till den anpassade domänen, men App Service gör enklare för dig. Utför följande steg:

Klicka på **anpassade domäner** i den vänstra navigeringen i din app och klicka på **Lägg till bindning** för domänen som du vill skydda. Om du inte ser **Lägg till bindning** för en domän, sedan du den redan är säker och ska ha en **Secure** SSL-tillstånd.

![Lägg till bindning till domänen](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Klicka på **Ladda upp certifikat**.

I **PFX-certifikatsfil** väljer du din PFX-fil. I **Certifikatslösenord** skriver du lösenordet som du skapade när du exporterade PFX-filen.

Klicka på **Överför**.

![Överför certifikat för domänen](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Vänta tills Azure för att ladda upp certifikatet och starta dialogrutan för SSL-bindningar.

Välj det certifikat som du överförde och SSL-typ i dialogrutan bindningar för SSL, och klicka sedan på **Lägg till bindning för**.

> [!NOTE]
> Följande typer av SSL stöds:
>
> - **[SNI-baserad SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)**  -flera SNI-baserad SSL-bindningar kan läggas till. Med det här alternativet kan flera SSL-certifikat skydda flera domäner på samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (mer information om webbläsare som stöds finns i [Servernamnindikator](https://wikipedia.org/wiki/Server_Name_Indication)).
> - **IP-baserad SSL** – Det går bara att lägga till en IP-baserad SSL-bindning. Med det här alternativet tillåts endast ett SSL-certifikat för att skydda en dedikerad offentlig IP-adress. Om du vill skydda flera domäner, måste du skydda alla med samma SSL-certifikat. Detta är det vanligaste alternativet för SSL-bindning.

![Binda SSL till domänen](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Domänens SSL-status bör nu ändras till **Secure**.

![Domänen skyddas](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> En **Secure** tillstånd i den **anpassade domäner** innebär att den är skyddad med ett certifikat, men Apptjänst kontrollerar inte om certifikatet är självsignerat eller upphört att gälla, till exempel som också kan orsaka webbläsare till Visa ett fel eller en varning.

## <a name="remap-a-record-for-ip-ssl"></a>Mappa om en A-post för IP SSL

Om du inte använder IP-baserad SSL i din app så kan du gå vidare till [Testa HTTPS för din anpassade domän](#test).

Som standard använder din app en delad offentlig IP-adress. När du binder ett certifikat med IP-baserad SSL, skapar App Service en ny dedikerad IP-adress för din app.

Om du har mappat en A-post till din app så uppdaterar du domänregistret med den här nya dedikerade IP-adressen.

Din apps **Anpassad domän**-sida uppdateras med den nya dedikerade IP-adressen. [Kopiera den här IP-adressen](app-service-web-tutorial-custom-domain.md#info) och [mappa om A-posten](app-service-web-tutorial-custom-domain.md#map-an-a-record) till den nya IP-adressen.

<a name="test"></a>

## <a name="test-https"></a>Testa HTTPS

Allt som nu återstår är att kontrollera att HTTPS fungerar för din anpassade domän. Använd olika webbläsare och gå till `https://<your.custom.domain>` för att se att det fungerar med din app.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Om din app visar fel i certifikatverifieringen så använder du förmodligen ett självsignerat certifikat.
>
> Om detta inte är fallet kan du ha utelämnat mellanliggande certifikat när du exporterade certifikatet till PFX-filen.

## <a name="renew-certificates"></a>Förnya certifikat

Din inkommande IP-adress kan ändras när du tar bort en bindning, även om bindningen är IP-baserad. Detta är särskilt viktigt att ha i åtanke när du förnyar ett certifikat som redan finns i en IP-baserad bindning. Du kan förhindra att appens IP-adress ändras genom att följa stegen nedan i ordning:

1. Ladda upp det nya certifikatet.
2. Bind det nya certifikatet till önskad anpassad domän utan att ta bort det gamla. Med den här åtgärden ersätts bindningen i stället för att den gamla tas bort.
3. Ta bort det gamla certifikatet. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Använda HTTPS

Som standard kan alla fortfarande komma åt din app med HTTP. Du kan omdirigera alla HTTP-begäranden till HTTPS-porten.

Välj **SSL-inställningar** i den vänstra navigeringen på din appsida. I **Endast HTTPS** väljer du **På**.

![Använda HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

När åtgärden har slutförts går du till någon av de HTTP-webbadresser som pekar på din app. Exempel:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Framtvinga TLS-version

Din app tillåter [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 som standard, vilket är den rekommenderade TLS-nivån enligt branschstandarder, t.ex. [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Följ dessa steg om du vill genomdriva en högre TLS-version:

Välj **SSL-inställningar** i den vänstra navigeringen på din appsida. I **TLS-version** väljer du sedan den lägsta TLS-version du vill använda. Den här inställningen styr endast inkommande samtal. 

![Kräv TLS 1.1 eller 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

När åtgärden är klar avvisar appen alla anslutningar med lägre TLS-version.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera SSL-bindningar för din app med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Följande kommando laddar upp en exporterad PFX-fil och hämtar tumavtrycket.

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

Följande kommando lägger till en SNI-baserad SSL-bindning med tumavtrycket från föregående kommando.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Följande kommando tvingar användning av TLS version 1.2 eller senare.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande kommando laddar upp en exporterad PFX-fil och lägger till en SNI-baserad SSL-bindning.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Använda certifikat i din kod

Om din app behöver ansluta till fjärresurser och fjärresursen kräver certifikatautentisering, kan du ladda upp offentliga eller privata certifikat till din app. Du behöver inte att binda dessa certifikat till en anpassad domän i din app. Mer information finns i [Använda ett SSL-certifikat i programkoden i Azure App Service](app-service-web-ssl-cert-load.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Uppgradera appens prisnivå
> * Binda ditt anpassade certifikat till App Service
> * Förnya certifikat
> * Använda HTTPS
> * Använda TLS 1.1/1.2
> * Automatisera hantering av TLS med skript

Gå vidare till nästa självstudie om du vill lära dig hur du använder Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Lägga till ett Content Delivery Network (CDN) i en Azure App Service](../cdn/cdn-add-to-web-app.md)

Mer information finns i [Använda ett SSL-certifikat i programkoden i Azure App Service](app-service-web-ssl-cert-load.md).
