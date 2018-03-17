---
title: "Molntjänster och hanteringscertifikat | Microsoft Docs"
description: "Lär dig hur du skapar och använder certifikat med Microsoft Azure"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: fc70d00d-899b-4771-855f-44574dc4bfc6
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 6a1e4f5316cc0321c1409f9e48daeae6ee483bf6
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Översikt över certifikat för Azure-molntjänster
Certifikat används i Azure för molntjänster ([tjänsten certifikat](#what-are-service-certificates)) och för att autentisera med management API ([hanteringscertifikat](#what-are-management-certificates)). Det här avsnittet ger en allmän översikt över båda typer av certifikat, hur till [skapa](#create) och [distribuera](#deploy) dem till Azure.

Certifikat som används i Azure är x.509 v3-certifikat och kan vara signerat av en annan betrodda certifikat eller så kan de vara självsignerade. Ett självsignerat certifikat som är signerat av en egen skapare, därför inte är betrodd som standard. De flesta webbläsare kan ignorera det här problemet. Du bör endast använda självsignerade certifikat när du utvecklar och testar dina molntjänster. 

Certifikat som används av Azure kan innehålla en privat eller offentlig nyckel. Certifikatet har ett tumavtryck som ett sätt att identifiera dem på ett entydigt sätt. Det här tumavtrycket används i Azure [konfigurationsfilen](cloud-services-configure-ssl-certificate-portal.md) för att identifiera vilket certifikat som en tjänst i molnet ska använda. 

## <a name="what-are-service-certificates"></a>Vad är service-certifikat?
Tjänstcertifikat som är kopplade till molntjänster och aktivera säker kommunikation till och från tjänsten. Om du har distribuerat en webbroll skulle du till exempel vill ange ett certifikat som kan autentisera en exponerade HTTPS-slutpunkt. Tjänstcertifikat som definierats i din tjänstdefinitionen distribueras automatiskt till den virtuella datorn som kör en instans av din roll. 

Du kan överföra tjänstcertifikat till Azure med antingen Azure-portalen eller med hjälp av den klassiska distributionsmodellen. Tjänstcertifikat som är associerade med en specifik molnbaserad tjänst. De är tilldelade till en distribution i tjänstdefinitionsfilen.

Tjänstcertifikat kan hanteras separat från dina tjänster och kan hanteras av olika personer. En utvecklare kan till exempel överföra en tjänstpaket som refererar till ett certifikat som en IT-chef tidigare har överförts till Azure. En IT-chef kan hantera och förnya certifikatet (ändra konfigurationen av tjänsten) utan att behöva ladda upp ett nytt tjänstepaket. Det är möjligt att uppdatera utan ett nytt tjänstepaket eftersom logiska namn, store-namn och plats för certifikatet tjänstdefinitionsfilen och när certifikatets tumavtryck har angetts i tjänstkonfigurationsfilen. Om du vill uppdatera certifikatet krävs endast att överföra ett nytt certifikat och ändra tumavtrycksvärde i tjänstekonfigurationsfilen.

>[!Note]
>Den [Cloud Services vanliga frågor och svar](cloud-services-faq.md) artikeln innehåller användbar information om certifikat.

## <a name="what-are-management-certificates"></a>Vad är certifikat?
Certifikat kan du autentisera med den klassiska distributionsmodellen. Många program och verktyg (till exempel Visual Studio eller Azure SDK) kan du använda dessa certifikat för att automatisera konfigurationen och distributionen av olika Azure-tjänster. Dessa är inte relaterade till molntjänster verkligen. 

> [!WARNING]
> Var försiktig! Dessa typer av certifikat kan alla som autentiserar med dem för att hantera de är associerade med prenumerationen. 
> 
> 

### <a name="limitations"></a>Begränsningar
Det finns en gräns på 100 hanteringscertifikat per prenumeration. Det finns en gräns på 100 hanteringscertifikat för alla prenumerationer under en viss tjänstadministratör användar-ID. Om användar-ID för kontoadministratören har redan använts för att lägga till 100 hanteringscertifikat och det finns flera certifikat måste du lägga till en medadministratör för att lägga till ytterligare certifikat. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat
Du kan använda ett verktyg som är tillgängliga för att skapa ett självsignerat certifikat, så länge de följer dessa inställningar:

* Ett X.509-certifikat.
* Innehåller en privat nyckel.
* Skapa för utbyte av nycklar (.pfx-fil).
* Ämnesnamn måste matcha den domän som används för åtkomst till Molntjänsten.

    > Du kan inte skaffa ett SSL-certifikat för cloudapp.net (eller något Azure-relaterade) domän. certifikatets ämnesnamn måste matcha det anpassade domännamnet som används för åtkomst till ditt program. Till exempel **contoso.net**, inte **contoso.cloudapp.net**.

* Minst 2048-bitars kryptering.
* **Tjänsten certifikat endast**: klientsidan certifikatet måste finnas i den *personliga* certifikatarkiv.

Det finns två enkelt sätt att skapa ett certifikat i Windows, med den `makecert.exe` verktyg eller IIS.

### <a name="makecertexe"></a>Makecert.exe
Det här verktyget är inaktuell och inte längre dokumenteras här. Mer information finns i [MSDN-artikel](https://msdn.microsoft.com/library/windows/desktop/aa386968).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Om du vill använda ett certifikat med en IP-adress i stället för en domän kan du använda IP-adressen i parametern - DNS-namn.


Om du vill använda den här [certifikat med hanteringsportalen](../azure-api-management-certs.md), exportera den till en **.cer** fil:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Det finns många sidor på internet som beskriver hur du gör detta med IIS. [Här](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) är en bra jag hittade som tror förklarar det bra. 

### <a name="linux"></a>Linux
[Detta](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artikeln beskriver hur du skapar certifikat med SSH.

## <a name="next-steps"></a>Nästa steg
[Överför din tjänstcertifikat till Azure portal](cloud-services-configure-ssl-certificate-portal.md).

Överför en [hanteringscertifikat API](../azure-api-management-certs.md) till Azure-portalen.

