---
title: Integrerade Azure Stack-infrastruktur för offentliga nycklar certifikatkrav för Azure Stack-system | Microsoft Docs
description: Beskriver distributionskrav för Azure Stack PKI-certifikat för integrerade Azure Stack-system.
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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 51ab999880dd3bfd453b0e6c2d20d8d6f9a0e093
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55660127"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Certifikatkrav för Azure Stack-infrastruktur för offentliga nycklar

Azure Stack har en infrastruktur för offentliga nätverk med hjälp av externt tillgängliga offentliga IP-adresser tilldelade till en liten uppsättning Azure Stack-tjänster och eventuellt virtuella klientdatorer. PKI-certifikat med rätt DNS-namn för de här slutpunkterna för Azure Stack-infrastruktur för offentliga krävs under distributionen av Azure Stack. Den här artikeln innehåller information om:

- Vilka certifikat som krävs för att distribuera Azure Stack
- Processen för att hämta certifikat som överensstämmer med dessa specifikationer
- Hur du förbereder, verifiera och använda dessa certifikat under distributionen

> [!Note]  
> Under distributionen måste du kopiera certifikat till distributionsmappen som matchar den identitetsprovider som du distribuerar mot (Azure AD eller AD FS). Om du använder ett enda certifikat för alla slutpunkter måste du kopiera den certifikatfilen till varje distributionsmappen som beskrivs i tabellerna nedan. Mappstrukturen är färdiga på den virtuella datorn för distribution och finns på: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Certifikatkrav
I följande lista beskrivs kraven på certifikaten som behövs för att distribuera Azure Stack: 
- Certifikat måste utfärdas från en intern certifikatutfärdare eller en offentlig certifikatutfärdare. Om du använder en offentlig certifikatutfärdare, måste finnas med i den grundläggande operativsystemavbildningen som en del av Microsoft Trusted Root utfärdare Program. Du hittar en fullständig lista: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Azure Stack-infrastruktur måste ha nätverksåtkomst till certifikatutfärdarens certifikat listan över Återkallade plats publiceras i certifikatet. Den här listan över återkallade certifikat måste vara en http-slutpunkt
- När du roterar certifikat, måste certifikaten ha antingen utfärdas från samma interna certifikatutfärdare används för att signera certifikat som angavs vid distribution eller en offentlig certifikatutfärdare ovan
- Användning av självsignerade certifikat stöds inte
- För distribution och rotation som du kan antingen använda ett enstaka certifikat som täcker alla namnutrymmen i certifikatets ämnesnamn och alternativt namn på CERTIFIKATMOTTAGARE eller du kan använda person-certifikat för varje namnrum nedan som Azure Stack tjänster som du planerar att använda kräver. Båda metoderna kräver med jokertecken för slutpunkter där de är obligatoriska, till exempel **KeyVault** och **KeyVaultInternal**. 
- Certifikatets PFX-kryptering ska vara 3DES. 
- Signaturalgoritm certifikatet får inte vara SHA1. 
- Certifikatformatet måste vara PFX, som både offentliga och privata nycklar som krävs för installation av Azure Stack. Den privata nyckeln måste ha lokal dator nyckelattributet ange.
- PFX-kryptering måste vara 3DES (detta är standard när du exporterar från en Windows 10-klient eller certifikatarkivet för Windows Server 2016).
- Pfx-filer för certifikatet måste ha ett värde ”Digital signatur” och ”KeyEncipherment” i dess ”nyckelanvändning”.
- Pfx-filer för certifikatet måste ha värdena ”serverautentisering (1.3.6.1.5.5.7.3.1)” och ”klientautentisering (1.3.6.1.5.5.7.3.2)” i fältet ”förbättrad nyckelanvändning”.
- Certifikatets ”utfärdat till”: fältet får inte vara samma som dess ”utfärdat av”: fält.
- Lösenorden för alla certifikat pfx-filer måste vara samma vid tidpunkten för distribution
- Lösenord för den certifikat PFX-filen måste vara ett komplext lösenord. Skapa ett lösenord som uppfyller följande komplexitetskrav för lösenord. En minsta längd på åtta tecken. Lösenordet innehåller minst tre av följande: versala bokstäver, gemena bokstäver, siffror från 0 till 9, specialtecken, alfabetiskt tecken som är varken versaler eller gemener. Anteckna det här lösenordet. Du använder den som en parameter för distribution.
- Se till att ämnesnamn och Alternativt ämnesnamn i ämne alternativt namn för tillägget (x509v3_config) matchningen. Fältet för certifikatmottagarens alternativa namn kan du ange ytterligare värdnamn (webbplatser, IP-adresser, vanliga namn) som ska skyddas av ett enda SSL-certifikat.

> [!NOTE]  
> Självsignerat certifikat för signering stöds inte.

> [!NOTE]  
> Förekomst av mellanhand certifikatutfärdare i kedjan av förtroenden IS för ett certifikat som stöds. 

## <a name="mandatory-certificates"></a>Obligatoriska certifikat
Tabellen i det här avsnittet beskrivs de Azure Stack offentlig slutpunkt PKI-certifikat som krävs för både Azure AD och AD FS Azure Stack-distributioner. Krav på certifikat är grupperade efter område, samt de namnområden som används och certifikat som krävs för varje namnområde. Tabellen beskriver också den mapp där leverantören kopierar olika certifikat per offentlig slutpunkt. 

Certifikat med rätt DNS-namn för varje Azure Stack-infrastruktur för offentliga slutpunkt krävs. DNS-namnet för varje slutpunkt uttrycks i formatet:  *&lt;prefix >.&lt; region >. &lt;fqdn >*. 

För din distribution [region] och [externalfqdn] värden måste matcha region och externa domännamn som du valde för Azure Stack-system. Till exempel om områdesnamnet var *Redmond* och namnet på extern domän var *contoso.com*, DNS-namn skulle ha formatet *&lt;prefix >. redmond.contoso.com*. Den  *&lt;prefix >* värden är förutbestämd av Microsoft för att beskriva den slutpunkt som skyddas av certifikatet. Dessutom kan den  *&lt;prefix >* värdena för de externa infrastrukturslutpunkter beroende av Azure Stack-tjänsten som använder den specifika slutpunkten. 

> [!note]  
> För produktionsmiljöer rekommenderar vi certifikat genereras för varje slutpunkt och kopieras till motsvarande katalog. För utvecklingsmiljöer, kan certifikat anges som ett enda jokertecken-certifikat som täcker alla namnområden i fälten ämne och alternativt namn på CERTIFIKATMOTTAGARE kopieras till alla kataloger. Ett enda certifikat som täcker alla slutpunkter och tjänster är ett osäkert hållning därför utveckling endast. Kom ihåg att båda alternativen måste du använda jokerteckencertifikat för slutpunkter som **acs** och Key Vault där de är obligatoriska. 

| Distributionsmappen | Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN) | Omfång (per region) | SubDomain namespace |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Offentlig Portal | portal.&lt;region>.&lt;fqdn> | Portaler | &lt;region>.&lt;fqdn> |
| Administratörsportalen | adminportal.&lt;region>.&lt;fqdn> | Portaler | &lt;region>.&lt;fqdn> |
| Azure Resource Manager Public | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Azure Resource Manager-administratör | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Jokertecken SSL-certifikat) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Jokertecken SSL-certifikat) | Table Storage | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Jokertecken SSL-certifikat) | Queue Storage | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Jokertecken SSL-certifikat) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Jokertecken SSL-certifikat) |  Intern Keyvault |  adminvault.&lt;region>.&lt;fqdn> |
| Admininistrationstillägg värden | *.adminhosting. \<region >. \<fqdn > (SSL-certifikat med jokertecken) | Admininistrationstillägg värden | adminhosting.\<region>.\<fqdn> |
| Offentliga tillägget värd | * .hosting. \<region >. \<fqdn > (SSL-certifikat med jokertecken) | Offentliga tillägget värd | hosting.\<region>.\<fqdn> |

Om du distribuerar Azure Stack med hjälp av Azure AD-distributionsläget, behöver du bara begära certifikat visas i föregående tabell. Om du distribuerar Azure Stack med hjälp av läget för AD FS-distribution måste du dock också begära certifikat beskrivs i följande tabell:

|Distributionsmappen|Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN)|Omfång (per region)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Alla certifikat i det här avsnittet måste ha samma lösenord. 

## <a name="optional-paas-certificates"></a>Valfritt PaaS-certifikat
Om du planerar att distribuera ytterligare Azure Stack PaaS-tjänster (SQL, MySQL och App Service) efter att Azure Stack har distribuerats och konfigurerats, måste du begära ytterligare certifikat för att täcka slutpunkterna för PaaS-tjänster. 

> [!IMPORTANT]
> De certifikat som du använder för App Service, SQL och MySQL-resursprovider måste ha samma rotcertifikatutfärdare som de som används för de globala Azure Stack-slutpunkterna. 

I följande tabell beskrivs de slutpunkter och certifikat som krävs för SQL- och MySQL-kort och för App Service. Du behöver inte kopiera dessa certifikat till mappen Azure Stack-distribution. Istället tillhandahåller du dessa certifikat när du installerar ytterligare resursleverantörer. 

|Omfång (per region)|Certifikat|Nödvändiga certifikatämnet och Alternativt ämnesnamn (SAN)|SubDomain namespace|
|-----|-----|-----|-----|
|SQL, MySQL|SQL- och MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Jokertecken SSL-certifikat)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Web Traffic standard SSL-certifikat|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Multi-domän med jokertecken SSL-certifikat<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|Enkel inloggning|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-certifikat<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> kräver ett certifikat med flera jokertecken Alternativt ämnesnamn. Flera jokertecken SAN-nätverk på ett enda certifikat kan inte stöds av alla offentliga certifikatutfärdare 

<sup>2</sup> A &#42;.appservice. *&lt;region >. &lt;fqdn >* jokertecken certifikatet kan inte användas i stället för de här tre certifikat (api.appservice. *&lt;region >. &lt;fqdn >*, ftp.appservice. *&lt;region >. &lt;fqdn >*, och sso.appservice. *&lt;region >. &lt;fqdn >*. Appservice kräver uttryckligen användning av separata certifikat för de här slutpunkterna. 

## <a name="learn-more"></a>Läs mer
Lär dig hur du [generera PKI-certifikat för Azure Stack-distributioner](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Nästa steg
[Identitetsintegrering](azure-stack-integrate-identity.md)

