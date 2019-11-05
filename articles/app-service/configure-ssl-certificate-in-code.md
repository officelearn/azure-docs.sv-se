---
title: Använd SSL-certifikat i program kod – Azure App Service | Microsoft Docs
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
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513697"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Använd ett SSL-certifikat i program koden i Azure App Service

Din App Service app-kod kan fungera som en klient och åtkomst till en extern tjänst som kräver certifikatautentisering. Den här instruktions guiden visar hur du använder offentliga eller privata certifikat i program koden.

Den här metoden för att använda certifikat i din kod använder SSL-funktioner i App Service, vilket kräver att din app är på **Basic** -nivå eller högre. Du kan också [inkludera certifikat filen i din app-lagringsplats](#load-certificate-from-file), men det är inte en rekommenderad metod för privata certifikat.

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

## <a name="load-the-certificate"></a>Läs in certifikatet

Om du vill använda ett certifikat i din app-kod lägger du till dess tumavtryck i `WEBSITE_LOAD_CERTIFICATES` app-inställningen genom att köra följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Om du vill göra alla dina certifikat tillgängliga, ställer du in värdet på `*`.

> [!NOTE]
> Den här inställningen placerar de angivna certifikaten i den [aktuella User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) -butiken för de flesta pris nivåer, men i den **isolerade** nivån (d.v.s. appen körs i en [App Service-miljön](environment/intro.md)) placeras certifikaten i den [lokala Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) auktoriseringsarkiv.
>

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

Även om du läser in filen direkt i din .NET-kod, verifierar biblioteket fortfarande om den aktuella användar profilen har lästs in. Om du vill läsa in den aktuella användar profilen anger du inställningen `WEBSITE_LOAD_USER_PROFILE` app med följande kommando i <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

När den här inställningen har angetts läser följande C# exempel in ett certifikat med namnet `mycert.pfx` från katalogen `certs` i appens lagrings plats.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>Fler resurser

* [Skydda ett anpassat DNS-namn med en SSL-bindning](configure-ssl-bindings.md)
* [Använda HTTPS](configure-ssl-bindings.md#enforce-https)
* [Framtvinga TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Vanliga frågor och svar: App Service certifikat](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
