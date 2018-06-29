---
title: Azure-stacken Public Key Infrastructure certifikatkrav för Azure-stacken integrerat system | Microsoft Docs
description: Beskriver distributionskraven för Azure-stacken PKI-certifikat för Azure-stacken integrerat system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 9a43179998e8377dfbbb1a41ba7d46936d63aedd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030163"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack PKI-certifikatkrav

Azure-stacken har en infrastruktur för offentliga nätverk med hjälp av externa offentliga IP-adresser tilldelas en liten uppsättning Azure Stack-tjänster och eventuellt klient virtuella datorer. PKI-certifikat med lämpliga DNS-namn för dessa Azure Stack infrastruktur för offentliga slutpunkter krävs under distributionen av Azure-stacken. Den här artikeln innehåller information om:

- Vilka certifikat som krävs för att distribuera Azure-stacken
- Processen för att hämta certifikat som matchar de specifikationerna
- Hur du förbereder, validera och använda dessa certifikat under distributionen

> [!Note]  
> Under distributionen måste du kopiera certifikat till distributionsmappen som matchar den identitetsleverantör som du distribuerar mot (Azure AD eller AD FS). Om du använder ett enda certifikat för alla slutpunkter, måste du kopiera den certifikatfilen i varje distributionsmappen som beskrivs i tabellerna nedan. Mappstrukturen före bygger på den virtuella datorn för distribution och finns på: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Certifikatkrav
I följande lista beskrivs kraven på certifikaten som behövs för att distribuera Azure Stack: 
- Vara måste utfärdat certifikat från en intern certifikatutfärdare eller en offentlig certifikatutfärdare. Om du använder en offentlig certifikatutfärdare, måste det ingå i den grundläggande operativsystemavbildningen som en del av Microsoft Trusted Root myndigheten Program. Du hittar en fullständig lista: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Infrastrukturen för Azure-stacken måste ha nätverksåtkomst till certifikatutfärdarens certifikat listan över Återkallade plats publiceras i certifikatet. Den här listan över återkallade certifikat måste vara en http-slutpunkt
- När du roterar certifikat, måste certifikat vara antingen utfärdats från samma interna certifikatutfärdare används för att signera certifikat som anges i distribution eller en offentlig certifikatutfärdare från ovan
- Användning av självsignerade certifikat stöds inte
- Certifikatet kan vara ett enda jokertecken certifikat som omfattar alla namnutrymmen i fältet alternativt namn på CERTIFIKATMOTTAGARE. Du kan också använda certifikat med jokertecken för slutpunkter, till exempel **acs** och Nyckelvalv där de är obligatoriska. 
- Signaturalgoritm certifikatet får inte vara SHA1, måste det vara bättre. 
- Certifikatets format måste vara PFX, som både offentliga och privata nycklar som krävs för installationen av Azure-stacken. 
- Certifikat pfx-filer måste ha ett värde ”Digital signatur” och ”KeyEncipherment” i dess ”nyckelanvändning”.
- Certifikat pfx-filer måste ha värdena ”serverautentisering (1.3.6.1.5.5.7.3.1)” och ”klientautentisering (1.3.6.1.5.5.7.3.2)” i fältet ”förbättrad nyckelanvändning”.
- Certifikatets ”utfärdat till”: fältet får inte vara samma som dess ”utfärdat av”: fältet.
- Lösenorden för alla certifikat pfx-filer måste vara samma vid tidpunkten för distribution
- Lösenord för att den certifikat PFX-filen måste vara ett komplext lösenord.
- Kontrollera att ämnesnamn och Alternativt ämnesnamn i tillägget (x509v3_config) för alternativt namn matchar ämne. Fältet för certifikatmottagarens alternativa namn kan du ange ytterligare värdnamn (webbplatser, IP-adresser, allmänna namn) som ska skyddas av ett enda SSL-certifikat.

> [!NOTE]  
> Self signerat certifikat inte stöds.

> [!NOTE]  
> Förekomsten av mellanhand certifikatutfärdare i kedjan för förtroenden är för ett certifikat som stöds. 

## <a name="mandatory-certificates"></a>Obligatoriska certifikat
Tabellen i det här avsnittet beskrivs de Azure Stack offentlig slutpunkt PKI-certifikat som krävs för både Azure AD och Azure-stacken för AD FS-distributioner. Krav på certifikat som är grupperade efter område, samt de namnområden som används och certifikat som krävs för varje namnområde. I tabellen beskrivs också mappen där din leverantör kopierar olika certifikat per offentlig slutpunkt. 

Certifikat med lämpliga DNS-namn för varje slutpunkt för infrastruktur för offentliga Azure-stacken krävs. DNS-namnet för varje slutpunkt uttrycks i formatet:  *&lt;prefix >.&lt; region >. &lt;fqdn >*. 

För din distribution [region] och [externalfqdn] värdena måste matcha region och externa domännamn som du har valt för ditt system med Azure-stacken. Ett exempel är om var regionsnamnet på *Redmond* och namnet på externa domänen var *contoso.com*, DNS-namn som skulle ha formatet *&lt;prefix >. redmond.contoso.com*. Den  *&lt;prefix >* värden förutbestämd av Microsoft för att beskriva den slutpunkt som skyddas av certifikat. Dessutom kan den  *&lt;prefix >* värden externa infrastruktur slutpunkter beror på Azure Stack-tjänst som använder specifika slutpunkten. 

> [!note]  
> Certifikat kan finnas som ett enda jokertecken certifikat som omfattar alla namnområden i fälten ämne och alternativt namn på CERTIFIKATMOTTAGARE kopieras till alla kataloger eller certifikat för varje slutpunkt som kopieras till motsvarande katalog. Kom ihåg att båda alternativen måste du använda jokerteckencertifikat för slutpunkter som **acs** och Nyckelvalv där de är obligatoriska. 

| Distributionsmappen | Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN) | Omfång (per region) | Underdomän namnområde |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Offentlig Portal | portalen. &lt;region >. &lt;fqdn > | Portaler | &lt;region>.&lt;fqdn> |
| Admin Portal | adminportal. &lt;region >. &lt;fqdn > | Portaler | &lt;region>.&lt;fqdn> |
| Azure Resource Manager offentliga | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Azure Resource Manager-administratör | adminmanagement. &lt;region >. &lt;fqdn > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Jokertecken SSL-certifikat) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | * .table. &lt;region >. &lt;fqdn ><br>(Jokertecken SSL-certifikat) | Table Storage | tabell. &lt;region >. &lt;fqdn > |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Jokertecken SSL-certifikat) | Queue Storage | kö. &lt;region >. &lt;fqdn > |
| KeyVault | * .vault. &lt;region >. &lt;fqdn ><br>(Jokertecken SSL-certifikat) | Key Vault | valvet. &lt;region >. &lt;fqdn > |
| KeyVaultInternal | *.adminvault. &lt;region >. &lt;fqdn ><br>(Jokertecken SSL-certifikat) |  Internt Keyvault |  adminvault. &lt;region >. &lt;fqdn > |

Om du distribuerar Azure stacken med hjälp av Azure AD-distribution, behöver du bara begära certifikat visas i föregående tabell. Om du distribuerar Azure stacken med hjälp av AD FS-distribution måste du dock även begära certifikat som beskrivs i följande tabell:

|Distributionsmappen|Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN)|Omfång (per region)|Underdomän namnområde|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Alla certifikat i det här avsnittet måste ha samma lösenord. 

## <a name="optional-paas-certificates"></a>Valfria PaaS-certifikat
Om du planerar att distribuera ytterligare Azure Stack PaaS-tjänster (SQL, MySQL och Apptjänst) efter Azure stacken har distribuerats och konfigurerats, måste du begära ytterligare certifikat att täcka slutpunkter PaaS-tjänster. 

> [!IMPORTANT]
> De certifikat som du använder för App Service, SQL och MySQL resursproviders måste ha samma rotcertifikatutfärdare som de som används för globala Azure Stack-slutpunkter. 

I följande tabell beskrivs slutpunkter och certifikat som krävs för SQL och MySQL-kort och Apptjänst. Du behöver inte kopiera dessa certifikat till Azure-stacken distributionsmappen. I stället ange dessa certifikat när du installerar ytterligare resursleverantörer. 

|Omfång (per region)|Certifikat|Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN)|Underdomän namnområde|
|-----|-----|-----|-----|
|SQL, MySQL|SQL- och MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Jokertecken SSL-certifikat)|dbadapter.  *&lt;region >.&lt; FQDN >*|
|App Service|Web trafik standard SSL-certifikat|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Multi-domän med jokertecken SSL-certifikat<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Enkel inloggning|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> kräver ett certifikat med flera jokertecken Alternativt ämnesnamn. Flera jokertecken SAN på ett enda certifikat kan stöds inte av alla offentlig certifikatutfärdare 

<sup>2</sup> A &#42;.appservice. *&lt;region >. &lt;fqdn >* jokertecken certifikatet kan inte användas i stället för de här tre certifikat (api.appservice. *&lt;region >. &lt;fqdn >*, ftp.appservice. *&lt;region >. &lt;fqdn >*, och sso.appservice. *&lt;region >. &lt;fqdn >*. Apptjänst kräver explicit användning av separata certifikat för dessa slutpunkter. 

## <a name="learn-more"></a>Läs mer
Lär dig hur du [generera PKI-certifikat för distribution av Azure-stacken](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Nästa steg
[Identitetsintegrering](azure-stack-integrate-identity.md)

