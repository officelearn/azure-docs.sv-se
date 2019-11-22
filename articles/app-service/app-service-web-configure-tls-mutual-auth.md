---
title: Konfigurera ömsesidig TLS-autentisering – Azure App Service
description: Lär dig hur du konfigurerar din app för att använda autentisering med klient certifikat på TLS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a07fa597305771ed3f4da01f2819297fc9cd3d77
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271693"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Konfigurera ömsesidig TLS-autentisering för Azure App Service

Du kan begränsa åtkomsten till Azure App Service-appen genom att aktivera olika typer av autentisering för den. Ett sätt att göra det är att begära ett klient certifikat när klientbegäran är över TLS/SSL och validera certifikatet. Denna mekanism kallas TLS-ömsesidig autentisering eller autentisering av klient certifikat. Den här artikeln visar hur du konfigurerar din app för att använda autentisering med klient certifikat.

> [!NOTE]
> Om du ansluter till din webbplats via HTTP och inte HTTPS får du inga klient certifikat. Så om ditt program kräver klient certifikat bör du inte tillåta begär anden till ditt program via HTTP.
>

## <a name="enable-client-certificates"></a>Aktivera klient certifikat

Om du vill konfigurera din app så att den kräver klient certifikat måste du ange `clientCertEnabled` inställningen för din app för att `true`. Ange inställningen genom att köra följande kommando i [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>Uteslut sökvägar från att kräva autentisering

När du aktiverar ömsesidig autentisering för ditt program, kräver alla sökvägar under appens rot ett klient certifikat för åtkomst. Om du vill tillåta att vissa sökvägar är öppna för anonym åtkomst kan du definiera undantags Sök vägar som en del av program konfigurationen.

Du kan konfigurera undantags Sök vägar genom att välja **konfiguration** > **allmänna inställningar** och definiera en sökväg för undantag. I det här exemplet skulle något under `/public` sökväg för ditt program inte begära ett klient certifikat.

![Sökvägar för certifikat undantag][exclusion-paths]


## <a name="access-client-certificate"></a>Åtkomst till klient certifikat

I App Service sker SSL-avslutning av begäran på klient delens belastningsutjämnare. När du vidarebefordrar begäran till din app-kod med [aktiverade klient certifikat](#enable-client-certificates), infogar App Service ett `X-ARR-ClientCert` begär ande huvud med klient certifikatet. App Service gör ingenting med det här klient certifikatet annat än att vidarebefordra det till din app. Appens kod ansvarar för att verifiera klient certifikatet.

För ASP.NET är klient certifikatet tillgängligt via egenskapen **HttpRequest. ClientCertificate** .

För andra program stackar (Node. js, PHP osv.) är klient certifikatet tillgängligt i din app via ett base64-kodat värde i `X-ARR-ClientCert` begär ande huvudet.

## <a name="aspnet-sample"></a>ASP.NET-exempel

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Node. js-exempel

Följande Node. js-exempel kod hämtar `X-ARR-ClientCert`s huvudet och använder [Node-falska](https://github.com/digitalbazaar/forge) för att konvertera den base64-KODAde PEM-strängen till ett certifikat objekt och verifiera den:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png