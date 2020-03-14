---
title: Cloud Services-och hanterings certifikat | Microsoft Docs
description: Lär dig hur du skapar och använder certifikat med Microsoft Azure
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 783343dd8877bdf18e783494960c3052c293cc7c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248143"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Översikt över certifikat för Azure Cloud Services
Certifikat används i Azure för moln tjänster ([tjänst certifikat](#what-are-service-certificates)) och för autentisering med hanterings-API: et ([hanterings certifikat](#what-are-management-certificates)). Det här avsnittet innehåller en allmän översikt över båda certifikat typerna, hur du [skapar](#create) och distribuerar dem till Azure.

Certifikat som används i Azure är x. 509 v3-certifikat och kan signeras av ett annat betrott certifikat eller så kan de vara självsignerade. Ett självsignerat certifikat signeras av en egen skapare, vilket innebär att det inte är betrott som standard. De flesta webbläsare kan ignorera det här problemet. Du bör endast använda självsignerade certifikat när du utvecklar och testar moln tjänsterna. 

Certifikat som används av Azure kan innehålla en privat eller offentlig nyckel. Certifikat har ett tumavtryck som ger ett sätt att identifiera dem på ett entydigt sätt. Detta tumavtryck används i Azure- [konfigurationsfilen](cloud-services-configure-ssl-certificate-portal.md) för att identifiera vilket certifikat som en moln tjänst ska använda. 

>[!Note]
>Azure Cloud Services accepterar inte AES256-SHA256-krypterat certifikat.

## <a name="what-are-service-certificates"></a>Vad är tjänst certifikat?
Tjänst certifikat är kopplade till moln tjänster och möjliggör säker kommunikation till och från tjänsten. Om du till exempel har distribuerat en webb roll vill du ange ett certifikat som kan autentisera en exponerad HTTPS-slutpunkt. Tjänst certifikat, som definieras i din tjänst definition, distribueras automatiskt till den virtuella datorn som kör en instans av din roll. 

Du kan ladda upp tjänst certifikat till Azure antingen med hjälp av Azure Portal eller med hjälp av den klassiska distributions modellen. Tjänst certifikaten är associerade med en speciell moln tjänst. De tilldelas en distribution i tjänst definitions filen.

Tjänst certifikat kan hanteras separat från dina tjänster och kan hanteras av olika individer. En utvecklare kan till exempel Ladda upp ett tjänst paket som refererar till ett certifikat som en IT-chef tidigare har laddat upp till Azure. En IT-chef kan hantera och förnya certifikatet (ändra konfigurationen för tjänsten) utan att behöva ladda upp ett nytt tjänst paket. Uppdatering utan ett nytt tjänst paket är möjligt eftersom det logiska namnet, lagrings namnet och platsen för certifikatet finns i tjänst definitions filen och när tumavtrycket för certifikatet anges i tjänst konfigurations filen. Om du vill uppdatera certifikatet är det bara nödvändigt att ladda upp ett nytt certifikat och ändra tumavtryck-värdet i tjänst konfigurations filen.

>[!Note]
>I artikeln [Cloud Services vanliga frågor och svar om konfiguration och hantering](cloud-services-configuration-and-management-faq.md) finns viss värdefull information om certifikat.

## <a name="what-are-management-certificates"></a>Vad är hanterings certifikat?
Med hanterings certifikat kan du autentisera med den klassiska distributions modellen. Många program och verktyg (till exempel Visual Studio eller Azure SDK) använder dessa certifikat för att automatisera konfigurationen och distributionen av olika Azure-tjänster. Dessa är inte relaterade till moln tjänster. 

> [!WARNING]
> Var försiktig! Med dessa typer av certifikat kan alla som autentiserar med dem hantera den prenumeration de är associerade med. 
> 
> 

### <a name="limitations"></a>Begränsningar
Det finns en gräns på 100 hanterings certifikat per prenumeration. Det finns också en gräns på 100 hanterings certifikat för alla prenumerationer under en angiven tjänst administratörs användar-ID. Om användar-ID: t för konto administratören redan har använts för att lägga till 100-hanterings certifikat och det behövs mer certifikat kan du lägga till en delad administratör för att lägga till ytterligare certifikat. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat
Du kan använda ett verktyg som är tillgängligt för att skapa ett självsignerat certifikat så länge som de följer dessa inställningar:

* Ett X. 509-certifikat.
* Innehåller en privat nyckel.
* Skapat för nyckel utbyte (. pfx-fil).
* Ämnes namnet måste matcha den domän som används för att få åtkomst till moln tjänsten.

    > Det går inte att skaffa ett SSL-certifikat för cloudapp.net (eller för någon Azure-relaterad) domän. certifikatets ämnes namn måste överensstämma med det anpassade domän namnet som används för att få åtkomst till ditt program. Till exempel **contoso.net**, inte **contoso.cloudapp.net**.

* Minst 2048-bitars kryptering.
* **Endast tjänst certifikat**: certifikat på klient sidan måste finnas i det *personliga* certifikat arkivet.

Det finns två enkla sätt att skapa ett certifikat i Windows, med `makecert.exe`-verktyget eller IIS.

### <a name="makecertexe"></a>Makecert.exe
Det här verktyget är föråldrat och är inte längre dokumenterat här. Mer information finns i [den här MSDN-artikeln](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Använd IP-adressen i parametern-DnsName om du vill använda certifikatet med en IP-adress i stället för en domän.


Om du vill använda det här [certifikatet med hanterings portalen](../azure-api-management-certs.md)exporterar du det till en **CER** -fil:

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Det finns många sidor på Internet som beskriver hur du gör detta med IIS. [Här](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) är en bra jag hittade att jag förklarar det bra. 

### <a name="linux"></a>Linux
[Den här](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artikeln beskriver hur du skapar certifikat med SSH.

## <a name="next-steps"></a>Nästa steg
[Ladda upp tjänst certifikatet till Azure Portal](cloud-services-configure-ssl-certificate-portal.md).

Överför ett [hanterings-API-certifikat](../azure-api-management-certs.md) till Azure Portal.




