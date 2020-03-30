---
title: Använda SSL-certifikat i kod
description: Läs om hur du använder klientcertifikat i koden. Autentisera med fjärrresurser med ett klientcertifikat eller kör kryptografiska uppgifter med dem.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d783b61c372c7d0f8cca13106bf297ab9b55c424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671883"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Använda ett SSL-certifikat i koden i Azure App Service

I programkoden kan du komma åt de [offentliga eller privata certifikat som du lägger till i App Service](configure-ssl-certificate.md). Din appkod kan fungera som en klient och komma åt en extern tjänst som kräver certifikatautentisering, eller så kan den behöva utföra kryptografiska uppgifter. Den här guiden visar hur du använder offentliga eller privata certifikat i programkoden.

Den här metoden för att använda certifikat i koden använder sig av SSL-funktionen i App Service, som kräver att appen är på **grundläggande** nivå eller högre. Om appen är på **den kostnadsfria** eller **delade** nivån kan du [inkludera certifikatfilen i appdatabasen](#load-certificate-from-file).

När du låter App Service hantera dina SSL-certifikat kan du underhålla certifikaten och programkoden separat och skydda dina känsliga data.

## <a name="prerequisites"></a>Krav

Så här följer du den här guiden:

- [Skapa en App Service-app](/azure/app-service/)
- [Lägga till ett certifikat i appen](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Hitta tumavtrycket

Välj App**\<Services->** **App Services** > på <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>på den vänstra menyn .

Välj **TLS/SSL-inställningar**från den vänstra navigeringen i appen och välj sedan **Privata nyckelcertifikat (.pfx)** eller **certifikat för offentlig nyckel (.cer).**

Leta reda på det certifikat du vill använda och kopiera tumavtrycket.

![Kopiera certifikatets tumavtryck](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Göra certifikatet tillgängligt

Om du vill komma åt ett certifikat i `WEBSITE_LOAD_CERTIFICATES` appkoden lägger du till dess tumavtryck i appinställningen genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Om du vill göra alla certifikat `*`tillgängliga anger du värdet på .

## <a name="load-certificate-in-windows-apps"></a>Läsa in certifikat i Windows-appar

Appinställningen `WEBSITE_LOAD_CERTIFICATES` gör de angivna certifikaten tillgängliga för din Windows-värdapp i Windows-certifikatarkivet, och platsen beror på [prisnivån:](overview-hosting-plans.md)

- **Isolerad** nivå - i [Lokal dator\Min](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Alla andra nivåer - i [Aktuell användare\Min](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

I C#-kod kommer du åt certifikatet med certifikatets tumavtryck. Följande kod läser in ett `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`certifikat med tumavtrycket .

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

I Java-kod kommer du åt certifikatet från arkivet "Windows-MY" med fältet Ämnes gemensamt namn (se [Certifikat för offentlig nyckel).](https://en.wikipedia.org/wiki/Public_key_certificate) Följande kod visar hur du läser in ett privat nyckelcertifikat:

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

Språk som inte stöder eller erbjuder otillräckligt stöd för Windows-certifikatarkivet finns [i Läsa in certifikat från filen](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Ladda certifikat i Linux-appar

Appinställningarna `WEBSITE_LOAD_CERTIFICATES` gör de angivna certifikaten tillgängliga för dina Linux-värdbaserade appar (inklusive anpassade behållarappar) som filer. Filerna hittas under följande kataloger:

- Privata certifikat `/var/ssl/private` - `.p12` ( filer)
- Offentliga certifikat `/var/ssl/certs` - `.der` ( filer)

Certifikatfilsnamnen är certifikatets tumavtryck. Följande C#-kod visar hur du läser in ett offentligt certifikat i en Linux-app.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Mer om du vill se hur du läser in ett SSL-certifikat från en fil i Node.js, PHP, Python, Java eller Ruby finns i dokumentationen för respektive språk- eller webbplattform.

## <a name="load-certificate-from-file"></a>Läsa in certifikat från fil

Om du behöver läsa in en certifikatfil som du laddar upp manuellt är det bättre att ladda upp certifikatet med [FTPS](deploy-ftp.md) i stället för [Git,](deploy-local-git.md)till exempel. Du bör hålla känsliga data som ett privat certifikat utanför källkontrollen.

> [!NOTE]
> ASP.NET och ASP.NET Core i Windows måste komma åt certifikatarkivet även om du läser in ett certifikat från en fil. Om du vill läsa in en certifikatfil i en Windows .NET-app läser du in den aktuella användarprofilen med följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Den här metoden för att använda certifikat i koden använder sig av SSL-funktionen i App Service, som kräver att appen är på **grundläggande** nivå eller högre.

I följande C#-exempel läses ett offentligt certifikat från en relativ sökväg i appen:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Mer om du vill se hur du läser in ett SSL-certifikat från en fil i Node.js, PHP, Python, Java eller Ruby finns i dokumentationen för respektive språk- eller webbplattform.

## <a name="more-resources"></a>Fler resurser

* [Skydda ett anpassat DNS-namn med en SSL-bindning](configure-ssl-bindings.md)
* [Använda HTTPS](configure-ssl-bindings.md#enforce-https)
* [Använda TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Vanliga frågor och svar : App Service-certifikat](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
