---
title: Cloud Services och hanteringscertifikat | Microsoft Docs
description: Lär dig hur du skapar och använder certifikat med Microsoft Azure
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 4ca26c7b8fbfebbce8cfcb9915a7db12e5ad2352
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793856"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Översikt över certifikat för Azure Cloud Services
Certifikat används i Azure för cloud services ([tjänsten certifikat](#what-are-service-certificates)) och för att autentisera med API för hantering ([hanteringscertifikat](#what-are-management-certificates)). Det här avsnittet ger en allmän översikt över båda typer av certifikat, hur till [skapa](#create) och distribuera dem till Azure.

Certifikat som används i Azure är x.509 v3-certifikat och kan vara signerat av en annan betrott certifikat eller de kan vara självsignerade. Ett självsignerat certifikat är signerat av en egen skapare kan därför inte är betrodd som standard. De flesta webbläsare kan ignorera det här problemet. Du bör endast använda självsignerade certifikat när du utvecklar och testar dina molntjänster. 

Certifikat som används av Azure kan innehålla en privat eller offentlig nyckel. Certifikatet har ett tumavtryck som ett sätt att identifiera dem på ett entydigt sätt. Det här tumavtrycket används i Azure [konfigurationsfilen](cloud-services-configure-ssl-certificate-portal.md) för att identifiera vilket certifikat som en tjänst i molnet ska använda. 

>[!Note]
>Azure Cloud Services accepterar inte krypterade AES256 SHA256-certifikatet.

## <a name="what-are-service-certificates"></a>Vad är service-certifikat?
Service-certifikat är kopplade till molntjänster och aktivera säker kommunikation till och från tjänsten. Om du har distribuerat en webbroll, skulle du till exempel vill ange ett certifikat som kan autentisera en exponerade HTTPS-slutpunkt. Tjänstcertifikat som definierats i tjänstdefinitionsfilen, distribueras automatiskt till den virtuella datorn som kör en instans av din roll. 

Du kan överföra service-certifikat till Azure, antingen med hjälp av Azure-portalen eller genom att använda den klassiska distributionsmodellen. Tjänstcertifikat som är associerade med en viss molntjänst. De är tilldelade till en distribution i tjänstdefinitionsfilen.

Service-certifikat kan hanteras separat från dina tjänster och kan hanteras av olika personer. Utvecklare kan till exempel överföra en tjänstpaket som refererar till ett certifikat som en IT-chef har tidigare har överförts till Azure. En IT-chef kan hantera och förnya certifikatet (ändra konfigurationen av tjänsten) utan att behöva ladda upp ett nytt tjänstpaket. Det är möjligt att uppdaterar utan att ett nytt tjänstpaket eftersom logiska namn, store-namnet och platsen för certifikatet är i tjänstdefinitionsfilen och medan certifikatets tumavtryck har angetts i tjänstkonfigurationsfilen. Om du vill uppdatera certifikatet krävs endast att ladda upp ett nytt certifikat och ändra värdet för tumavtrycket i tjänstekonfigurationsfilen.

>[!Note]
>Den [Cloud Services vanliga frågor och svar](cloud-services-faq.md) artikeln har lite användbar information om certifikat.

## <a name="what-are-management-certificates"></a>Vad är certifikat?
Hanteringscertifikat kan du autentisera med den klassiska distributionsmodellen. Många program och verktyg (till exempel Visual Studio eller Azure SDK) kan du använda dessa certifikat för att automatisera konfigurationen och distributionen av olika Azure-tjänster. Dessa är inte riktigt relaterade till molntjänster. 

> [!WARNING]
> Var försiktig! Dessa typer av certifikat kan vem som helst som autentiserar med dem för att hantera de är associerade med prenumerationen. 
> 
> 

### <a name="limitations"></a>Begränsningar
Det finns en gräns på 100 hanteringscertifikat per prenumeration. Det finns också en gräns på 100 hanteringscertifikat för alla prenumerationer under en viss tjänstadministratör användar-ID. Om användar-ID för administratörskontot har redan använts för att lägga till 100 hanteringscertifikat och det finns ett behov av fler certifikat, kan du lägga till en delad administratör för att lägga till ytterligare certifikat. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat
Du kan använda ett verktyg som kan användas för att skapa ett självsignerat certifikat, så länge de följa de här inställningarna:

* Ett X.509-certifikat.
* Innehåller en privat nyckel.
* Skapat för nyckelutbyte (.pfx-fil).
* Ämnesnamn måste matcha den domän som används för att få åtkomst till Molntjänsten.

    > Du kan inte skaffa ett SSL-certifikat för cloudapp.net (eller för alla Azure-relaterade) domänen. certifikatets ämnesnamn måste matcha det anpassade domännamnet som används för att komma åt ditt program. Till exempel **contoso.net**, inte **contoso.cloudapp.net**.

* Minst 2048-bitars kryptering.
* **Tjänsten certifikat endast**: Klientsidan certifikatet måste finnas i den *personliga* certifikatarkiv.

Det finns två enkla sätt för att skapa ett certifikat på Windows, med den `makecert.exe` verktyg eller IIS.

### <a name="makecertexe"></a>Makecert.exe
Det här verktyget är inaktuell och är inte längre dokumenteras här. Mer information finns i [den här MSDN-artikeln](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Om du vill använda ett certifikat med en IP-adress i stället för en domän kan du använda IP-adressen i parametern - DnsName.


Om du vill använda den här [certifikat med hanteringsportalen](../azure-api-management-certs.md), exportera den till en **.cer** fil:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Det finns många sidor på internet som beskriver hur du gör detta med IIS. [Här](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) är en bra jag hittade jag tror förklarar den också. 

### <a name="linux"></a>Linux
[Detta](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artikeln beskrivs hur du skapar certifikat med SSH.

## <a name="next-steps"></a>Nästa steg
[Ladda upp service-certifikatet till Azure-portalen](cloud-services-configure-ssl-certificate-portal.md).

Ladda upp en [API-hanteringscertifikat](../azure-api-management-certs.md) till Azure-portalen.

