---
title: Molntjänster och hanteringscertifikat | Microsoft-dokument
description: Lär dig hur du skapar och använder certifikat med Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 173f5c698ab44ea269995665bcbc33c726d4f03a
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811455"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Certifikatöversikt för Azure Cloud Services
Certifikat används i Azure för molntjänster[(tjänstcertifikat)](#what-are-service-certificates)och för att autentisera med[hanterings-API :et ( hanteringscertifikat](#what-are-management-certificates)). Det här avsnittet ger en allmän översikt över båda certifikattyperna, hur du [skapar](#create) och distribuerar dem till Azure.

Certifikat som används i Azure är x.509 v3-certifikat och kan signeras av ett annat betrott certifikat eller så kan de vara självsignerade. Ett självsignerat certifikat signeras av den egna skaparen, därför är det inte betrott som standard. De flesta webbläsare kan ignorera det här problemet. Du bör bara använda självsignerade certifikat när du utvecklar och testar dina molntjänster. 

Certifikat som används av Azure kan innehålla en privat eller en offentlig nyckel. Certifikat har ett tumavtryck som ger ett sätt att identifiera dem på ett entydigt sätt. Det här tumavtrycket används i [Azure-konfigurationsfilen](cloud-services-configure-ssl-certificate-portal.md) för att identifiera vilket certifikat en molntjänst ska använda. 

>[!Note]
>Azure Cloud Services accepterar inte AES256-SHA256 krypterat certifikat.

## <a name="what-are-service-certificates"></a>Vad är tjänstcertifikat?
Tjänstcertifikat är kopplade till molntjänster och möjliggör säker kommunikation till och från tjänsten. Om du till exempel har distribuerat en webbroll vill du ange ett certifikat som kan autentisera en exponerad HTTPS-slutpunkt. Tjänstcertifikat, som definieras i tjänstdefinitionen, distribueras automatiskt till den virtuella datorn som kör en instans av din roll. 

Du kan antingen ladda upp tjänstcertifikat till Azure via Azure-portalen eller med hjälp av den klassiska distributionsmodellen. Tjänstcertifikaten är associerade med en viss molntjänst. De tilldelas en distribution i tjänstens definitionsfil.

Tjänstcertifikat kan hanteras separat från dina tjänster och kan hanteras av olika personer. En utvecklare kan till exempel ladda upp ett servicepaket som refererar till ett certifikat som en IT-chef tidigare har laddat upp till Azure. En IT-chef kan hantera och förnya certifikatet (ändra tjänstens konfiguration) utan att behöva ladda upp ett nytt tjänstpaket. Det går att uppdatera utan ett nytt servicepaket eftersom certifikatets logiska namn, arkivnamn och plats finns i tjänstdefinitionsfilen och medan certifikatets tumavtryck anges i tjänstkonfigurationsfilen. Om du vill uppdatera certifikatet behöver du bara ladda upp ett nytt certifikat och ändra tumavtrycksvärdet i tjänstens konfigurationsfil.

>[!Note]
>[Vanliga frågor och svar om Molntjänster –](cloud-services-configuration-and-management-faq.md) konfiguration och hantering innehåller användbar information om certifikat.

## <a name="what-are-management-certificates"></a>Vad är hanteringscertifikat?
Hanteringscertifikat gör att du kan autentisera med den klassiska distributionsmodellen. Många program och verktyg (som Visual Studio och Azure SDK) använder sådana här certifikat till att automatisera konfigurationen och distributionen av olika Azure-tjänster. Dessa är egentligen inte relaterade till molntjänster. 

> [!WARNING]
> Var försiktig! Med de här typerna av certifikat kan alla som autentiserar med dem hantera prenumerationen som de är associerade med. 
> 
> 

### <a name="limitations"></a>Begränsningar
Det finns en gräns på 100 hanteringscertifikat per prenumeration. Det finns också en gräns på 100 hanteringscertifikat för alla prenumerationer under en viss tjänstadministratörs användar-ID. Om kontoadministratörens användar-ID redan har använts för att lägga till 100 hanteringscertifikat och det behövs fler certifikat kan du lägga till en medadministratör för att lägga till ytterligare certifikat. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat
Du kan använda alla tillgängliga verktyg för att skapa ett självsignerat certifikat så länge de följer dessa inställningar:

* Ett X.509-certifikat.
* Innehåller en privat nyckel.
* Skapad för nyckelutbyte (.pfx-fil).
* Ämnesnamn måste matcha den domän som används för att komma åt molntjänsten.

    > Du kan inte hämta ett TLS/SSL-certifikat för cloudapp.net (eller för någon Azure-relaterad) domän. Certifikatets ämnesnamn måste matcha det anpassade domännamn som används för att komma åt ditt program. Till exempel **contoso.net**, inte **contoso.cloudapp.net**.

* Minst 2048-bitars kryptering.
* **Endast servicecertifikat:** Certifikat på klientsidan måste finnas i det *personliga* certifikatarkivet.

Det finns två enkla sätt att skapa `makecert.exe` ett certifikat i Windows, med verktyget eller IIS.

### <a name="makecertexe"></a>Makecert.exe
Det här verktyget har inaktuellt och dokumenteras inte längre här. Mer information finns i [den här MSDN-artikeln](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Om du vill använda certifikatet med en IP-adress i stället för en domän använder du IP-adressen i parametern -DnsName.


Om du vill använda det här [certifikatet med hanteringsportalen](../azure-api-management-certs.md)exporterar du det till en **CER-fil:**

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Det finns många sidor på internet som täcker hur man gör detta med IIS. [Här](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) är en stor en jag fann att jag tycker förklarar det väl. 

### <a name="linux"></a>Linux
[I](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) den här artikeln beskrivs hur du skapar certifikat med SSH.

## <a name="next-steps"></a>Nästa steg
[Ladda upp tjänstcertifikatet till Azure-portalen](cloud-services-configure-ssl-certificate-portal.md).

Ladda upp ett [API-certifikat](../azure-api-management-certs.md) för hantering till Azure-portalen.




