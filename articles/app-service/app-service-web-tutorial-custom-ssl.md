---
title: Bind ett befintligt anpassat SSL-certifikat till Azure Web Apps | Microsoft Docs
description: "Lär dig att binda ett anpassat SSL-certifikat till ditt webbprogram, mobilappsserverdel eller API-app i Azure App Service."
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f69bc731b2858c338d7f7b4d347e7107a0f4eeed
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Bind ett befintligt anpassat SSL-certifikat till Azure Web Apps

Azure Web Apps ger en mycket skalbar, automatisk uppdatering värdtjänst. Den här kursen visar hur du binda ett anpassat SSL-certifikat som du har köpt från en betrodd certifikatutfärdare att [Azure Web Apps](app-service-web-overview.md). När du är klar kan vara du tillgång till ditt webbprogram på HTTPS-slutpunkten för din anpassade DNS-domän.

![Webbprogram med anpassade SSL-certifikat](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Uppgradera prisnivån för din app
> * Koppla dina anpassade SSL-certifikatet till App Service
> * Använd HTTPS för din app
> * Automatisera SSL-certifikat-bindning med skript

> [!NOTE]
> Om du behöver en anpassad SSL-certifikat kan du skaffa en Azure-portalen direkt och binda den till ditt webbprogram. Följ den [Apptjänstcertifikat kursen](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- [Skapa en Apptjänst-app](/azure/app-service/)
- [Mappa ett anpassat DNS-namn till ditt webbprogram](app-service-web-tutorial-custom-domain.md)
- Skaffa ett SSL-certifikat från en betrodd certifikatutfärdare
- En privat nyckel som du använde för att logga en begäran för SSL-certifikat

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Krav för SSL-certifikat

Om du vill använda ett certifikat i App Service uppfyller certifikatet följande krav:

* Signerats av en betrodd certifikatutfärdare
* Exporteras som en lösenordsskyddad PFX-fil
* Innehåller privat nyckel minst 2048 bitar långt
* Innehåller alla mellanliggande certifikat i certifikatkedjan

> [!NOTE]
> **Elliptic Curve Cryptography (ECC) certifikat** kan arbeta med App Service men omfattas inte av den här artikeln. Arbeta med din certifikatutfärdare på exakt hur du skapar ECC-certifikat.

## <a name="prepare-your-web-app"></a>Förbered ditt webbprogram

Binda ett anpassat SSL-certifikat till ditt webbprogram din [programtjänstplanen](https://azure.microsoft.com/pricing/details/app-service/) måste finnas i den **grundläggande**, **Standard**, eller **Premium** nivå. I det här steget ska kontrollera du att ditt webbprogram är i det prisnivån.

### <a name="log-in-to-azure"></a>Logga in på Azure

Öppna [Azure-portalen](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navigera till ditt webbprogram

I den vänstra menyn klickar du på **Apptjänster**, och klicka sedan på namnet på ditt webbprogram.

![Välj webbprogram](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Du har landat i sidan för hantering av ditt webbprogram.  

### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

I det vänstra navigeringsfönstret på webbsidan för appen, bläddra till den **inställningar** avsnittet och väljer **skala upp (apptjänstplan)**.

![Skala-menyn](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Kontrollera att ditt webbprogram inte är i den **lediga** eller **delade** nivå. Ditt webbprogram aktuell nivå markeras med en mörk ruta.

![Kontrollera prisnivå](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Anpassat SSL stöds inte i den **lediga** eller **delade** nivå. Om du behöver skala upp, följer du stegen i nästa avsnitt. I annat fall stänger den **Välj din prisnivå** sidan och gå vidare till [överför och binda SSL-certifikat](#upload).

### <a name="scale-up-your-app-service-plan"></a>Skala upp din programtjänstplan

Välj en av de **grundläggande**, **Standard**, eller **Premium** nivåer.

Klicka på **Välj**.

![Välj prisnivå](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

När du ser följande meddelande har åtgärden slutförts.

![Skala upp meddelande](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Binda SSL-certifikat

Du är redo att överföra SSL-certifikatet till ditt webbprogram.

### <a name="merge-intermediate-certificates"></a>Sammanfoga mellanliggande certifikat

Om din certifikatutfärdare ger flera certifikat i certifikatkedjan, måste du koppla certifikat i ordning. 

Gör detta genom att öppna varje certifikat som du fick i en textredigerare. 

Skapa en fil för den kopplade certifikat kallas _mergedcertificate.crt_. Kopiera innehållet i varje certifikat till den här filen i en textredigerare. Ordningen på dina certifikat bör följa ordningen i certifikatkedjan börjar med ditt certifikat och slutar med rotcertifikatet. Det ser ut som följande exempel:

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

### <a name="export-certificate-to-pfx"></a>Exportera certifikatet till PFX

Exportera sammanfogade SSL-certifikat med privat nyckel som genererades certifikatbegäran med.

Om du genererade din certifikatbegäran med hjälp av OpenSSL har du skapat en fil för privat nyckel. Kör följande kommando för att exportera certifikatet till PFX. Ersätt platshållarna  _&lt;fil för privat nyckel >_ och  _&lt;samman certifikatfil >_ med sökvägar till din privata nyckel och din kopplade certifikatfil.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

När du uppmanas ange ett lösenord för export. Du ska använda det här lösenordet när du senare överför din SSL-certifikatet till App Service.

Om du använder IIS eller _Certreq.exe_ generera certifikatbegäran genom att installera certifikatet på den lokala datorn och sedan [exportera certifikatet till PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Överför SSL-certifikat

Om du vill överföra SSL-certifikat klickar du på **SSL-certifikat** i navigeringen till vänster i ditt webbprogram.

Klicka på **överför certifikat**. 

I **PFX-certifikatsfilen**, Välj din PFX-fil. I **certifikatlösenord**, Skriv in lösenordet som du skapade när du exporterade PFX-filen.

Klicka på **Överför**.

![Överför certifikat](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

När Apptjänst har överförts certifikatet, visas den i den **SSL-certifikat** sidan.

![Certifikat som har överförts](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Binda SSL-certifikat

I den **SSL-bindningar** klickar du på **bindning**.

I den **lägger till SSL-bindningen** använder nedrullningsbara listorna och välj namnet på en domän att skydda och certifikat som ska användas.

> [!NOTE]
> Om du har överfört ett certifikat, men inte ser domänens namn i den **värdnamn** listrutan försök att uppdatera sidan webbläsare.
>
>

I **SSL typen**, Välj om du vill använda  **[Server Servernamnsindikation (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**  eller IP-baserade SSL.

- **SNI-baserade SSL** -flera SNI-baserade SSL-bindningar kan läggas till. Det här alternativet kan flera SSL-certifikat att skydda flera domäner i samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (supportinformation finns mer omfattande webbläsare på [Servernamnindikator](http://wikipedia.org/wiki/Server_Name_Indication)).
- **IP-baserade SSL** – en enda IP-baserade SSL-bindning kan läggas till. Det här alternativet kan endast en SSL-certifikat för säker dedikerade offentlig IP-adress. Om du vill skydda flera domäner, måste du skydda dem alla använder samma SSL-certifikat. Detta är det traditionella alternativet för SSL-bindning.

Klicka på **bindning**.

![Binda SSL-certifikat](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

När Apptjänst har överförts certifikatet, visas den i den **SSL-bindningar** avsnitt.

![Certifikatet som är bundet till webbprogram](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Mappa om en post för IP-SSL

Om du inte använder IP-baserade SSL i ditt webbprogram, går du till [Test HTTPS för den anpassade domänen](#test).

Som standard används ditt webbprogram delad offentlig IP-adress. När du binda ett certifikat med IP-baserade SSL skapar App Service en ny, dedicerade IP-adress för din webbapp.

Om du har mappat en A-post till ditt webbprogram, Uppdatera registret domän med den här nya, dedikerade IP-adress.

Ditt webbprogram **anpassad domän** sidan uppdateras med den nya, dedikerade IP-adressen. [Kopiera den här IP-adressen](app-service-web-tutorial-custom-domain.md#info), sedan [mappa om A-posten](app-service-web-tutorial-custom-domain.md#map-an-a-record) till den nya IP-adressen.

<a name="test"></a>

## <a name="test-https"></a>Testa HTTPS

Nu är återstår gör att HTTPS fungerar för domänen. Olika webbläsare, bläddra till `https://<your.custom.domain>` att se att den fungerar upp ditt webbprogram.

![Portalen navigering till Azure-app](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Om ditt webbprogram ger du certifikat verifieringsfel, använder du förmodligen ett självsignerat certifikat.
>
> Om detta inte är fallet, kan du ha utelämnat mellanliggande certifikat när du exporterar certifikatet i PFX-filen.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Använda HTTPS

Som standard alla fortfarande komma åt ditt webbprogram med hjälp av HTTP. Du kan omdirigera alla HTTP-begäranden till HTTPS-porten.

Välj i din app webbsida, i det vänstra navigeringsfönstret **anpassade domäner**. I **endast HTTPS**väljer **på**.

![Använda HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

När åtgärden har slutförts, navigera till alla HTTP-URL: er som pekar på din app. Exempel:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera SSL-bindningar för ditt webbprogram med skript, med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>Azure CLI

Följande kommando överför exporterade PFX-filen och hämtar certifikatets tumavtryck.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Följande kommando lägger till en SNI-baserade SSL-bindning som använder certifikatets tumavtryck från det föregående kommandot.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

Följande kommando överför exporterade PFX-filen och lägger till en SNI-baserade SSL-bindning.

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
Du kan ladda upp [offentliga certifikat](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) till ditt webbprogram. Du kan använda certifikat för offentlig för appar i Apptjänstmiljöer också. Om du behöver lagra certifikatet i arkivet LocalMachine certifikat som du behöver använda ett webbprogram på Apptjänst-miljö. Mer information finns i [hur du konfigurerar offentliga certifikat till ditt webbprogram](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer).

![Överför offentliga certifikat](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Uppgradera prisnivån för din app
> * Koppla dina anpassade SSL-certifikatet till App Service
> * Använd HTTPS för din app
> * Automatisera SSL-certifikat-bindning med skript

Gå vidare till nästa kurs att lära dig hur du använder Azure Content Delivery Network.

> [!div class="nextstepaction"]
> [Lägg till ett innehållsleveransnätverk (CDN) till en Azure App Service](app-service-web-tutorial-content-delivery-network.md)

Mer information finns i [använder SSL-certifikat i din programkod i Azure App Service](app-service-web-ssl-cert-load.md).