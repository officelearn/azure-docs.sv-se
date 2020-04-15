---
title: Generera självsignerat certifikat med en anpassad rotcertifikatutfärdaren
titleSuffix: Azure Application Gateway
description: Lär dig hur du skapar ett självsignerat Azure Application Gateway-certifikat med en anpassad rotcertifikatutfärdaren
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 5ceefb076b63df942cfff202946f6b82050bbab9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311948"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Generera ett självsignerat Azure Application Gateway-certifikat med en anpassad rotcertifikatutfärdaren

Application Gateway v2 SKU introducerar användningen av betrodda rotcertifikat för att tillåta serverdserver. Detta tar bort autentiseringscertifikat som krävdes i v1 SKU. *Rotcertifikatet* är en Base-64-kodad X.509(. CER)-formatrotcertifikat från serverdingscertifikatservern. Den identifierar rotcertifikatutfärdaren som utfärdade servercertifikatet och servercertifikatet används sedan för TLS/SSL-kommunikationen.

Application Gateway litar på webbplatsens certifikat som standard om det är signerat av en välkänd certifikatutfärdare (till exempel GoDaddy eller DigiCert). Du behöver inte uttryckligen överföra rotcertifikatet i så fall. Mer information finns i [Översikt över TLS-avslutning och till TLS med Application Gateway](ssl-overview.md). Men om du har en utvecklings-/testmiljö och inte vill köpa ett verifierat certifikat för certifikatutfärdare kan du skapa en egen anpassad certifikatutfärdare och skapa ett självsignerat certifikat med den. 

> [!NOTE]
> Självsignerade certifikat är inte betrodda som standard och de kan vara svåra att underhålla. Dessutom kan de använda föråldrade hash- och chiffersviter som kanske inte är starka. För bättre säkerhet, köpa ett certifikat som signerats av en välkänd certifikatutfärdar.

I den här artikeln får du lära dig hur du:

- Skapa en egen anpassad certifikatutfärdarmyndighet
- Skapa ett självsignerat certifikat signerat av din anpassade certifikatutfärdaren
- Ladda upp ett självsignerat rotcertifikat till en programgateway för att autentisera serveråtkomstservern

## <a name="prerequisites"></a>Krav

- **[OpenSSL](https://www.openssl.org/) på en dator som kör Windows eller Linux** 

   Det kan finnas andra verktyg för certifikathantering, men den här självstudien använder OpenSSL. Du kan hitta OpenSSL levereras med många Linux-distributioner, såsom Ubuntu.
- **En webbserver**

   Apache, IIS eller NGINX för att testa certifikaten.

- **En programgateway v2 SKU**
   
  Om du inte har en befintlig programgateway läser du [Snabbstart: Direkt webbtrafik med Azure Application Gateway - Azure portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Skapa ett rotcertifikatutfärdarcertifikat

Skapa rotcertifikatutfärdaren med OpenSSL.

### <a name="create-the-root-key"></a>Skapa rotnyckeln

1. Logga in på datorn där OpenSSL är installerat och kör följande kommando. Detta skapar en lösenordsskyddad nyckel.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Skriv ett starkt lösenord vid prompten. Till exempel minst nio tecken med versaler, gemener, siffror och symboler.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Skapa ett rotcertifikat och signera det själv

1. Använd följande kommandon för att generera csr och certifikatet.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   De tidigare kommandona skapar rotcertifikatet. Du ska använda detta för att signera servercertifikatet.

1. När du uppmanas till det skriver du lösenordet för rotnyckeln och organisationsinformationen för den anpassade certifikatutfärdaren, till exempel Land, Stat, Org, OU och det fullständigt kvalificerade domännamnet (det här är utfärdarens domän).

   ![skapa rotcertifikat](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Skapa ett servercertifikat

Därefter ska du skapa ett servercertifikat med OpenSSL.

### <a name="create-the-certificates-key"></a>Skapa certifikatets nyckel

Använd följande kommando för att generera nyckeln för servercertifikatet.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Skapa kundtjänstrepresentanten (begäran om certifikatsignering)

Kundtjänstrepresentanten är en offentlig nyckel som ges till en certifikatutfärdare när du begär ett certifikat. Certifikatutfärdaren utfärdar certifikatet för den här specifika begäran.

> [!NOTE]
> Servercertifikatet (gemensamt namn) måste skilja sig från utfärdarens domän. I det här fallet är `www.contoso.com` till exempel CN för utfärdaren och `www.fabrikam.com`servercertifikatets CN är .


1. Använd följande kommando för att generera kundtjänstrepresentanten:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. När du uppmanas till det skriver du lösenordet för rotnyckeln och organisationsinformationen för den anpassade certifikatutfärdaren: Land, Delstat, Org, OU och det fullständigt kvalificerade domännamnet. Detta är webbplatsens område och det bör skilja sig från emittenten.

   ![Servercertifikat](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Generera certifikatet med kundtjänstrepresentanten och nyckeln och signera det med certifikatutfärdarens rotnyckel

1. Använd följande kommando för att skapa certifikatet:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verifiera det nyligen skapade certifikatet

1. Använd följande kommando för att skriva ut utdata från CRT-filen och verifiera dess innehåll:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Kontroll av certifikat](media/self-signed-certificates/verify-cert.png)

1. Kontrollera filerna i katalogen och se till att du har följande filer:

   - contoso.crt (contoso.crt)
   - contoso.key (contoso.key)
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Konfigurera certifikatet i TLS-inställningarna för webbservern

Konfigurera TLS på webbservern med filerna fabrikam.crt och fabrikam.key. Om webbservern inte kan ta två filer kan du kombinera dem med en enda .pem- eller .pfx-fil med OpenSSL-kommandon.

### <a name="iis"></a>IIS

Instruktioner om hur du importerar certifikat och överför dem som servercertifikat på IIS finns i [SÅ HÄR: Installera importerade certifikat på en webbserver i Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Instruktioner för TLS-bindning finns i [Så här konfigurerar du SSL på IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Följande konfiguration är ett exempel [virtuell värd konfigurerad för SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) i Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Följande konfiguration är ett exempel [på NGINX-serverblock](https://nginx.org/docs/http/configuring_https_servers.html) med TLS-konfiguration:

![NGINX med TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Öppna servern för att verifiera konfigurationen

1. Lägg till rotcertifikatet i datorns betrodda rotarkiv. När du öppnar webbplatsen, se till att hela certifikatkedjan ses i webbläsaren.

   ![Betrodda rotcertifikat](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Det förutsätts att DNS har konfigurerats för att peka webbserverns namn (i det här exemplet www.fabrikam.com) till webbserverns IP-adress. Om inte, kan du redigera [hosts-filen](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) för att matcha namnet.
1. Bläddra till din webbplats och klicka på låsikonen i webbläsarens adressruta för att verifiera webbplatsens och certifikatinformationen.

## <a name="verify-the-configuration-with-openssl"></a>Verifiera konfigurationen med OpenSSL

Du kan också använda OpenSSL för att verifiera certifikatet.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL-certifikatverifiering](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Ladda upp rotcertifikatet till HTTP-inställningar för Programgateway

Om du vill överföra certifikatet i Application Gateway måste du exportera CRT-certifikatet till en .cer-format Base-64-kodad. Eftersom .crt redan innehåller den offentliga nyckeln i base-64 kodade format, bara byta namn på filändelsen från .crt till .cer. 

### <a name="azure-portal"></a>Azure Portal

Om du vill överföra det betrodda rotcertifikatet från portalen väljer du **HTTP-inställningarna** och väljer **HTTPS-protokollet.**

![Lägga till ett certifikat med portalen](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Du kan också använda Azure CLI eller Azure PowerShell för att överföra rotcertifikatet. Följande kod är ett Azure PowerShell-exempel.

> [!NOTE]
> I följande exempel läggs ett betrott rotcertifikat till i programgatewayen, en ny HTTP-inställning skapas och en ny regel läggs till, förutsatt att serverdapoolen och lyssnaren redan finns.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Verifiera återställningshälsa för programgateway

1. Klicka på vyn **Serverdhälsa** för programgatewayen för att kontrollera om avsökningen är felfri.
1. Du bör se att statusen är **felfri** för HTTPS-avsökningen.

![HTTPS-sond](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Nästa steg

Mer information om SSL\TLS i Application Gateway finns i [Översikt över TLS-avslutning och slutpunkt till TLS med Application Gateway](ssl-overview.md).

