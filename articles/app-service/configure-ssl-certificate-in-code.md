---
title: Använd SSL-certifikat i kod-Azure App Service | Microsoft Docs
description: Lär dig hur du använder klient certifikat för att ansluta till fjär resurser som kräver dem.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1546ded1977e1e26792189e1d992d106d3d77ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271289"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Använd ett SSL-certifikat i koden i Azure App Service

I program koden kan du komma åt [offentliga eller privata certifikat som du lägger till i App Service](configure-ssl-certificate.md). Din app-kod kan fungera som en klient och komma åt en extern tjänst som kräver certifikatautentisering, eller så kan den behöva utföra kryptografiska uppgifter. Den här instruktions guiden visar hur du använder offentliga eller privata certifikat i program koden.

Den här metoden för att använda certifikat i din kod använder SSL-funktioner i App Service, vilket kräver att din app är på **Basic** -nivå eller högre. Om din app finns på **kostnads fri** eller **delad** nivå kan du [inkludera certifikat filen i din app-lagringsplats](#load-certificate-from-file).

När du låter App Service hantera dina SSL-certifikat kan du underhålla certifikaten och program koden separat och skydda känsliga data.

## <a name="prerequisites"></a>Förutsättningar

För att följa den här instruktions guiden:

- [Skapa en App Service-app](/azure/app-service/)
- [Lägg till ett certifikat i appen](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Hitta tumavtrycket

I <a href="https://portal.azure.com" target="_blank">Azure Portal</a>väljer du **app Services** >  **\<App-Name >** på menyn till vänster.

Välj **TLS/SSL-inställningar**i den vänstra navigeringen i din app och välj sedan **privat nyckel certifikat (. pfx)** eller **certifikat för offentlig nyckel (. cer)** .

Hitta det certifikat som du vill använda och kopiera tumavtrycket.

![Kopiera certifikatets tumavtryck](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Gör certifikatet tillgängligt

Om du vill komma åt ett certifikat i din app-kod lägger du till dess tumavtryck i `WEBSITE_LOAD_CERTIFICATES` app-inställningen genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Om du vill göra alla dina certifikat tillgängliga, ställer du in värdet på `*`.

## <a name="load-certificate-in-windows-apps"></a>Läs in certifikat i Windows-appar

Inställningen `WEBSITE_LOAD_CERTIFICATES` app gör de angivna certifikaten tillgängliga för din Windows-värdbaserade app i Windows certifikat Arkiv och platsen är beroende av [pris nivån](overview-hosting-plans.md):

- **Isolerad** nivå – i [lokalt Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Alla andra nivåer – i [aktuell User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

I C# kod får du åtkomst till certifikatet med tumavtrycket för certifikatet. Följande kod läser in ett certifikat med tumavtrycket `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

I Java-kod får du åtkomst till certifikatet från "Windows-MY"-butiken med hjälp av fältet eget namn på certifikat mottagare (se [certifikat för offentlig nyckel](https://en.wikipedia.org/wiki/Public_key_certificate)). Följande kod visar hur du läser in ett privat nyckel certifikat:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

För språk som inte stöder eller som inte erbjuder stöd för Windows certifikat Arkiv, se [Läs in certifikat från fil](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Läs in certifikat i Linux-appar

Inställningarna för `WEBSITE_LOAD_CERTIFICATES` app gör de angivna certifikaten tillgängliga för dina Linux-värdbaserade appar (inklusive anpassade behållar appar) som filer. Filerna finns under följande kataloger:

- Privata certifikat – `/var/ssl/private` (`.p12` filer)
- Offentliga certifikat – `/var/ssl/certs` (`.der` filer)

Certifikat fil namnen är tumavtrycken. Följande C# kod visar hur du läser in ett offentligt certifikat i en Linux-app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Information om hur du läser in ett SSL-certifikat från en fil i Node. js, PHP, python, Java eller ruby finns i dokumentationen för respektive språk eller webb plattform.

## <a name="load-certificate-from-file"></a>Läs in certifikat från fil

Om du behöver läsa in en certifikat fil som du överför manuellt är det bättre att ladda upp certifikatet med [FTPS](deploy-ftp.md) i stället för [git](deploy-local-git.md), till exempel. Du bör behålla känsliga data som ett privat certifikat från käll kontroll.

> [!NOTE]
> ASP.NET och ASP.NET Core i Windows måste komma åt certifikat arkivet även om du läser in ett certifikat från en fil. Om du vill läsa in en certifikat fil i en Windows .NET-app läser du in den aktuella användar profilen med följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Den här metoden för att använda certifikat i din kod använder SSL-funktioner i App Service, vilket kräver att din app är på **Basic** -nivå eller högre.

I följande C# exempel läses ett offentligt certifikat från en relativ sökväg i appen:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Information om hur du läser in ett SSL-certifikat från en fil i Node. js, PHP, python, Java eller ruby finns i dokumentationen för respektive språk eller webb plattform.

## <a name="more-resources"></a>Fler resurser

* [Skydda ett anpassat DNS-namn med en SSL-bindning](configure-ssl-bindings.md)
* [Använda HTTPS](configure-ssl-bindings.md#enforce-https)
* [Framtvinga TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Vanliga frågor och svar: App Service certifikat](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
