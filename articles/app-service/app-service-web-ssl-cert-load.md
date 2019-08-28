---
title: Använd klient-SSL-certifikat i program kod-Azure App Service | Microsoft Docs
description: Lär dig hur du använder klient certifikat för att ansluta till fjär resurser som kräver dem.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6820daf34e63fd48e83c645e7509a3256bc8435b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066995"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Använd ett SSL-certifikat i program koden i Azure App Service

Den här instruktions guiden visar hur du använder offentliga eller privata certifikat i program koden. Ett exempel på användnings fallet är att appen ansluter till en extern tjänst som kräver certifikatautentisering.

Den här metoden för att använda certifikat i din kod använder SSL-funktioner i App Service, vilket kräver att din app är på **Basic** -nivå eller högre. Du kan också [inkludera certifikat filen i din app-lagringsplats](#load-certificate-from-file), men det är inte en rekommenderad metod för privata certifikat.

När du låter App Service hantera dina SSL-certifikat kan du underhålla certifikaten och program koden separat och skydda känsliga data.

## <a name="upload-a-private-certificate"></a>Ladda upp ett privat certifikat

Innan du överför ett privat certifikat måste du kontrol lera att [det uppfyller alla krav](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), förutom att det inte behöver konfigureras för serverautentisering.

När du är redo att ladda upp kör du följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Kopiera certifikatets tumavtryck och se [göra certifikatet tillgängligt](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Ladda upp ett offentligt certifikat

Offentliga certifikat stöds i *. cer* -format. För att ladda upp ett offentligt certifikat, <a href="https://portal.azure.com" target="_blank">Azure Portal</a>och navigera till din app.

Klicka **på SSL-inställningar** > **offentliga certifikat (. cer)**  > **Ladda upp offentligt certifikat** från den vänstra navigeringen i din app.

I **namn**anger du ett namn för certifikatet. I **CER-certifikatfil**väljer du CER-filen.

Klicka på **Överför**.

![Ladda upp offentligt certifikat](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

När certifikatet har laddats upp kopierar du tumavtrycket för certifikatet och ser [till att det blir tillgängligt för certifikatet](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importera ett App Service-certifikat

Se [köpa och konfigurera ett SSL-certifikat för Azure App Service](web-sites-purchase-ssl-web-site.md).

När certifikatet har importer ATS kopierar du tumavtrycket för certifikatet och ser [till att certifikatet är tillgängligt](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Gör certifikatet tillgängligt

Om du vill använda ett uppladdat eller importerat certifikat i appens kod gör du `WEBSITE_LOAD_CERTIFICATES` dess tumavtryck tillgängligt med appens inställning genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Ange värdet till `*`om du vill göra alla dina certifikat tillgängliga.

> [!NOTE]
> Den här inställningen placerar de angivna certifikaten i den [aktuella User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) -butiken för de flesta pris nivåer, men i den isolerade nivån (d.v.s. appen körs i en [App Service-miljön](environment/intro.md)) placeras certifikaten i den [lokala Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) auktoriseringsarkiv.
>

![Konfigurera app-inställning](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

När det är klart klickar du på **Spara**.

De konfigurerade certifikaten är nu redo att användas av din kod.

## <a name="load-the-certificate-in-code"></a>Läs in certifikatet i kod

När ditt certifikat är tillgängligt kan du komma åt det C# i kod av certifikatets tumavtryck. Följande kod läser in ett certifikat med tumavtrycket `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

Om du behöver läsa in en certifikat fil från program katalogen är det bättre att ladda upp den med [FTPS](deploy-ftp.md) i stället för [git](deploy-local-git.md), till exempel. Du bör behålla känsliga data som ett privat certifikat från käll kontroll.

Även om du läser in filen direkt i din .NET-kod, verifierar biblioteket fortfarande om den aktuella användar profilen har lästs in. Om du vill läsa in den aktuella användar profilen `WEBSITE_LOAD_USER_PROFILE` anger du appens inställning med följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

När den här inställningen har angetts läser följande C# exempel in ett certifikat som `mycert.pfx` kallas från `certs` katalogen i appens lagrings plats.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
