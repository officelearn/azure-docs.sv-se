---
title: Använd SSL-klientcertifikat i programkod – Azure App Service | Microsoft Docs
description: ''
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
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8041d86e21f464e1dee4fba543be7d2c54c10367
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254334"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Använda ett SSL-certifikat i programkoden i Azure App Service

Den här guiden visar hur du använder en SSL-certifikat som du har laddat upp eller importerat till din App Service-app i programkoden. Ett exempel på användningsfallet är att din app har åtkomst till en extern tjänst som kräver certifikatautentisering. 

Den här metoden för att använda SSL-certifikat i din kod gör användning av SSL-funktioner i App Service, som kräver din app ska vara i **grundläggande** nivå eller senare. Ett alternativ är att inkludera certifikatfilen i programkatalogen och läsa in den direkt (se [alternativa: Läs in certifikatet som en fil](#file)). Detta alternativ tillåter dock inte att du dölja den privata nyckeln i certifikatet från programkoden eller utvecklaren. Dessutom om din programkod är i en lagringsplats för öppen källkod, är som ett certifikat med en privat nyckel i databasen inte ett alternativ.

När du låta App Service hantera SSL-certifikat du underhålla certifikaten och din programkod separat och skydda känsliga data.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här guiden:

- [Skapa en App Service-app](/azure/app-service/)
- [Mappa ett anpassat DNS-namn till din webbapp](app-service-web-tutorial-custom-domain.md)
- [Överför ett SSL-certifikat](app-service-web-tutorial-custom-ssl.md) eller [importera ett App Service Certificate](web-sites-purchase-ssl-web-site.md) till din webbapp


## <a name="load-your-certificates"></a>Läsa in dina certifikat

Om du vill använda ett certifikat som överförs till eller importerats till App Service måste först gör tillgänglig för din programkod. Du gör detta med den `WEBSITE_LOAD_CERTIFICATES` appinställningen.

I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna webbappens sida.

I det vänstra navigeringsfönstret klickar du på **SSL-certifikat**.

![Certifikatet har laddats upp](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Alla dina uppladdade och importerade SSL-certifikat för det här webbprogrammet visas här med sina tumavtryck. Kopiera tumavtrycket för certifikatet som du vill använda.

I det vänstra navigeringsfönstret klickar du på **programinställningar**.

Lägg till en app som heter `WEBSITE_LOAD_CERTIFICATES` och ange värdet till tumavtrycket för certifikatet. Använd tumavtryck för fil med kommaavgränsade värden för att göra flera certifikat tillgängligt. För att göra alla certifikat som är tillgängliga, anger du värdet till `*`. Notera det här placerar du certifikatet till den `CurrentUser\My` lagra.

![Konfigurera app-inställning](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

När det är klart klickar du på **Spara**.

Det konfigurerade certifikatet är nu redo att användas av din kod.

## <a name="use-certificate-in-c-code"></a>Använda certifikat i C#-kod

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
## <a name="alternative-load-certificate-as-a-file"></a>Alternativ: läsa in certifikatet som en fil

Det här avsnittet visas hur du och läsa in en certifikatfil som är i programkatalogen. 

I följande C#-exempel läser in ett certifikat som kallas `mycert.pfx` från den `certs` för appens lagringsplats.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

