---
title: Binda ett befintligt anpassat SSL-certifikat till Azure Web Apps | Microsoft Docs
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
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 700465279a43a8490c5863e7181c4c4d009ee97b
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857932"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Självstudie: Binda ett befintligt anpassat SSL-certifikat till Azure Web Apps

Med Azure Web Apps får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. Den här självstudien visar hur du binder ett anpassat SSL-certifikat som du har köpt från en betrodd certifikatutfärdare till [Azure Web Apps](app-service-web-overview.md). När du är klar har du åtkomst till din webbapp på HTTPS-slutpunkten för din anpassade DNS-domän.

![Webbapp med anpassat SSL-certifikat](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppgradera appens prisnivå
> * Binda ditt anpassade certifikat till App Service
> * Förnya certifikat
> * Använda HTTPS
> * Använda TLS 1.1/1.2
> * Automatisera hantering av TLS med skript

> [!NOTE]
> Om du behöver ett anpassat SSL-certifikat kan du skaffa ett i Azure Portal direkt och binda det till din webbapp. Följ [Självstudie för App Service Certificate](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

- [Skapa en App Service-app](/azure/app-service/)
- [Mappa ett anpassat DNS-namn till din webbapp](app-service-web-tutorial-custom-domain.md)
- Skaffa ett SSL-certifikat från en betrodd certifikatutfärdare
- Använd samma privata nyckel som när du registrerade din begäran om SSL-certifikatet

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Krav för ditt SSL-certifikat

Om du vill använda ett certifikat i App Service måste certifikatet uppfylla alla följande krav:

* Vara signerat av en betrodd certifikatutfärdare
* Ha exporterats som en lösenordsskyddad PFX-fil
* Innehålla en privat nyckel som är minst 2 048 bitar lång
* Innehålla alla mellanliggande certifikat i certifikatkedjan

> [!NOTE]
> **ECC-certifikat (Elliptic Curve Cryptography)** kan fungera med App Service, men behandlas inte i den här artikeln. Din certifikatutfärdare kan visa hur du skapar ECC-certifikat.

## <a name="prepare-your-web-app"></a>Förbereda din webbapp

Om du vill binda ett anpassat SSL-certifikat till din webbapp måste din [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) finnas på nivån **Basic**, **Standard**, **Premium** eller **Isolerad**. I det här steget ser du till att webbappen har en prisnivå som stöds.

### <a name="log-in-to-azure"></a>Logga in på Azure

Öppna [Azure-portalen](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navigera till din webbapp

Klicka på **App Services** i menyn till vänster och sedan på namnet på din webbapp.

![Välja webbapp](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Du har kommit till hanteringssidan för din webbapp.  

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfältet på webbappsidan bläddrar du till avsnittet **Inställningar** och väljer **Skala upp (App Service-plan)**.

![Skala upp-menyn](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Kontrollera att webbappen inte är på nivån **F1** eller **D1**. Appens aktuella nivå markeras med en mörkblå ruta.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Anpassat SSL stöds inte på nivån **F1** eller **D1**. Om du behöver skala upp följer du stegen i nästa avsnitt. I annat fall stänger du sidan **Skala upp** och går vidare till [Ladda upp och binda SSL-certifikat](#upload).

### <a name="scale-up-your-app-service-plan"></a>Skala upp App Service-planen

Välj någon av betalnivåerna (**B1**, **B2**, **B3** eller någon nivå i kategorin **Produktion**). Klicka på **Visa ytterligare alternativ** om du vill se fler alternativ.

Klicka på **Verkställ**.

![Välja prisnivå](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

När du ser följande meddelande har skalningsåtgärden slutförts.

![Uppskalningsmeddelande](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Binda SSL-certifikatet

Du är redo att ladda upp SSL-certifikatet till din webbapp.

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

### <a name="upload-your-ssl-certificate"></a>Ladda upp SSL-certifikatet

Om du vill ladda upp SSL-certifikatet klickar du på **SSL-inställningar** i den vänstra navigeringen i webbappen.

Klicka på **Ladda upp certifikat**. 

I **PFX-certifikatsfil** väljer du din PFX-fil. I **Certifikatslösenord** skriver du lösenordet som du skapade när du exporterade PFX-filen.

Klicka på **Överför**.

![Överför certifikat](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

När App Service har överfört certifikatet visas det på sidan **SSL-inställningar**.

![Certifikatet har laddats upp](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Binda SSL-certifikatet

I avsnittet **SSL-bindningar** klickar du på **Lägg till bindning**.

På sidan **Lägg till SSL-bindning** använder du listrutorna och väljer det domännamn som ska skyddas och det certifikat som ska användas.

> [!NOTE]
> Om du har laddat upp ett certifikat men inte ser domänens namn i listrutan**Värdnamn**, kan du försöka med att uppdatera webbläsarsidan.
>
>

I **SSL-typ** väljer du om du vill använda **[Servernamnindikator (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** eller IP-baserad SSL.

- **SNI-baserad SSL** – Du kan lägga till flera SNI-baserade SSL-bindningar. Med det här alternativet kan flera SSL-certifikat skydda flera domäner på samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (mer information om webbläsare som stöds finns i [Servernamnindikator](http://wikipedia.org/wiki/Server_Name_Indication)).
- **IP-baserad SSL** – Det går bara att lägga till en IP-baserad SSL-bindning. Med det här alternativet tillåts endast ett SSL-certifikat för att skydda en dedikerad offentlig IP-adress. Om du vill skydda flera domäner, måste du skydda alla med samma SSL-certifikat. Detta är det vanligaste alternativet för SSL-bindning.

Klicka på **Lägg till bindning**.

![Binda SSL-certifikat](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

När App Service har laddat upp certifikatet, visas det i avsnitten **SSL-bindningar**.

![Certifikat som är bundet till en webbapp](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Mappa om en A-post för IP SSL

Om du inte använder IP-baserad SSL i din webbapp, kan du gå vidare till [Testa HTTPS för din anpassade domän](#test).

Som standard använder webbappen en delad offentlig IP-adress. När du binder ett certifikat med IP-baserad SSL, skapar App Service en ny dedikerad IP-adress för din webbapp.

Om du har mappat en A-post till din webbapp, uppdaterar du domänregistret med den nya dedikerade IP-adressen.

Webbappsidan **Anpassad domän** uppdateras med den nya dedikerade IP-adressen. [Kopiera den här IP-adressen](app-service-web-tutorial-custom-domain.md#info) och [mappa om A-posten](app-service-web-tutorial-custom-domain.md#map-an-a-record) till den nya IP-adressen.

<a name="test"></a>

## <a name="test-https"></a>Testa HTTPS

Allt som nu återstår är att kontrollera att HTTPS fungerar för din anpassade domän. Använd olika webbläsare och gå till `https://<your.custom.domain>` för att se att det fungerar med din webbapp.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Om din webbapp visar fel i certifikatverifieringen, använder du förmodligen ett självsignerat certifikat.
>
> Om detta inte är fallet kan du ha utelämnat mellanliggande certifikat när du exporterade certifikatet till PFX-filen.

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>Förnya certifikat

Din inkommande IP-adress kan ändras när du tar bort en bindning, även om bindningen är IP-baserad. Detta är särskilt viktigt att ha i åtanke när du förnyar ett certifikat som redan finns i en IP-baserad bindning. Du kan förhindra att appens IP-adress ändras genom att följa stegen nedan i ordning:

1. Ladda upp det nya certifikatet.
2. Bind det nya certifikatet till önskad anpassad domän utan att ta bort det gamla. Med den här åtgärden ersätts bindningen i stället för att den gamla tas bort.
3. Ta bort det gamla certifikatet. 

## <a name="enforce-https"></a>Använda HTTPS

Som standard kan alla fortfarande komma åt webbappen med hjälp av HTTP. Du kan omdirigera alla HTTP-begäranden till HTTPS-porten.

Välj **SSL-inställningar** i den vänstra navigeringen på webbappsidan. I **Endast HTTPS** väljer du **På**.

![Använda HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

När åtgärden har slutförts går du till någon av de HTTP-webbadresser som pekar på din app. Exempel:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Framtvinga TLS-version

Din app tillåter [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 som standard, vilket är den rekommenderade TLS-nivån enligt branschstandarder, t.ex. [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Följ dessa steg om du vill genomdriva en högre TLS-version:

Välj **SSL-inställningar** i den vänstra navigeringen på webbappsidan. I **TLS-version** väljer du sedan den lägsta TLS-version du vill använda. Den här inställningen styr endast inkommande samtal. 

![Kräv TLS 1.1 eller 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

När åtgärden är klar avvisar appen alla anslutningar med lägre TLS-version.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera SSL-bindningar för webbappen med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Följande kommando laddar upp en exporterad PFX-fil och hämtar tumavtrycket.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Följande kommando lägger till en SNI-baserad SSL-bindning med tumavtrycket från föregående kommando.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

Följande kommando tvingar användning av TLS version 1.2 eller senare.

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

Följande kommando laddar upp en exporterad PFX-fil och lägger till en SNI-baserad SSL-bindning.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Offentliga certifikat (valfritt)
Du kan ladda upp [offentliga certifikat](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) till din webbapp så att appen kan ansluta till en extern tjänst som kräver certifikatautentisering.  Mer information om hur du läser in och använder ett offentligt certifikat i din app finns i [Använda ett SSL-certifikat i programkoden i Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-ssl-cert-load).  Du kan även använda offentliga certifikat med appar i App Service Environment. Om du behöver lagra certifikatet i certifikatarkivet LocalMachine måste du använda en webbapp i App Service Environment. Mer information finns i [Så här konfigurerar du offentliga certifikat till din webbapp](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Ladda upp ett offentligt certifikat](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

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
