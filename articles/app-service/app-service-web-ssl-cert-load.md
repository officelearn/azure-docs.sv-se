---
title: Använd SSL-klientcertifikat i programkod – Azure App Service | Microsoft Docs
description: Lär dig hur du använder klientcertifikat för att ansluta till fjärranslutna resurser som kräver att användaren.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475121"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Använda ett SSL-certifikat i programkoden i Azure App Service

Den här guiden visar hur du använder offentliga eller privata certifikat i programkoden. Ett exempel på användningsfallet är att din app har åtkomst till en extern tjänst som kräver certifikatautentisering.

Den här metoden för att använda certifikat i din kod gör användning av SSL-funktioner i App Service, som kräver din app ska vara i **grundläggande** nivå eller senare. Du kan också [inkludera certifikatfilen i databasen för din](#load-certificate-from-file), men det rekommenderas inte för privata certifikat.

När du låta App Service hantera SSL-certifikat du underhålla certifikaten och din programkod separat och skydda känsliga data.

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

Innan du laddar upp ett privat certifikat, se till att [den uppfyller alla krav](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), förutom att det inte behöver vara konfigurerad för serverautentisering.

När du är redo att överföra, kör du följande kommando den <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Kopiera tumavtrycket för certifikatet och se [göra certifikatet tillgängligt](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Offentliga certifikat stöds i den *.cer* format. Ladda upp ett offentligt certifikat den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, och gå till din app.

Klicka på **SSL-inställningar** > **offentliga certifikat (.cer)**  > **ladda upp offentliga certifikat** från den vänstra navigeringen i din app.

I **namn**, Skriv ett namn för certifikatet. I **CER-certifikatfil**, Välj din CER-fil.

Klicka på **Överför**.

![Ladda upp offentliga certifikat](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

När certifikatet har överförts, kopiera tumavtrycket för certifikatet och se [göra certifikatet tillgängligt](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importera ett certifikat för App Service

Se [köp och konfigurera ett SSL-certifikat för Azure App Service](web-sites-purchase-ssl-web-site.md).

När du har importerat certifikatet kopiera tumavtrycket för certifikatet och se [göra certifikatet tillgängligt](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Gör certifikaten som är tillgänglig

Om du vill använda ett överförda eller importerade certifikat i din kod, se dess tumavtrycket är tillgängliga med den `WEBSITE_LOAD_CERTIFICATES` appinställningen, genom att köra följande kommando i den <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

För att göra alla certifikat som är tillgängliga, anger du värdet till `*`.

> [!NOTE]
> Den här inställningen placerar angivna certifikat i den [aktuella User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) store för de flesta prisnivåer, men den **isolerad** nivå (dvs. appen körs i en [App Service Environment](environment/intro.md)), placeras certifikat i den [lokala Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) lagra.
>

![Konfigurera app-inställning](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

När det är klart klickar du på **Spara**.

De konfigurera certifikat är nu redo att användas av din kod.

## <a name="load-the-certificate-in-code"></a>Läsa in certifikatet i kod

När ditt certifikat är tillgänglig kan visa du det i C#-kod genom tumavtrycket för certifikatet. Följande kod läser in ett certifikat med tumavtrycket `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Läs in certifikat från fil

Om du vill läsa in en certifikatfil från programkatalogen är det bättre att ladda upp den med [FTPS](deploy-ftp.md) i stället för [Git](deploy-local-git.md), till exempel. Du bör behålla känsliga data som ett privat certifikat från källkontroll.

Även om du läser in filen direkt i din .NET-kod, verifierar biblioteket fortfarande om den aktuella användarprofilen har lästs in. Om du vill läsa in den aktuella användarprofilen, ange den `WEBSITE_LOAD_USER_PROFILE` appinställningen med följande kommando i den <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

När den här inställningen, följande C# exempel läser in ett certifikat som kallas `mycert.pfx` från den `certs` för appens lagringsplats.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
