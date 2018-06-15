---
title: Konfigurera TLS ömsesidig autentisering för webbapp
description: Lär dig hur du konfigurerar ditt webbprogram om du vill använda klientcertifikatautentisering på TLS.
services: app-service
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836310"
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Konfigurera TLS ömsesidig autentisering för webbapp
## <a name="overview"></a>Översikt
Du kan begränsa åtkomsten till din Azure-webbapp genom att aktivera olika typer av autentisering för den. Ett sätt att göra detta är att autentisera med ett klientcertifikat när begäran är över TLS/SSL. Den här mekanismen kallas TLS ömsesidig autentisering eller autentisering och den här artikeln kommer innehåller information om hur du ställer in ditt webbprogram om du vill använda klientcertifikatautentisering klientcertifikat.

> **Obs:** om du har åtkomst till webbplatsen via HTTP och HTTPS inte visas inte alla klientcertifikat. Så om ditt program kräver klientcertifikat bör du inte tillåta begäranden till ditt program via HTTP.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Konfigurera Webbappen för autentisering av klientcertifikat
Om du vill konfigurera ditt webbprogram för att kräva klientcertifikat måste du lägga till clientCertEnabled-platsinställningen för ditt webbprogram och angetts till true. Den här inställningen är inte tillgänglig via hanteringen av portalen och REST-API behöver för att åstadkomma detta.

Du kan använda den [ARMClient verktyget](https://github.com/projectkudu/ARMClient) att göra det enkelt att skapa REST API-anrop. När du har loggat in med verktyget måste du följande kommando:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

Ersätt allt i {} med information för ditt webbprogram och skapa en fil med namnet enableclientcert.json med följande JSON innehåll:

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Se till att ändra värdet för ”plats” till var ditt webbprogram finns t.ex. norra centrala USA eller västra USA osv.

Du kan också använda https://resources.azure.com för att vända de `clientCertEnabled` egenskapen `true`.

> **Obs:** om du kör ARMClient från Powershell, behöver du undanta den @-tecknet för JSON-fil med en bakre skalstreck '.
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Åtkomst till klientens certifikat från ditt webbprogram
Om du använder ASP.NET och konfigurera din app om du vill använda klientcertifikatautentisering certifikatet blir tillgängliga via den **HttpRequest.ClientCertificate** egenskapen. Andra program stackar ska cert klienten vara tillgänglig i appen genom en base64-kodad värdet i resurshuvudet ”X-ARR-ClientCert”. Ditt program kan skapa ett certifikat från det här värdet och sedan använda datorn för autentisering och auktorisering ändamål i ditt program.

## <a name="special-considerations-for-certificate-validation"></a>Speciella överväganden vid valideringen av servercertifikatet
Det klientcertifikat som skickas till programmet går inte via en verifiering av Azure Web Apps-plattformen. Verifiera det här certifikatet ansvarar för webbappen. Här är exempelkod för ASP.NET som validerar egenskaper för certifikat för autentisering.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
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

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
