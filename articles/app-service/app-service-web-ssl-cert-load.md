---
title: Använder överförda SSL-certifikat i din programkod i Azure App Service | Microsoft Docs
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
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
ms.locfileid: "26047702"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Om du Använd SSL-certifikat i din programkod i Azure App Service

Den här instruktioner visar hur du använder en SSL-certifikat som du har laddat upp eller importeras till din Apptjänst-app i din programkod. Ett exempel på användningsfallet är att appen ansluter till en extern tjänst som kräver autentisering med datorcertifikat. 

Den här metoden för att använda SSL-certifikat i koden gör användning av SSL-funktioner i Apptjänst som kräver din app i **grundläggande** nivå eller senare. Ett alternativ är att inkludera certifikatfilen i programkatalogen och Läs in den direkt (se [alternativa: Läs in certifikatet som en fil](#file)). Men kan det här alternativet du inte dölja den privata nyckeln i certifikatet från programkoden eller utvecklaren. Dessutom om din programkod finns i en databas med öppen källkod, är som ett certifikat med en privat nyckel i databasen inte ett alternativ.

När du gör Apptjänst hantera SSL-certifikat du underhåller certifikat och din programkod separat och skydda dina känsliga data.

## <a name="prerequisites"></a>Krav

Att slutföra den här instruktioner:

- [Skapa en Apptjänst-app](/azure/app-service/)
- [Mappa ett anpassat DNS-namn till ditt webbprogram](app-service-web-tutorial-custom-domain.md)
- [Ladda upp ett SSL-certifikat](app-service-web-tutorial-custom-ssl.md) eller [importera ett certifikat för App Service](web-sites-purchase-ssl-web-site.md) till ditt webbprogram


## <a name="load-your-certificates"></a>Läsa in dina certifikat

Om du vill använda ett certifikat som har överförts till eller importerats till Apptjänst först gör den tillgänglig för din programkod. Det gör du den `WEBSITE_LOAD_CERTIFICATES` appinställningen.

I den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>, öppna appen till webbsidan.

I det vänstra navigeringsfönstret klickar du på **SSL-certifikat**.

![Certifikat som har överförts](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Alla dina överföras och importerade SSL-certifikat för det här webbprogrammet visas här med deras tumavtryck. Kopiera tumavtrycket för certifikatet som du vill använda.

I det vänstra navigeringsfönstret klickar du på **programinställningar**.

Lägg till en app inställningen kallas `WEBSITE_LOAD_CERTIFICATES` och ange värdet till tumavtrycket för certifikatet. Använd CSV-tumavtryck värden för att göra flera certifikat tillgänglig. Om du vill göra alla certifikat som är tillgänglig, anger du värdet till `*`. 

![Konfigurera appinställningen för](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

När du är klar klickar du på **spara**.

Det konfigurerade certifikatet är nu redo att användas av din kod.

## <a name="use-certificate-in-c-code"></a>Använda certifikat i C#-kod

När ditt certifikat är tillgänglig kan du komma åt den i C#-kod genom tumavtrycket för certifikatet. Följande kod läser in ett certifikat med tumavtrycket `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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

Följande C#-exempel läser in ett certifikat som kallas `mycert.pfx` från den `certs` katalogen för din app lagringsplats.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

