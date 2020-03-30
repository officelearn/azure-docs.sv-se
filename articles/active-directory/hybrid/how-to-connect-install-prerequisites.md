---
title: 'Azure AD Connect: Förutsättningar och maskinvara | Microsoft-dokument'
description: I det här avsnittet beskrivs förkraven och maskinvarukraven för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79741557e6eea1b4252e5ab4d9976b124cea1169
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346891"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Förhandskrav för Azure AD Connect
I det här avsnittet beskrivs förkraven och maskinvarukraven för Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Innan du installerar Azure AD Connect
Innan du installerar Azure AD Connect finns det några saker du behöver.

### <a name="azure-ad"></a>Azure AD
* En Azure AD-klientorganisation. Du får en med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). Du kan använda någon av följande portaler för att hantera Azure AD Connect:
  * [Azure-portalen](https://portal.azure.com).
  * [Office-portalen](https://portal.office.com).  
* [Lägg till och verifiera den domän](../active-directory-domains-add-azure-portal.md) som du planerar att använda i Azure AD. Om du till exempel planerar att använda contoso.com för användarna kontrollerar du att den här domänen har verifierats och att du inte bara använder contoso.onmicrosoft.com standarddomänen.
* En Azure AD-klient tillåter som standard 50k-objekt. När du verifierar domänen ökas gränsen till 300 000 objekt. Om du behöver ännu fler objekt i Azure AD måste du öppna ett supportärende för att få gränsen ännu längre. Om du behöver fler än 500 k-objekt behöver du en licens, till exempel Office 365, Azure AD Basic, Azure AD Premium eller Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Förbereda lokala data
* Använd [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) för att identifiera fel som dubbletter och formateringsproblem i katalogen innan du synkroniserar med Azure AD och Office 365.
* Granska [valfria synkroniseringsfunktioner som du kan aktivera i Azure AD](how-to-connect-syncservice-features.md) och utvärdera vilka funktioner du ska aktivera.

### <a name="on-premises-active-directory"></a>Lokalt Active Directory
* AD-schemaversionen och skogens funktionalitetsnivå måste vara Windows Server 2003 eller senare. Domänkontrollanterna kan köra vilken version som helst så länge schema- och skogsnivåkraven är uppfyllda.
* Om du planerar att använda **tillbakaskrivningen av funktionslösenordet**måste domänkontrollanterna finnas på Windows Server 2008 R2 eller senare.
* Domänkontrollanten som används av Azure AD måste vara skrivbar. Det **stöds inte** att använda en RODC (skrivskyddad domänkontrollant) och Azure AD Connect följer inte några skrivomdirigeringar.
* Det **stöds inte** att använda lokala skogar/domäner med "prickade" (namnet innehåller en punkt ".") NetBios namn.
* Vi rekommenderar att [du aktiverar Papperskorgen i Active Directory](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect-server
>[!IMPORTANT]
>Azure AD Connect-servern innehåller kritiska identitetsdata och bör behandlas som en nivå 0-komponent som dokumenteras i [Active Directory-administratörsnivåmodellen](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Det går inte att installera Azure AD Connect på Small Business Server eller Windows Server Essentials före 2019 (Windows Server Essentials 2019 stöds). Servern måste använda Windows Server standard eller bättre.
* Installera Azure AD Connect på en domänkontrollant rekommenderas inte på grund av säkerhetsrutiner och mer restriktiva inställningar som kan förhindra att Azure AD Connect installeras korrekt.
* Azure AD Connect-servern måste ha ett fullständigt guidning installerat. Det **stöds inte** att installera på serverkärna.
>[!IMPORTANT]
>Det går inte att installera Azure AD Connect på server, server essentials eller serverkärna.

* Azure AD Connect måste installeras på Windows Server 2012 eller senare. Den här servern måste vara domänansluten och kan vara en domänkontrollant eller medlemsserver.
* Azure AD Connect-servern får inte ha PowerShell Transcription Group Policy aktiverad om du använder Azure AD Connect-guiden för att hantera ADFS-konfiguration. Du kan aktivera PowerShell-transkription om du använder Azure AD Connect-guiden för att hantera synkroniseringskonfiguration.
* Om Active Directory Federation Services distribueras måste servrarna där AD FS- eller Webbprogramproxyn är installerade vara Windows Server 2012 R2 eller senare. [Fjärrhantering i Windows](#windows-remote-management) måste vara aktiverat på dessa servrar för fjärrinstallation.
* Om Active Directory Federation Services distribueras behöver du [TLS/SSL-certifikat](#tlsssl-certificate-requirements).
* Om Active Directory Federation Services distribueras måste du konfigurera [namnmatchning](#name-resolution-for-federation-servers).
* Om dina globala administratörer har MFA **https://secure.aadcdn.microsoftonline-p.com** aktiverat måste webbadressen finnas i listan betrodda platser. Du uppmanas att lägga till den här webbplatsen i listan betrodda platser när du uppmanas att anta en MFA-utmaning och den har inte lagts till tidigare. Du kan använda Internet Explorer för att lägga till det på dina betrodda webbplatser.
* Microsoft rekommenderar att du härdar din Azure AD Connect-server för att minska säkerhetsattackytan för den här kritiska komponenten i IT-miljön.  Om du följer rekommendationerna nedan minskar säkerhetsriskerna för din organisation.

* Distribuera Azure AD Connect på en domänansluten server och begränsa administrativ åtkomst till domänadministratörer eller andra hårt kontrollerade säkerhetsgrupper.

Du kan läsa mer här: 

* [Skydda administratörsgrupper](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Skydda inbyggda administratörskonton](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Säkerhetsförbättring och underhåll genom att minska angreppsytor](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Minska Active Directory-angreppsytan](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server som används av Azure AD Connect
* Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Som standard installeras en SQL Server 2012 Express LocalDB (en lätt version av SQL Server Express). SQL Server Express har en storleksgräns på 10 GB som gör att du kan hantera cirka 100 000 objekt. Om du behöver hantera en högre volym katalogobjekt måste du peka installationsguiden på en annan installation av SQL Server. Den typ av SQL Server-installation kan påverka prestanda för [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Om du använder en annan installation av SQL Server gäller följande krav:
  * Azure AD Connect stöder alla versioner av Microsoft SQL Server från 2012 (med senaste Service Pack) till SQL Server 2019. Microsoft Azure SQL Database **stöds inte** som en databas.
  * Du måste använda en skiftlägesokänslig SQL-sortering. Dessa sorteringar identifieras \_med en CI_ i deras namn. Det **stöds inte** att använda en skiftlägeskänslig \_sortering, som identifieras av CS_ i deras namn.
  * Du kan bara ha en synkroniseringsmotor per SQL-instans. Det **stöds inte** att dela en SQL-instans med FIM/MIM Sync, DirSync eller Azure AD Sync.

### <a name="accounts"></a>Konton
* Ett Globalt Azure AD-administratörskonto för Den Azure AD-klient som du vill integrera med. Det här kontot måste vara ett **skol- eller organisationskonto** och kan inte vara ett **Microsoft-konto**.
* Om du använder [snabbinställningar](reference-connect-accounts-permissions.md#express-settings-installation) eller uppgraderar från DirSync måste du ha ett Enterprise Administrator-konto för din lokala Active Directory.
* Om du använder installationssökvägen för anpassade inställningar har du fler alternativ se [Konton i Active Directory](reference-connect-accounts-permissions.md#custom-installation-settings)

### <a name="connectivity"></a>Anslutning
* Azure AD Connect-servern behöver DNS-lösning för både intranät och internet. DNS-servern måste kunna matcha namn både till din lokala Active Directory och Azure AD-slutpunkterna.
* Om du har brandväggar på intranätet och du behöver öppna portar mellan Azure AD Connect-servrarna och domänkontrollanterna läser du [Azure AD Connect Ports](reference-connect-ports.md) för mer information.
* Om proxy- eller brandväggsgränsen vilka webbadresser som kan nås måste webbadresserna som dokumenteras i [Office 365-url:er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) öppnas.
  * Om du använder Microsoft Cloud i Tyskland eller Microsoft Azure Government-molnet läser du [azure AD Connect-synkroniseringstjänstinstanser som gäller för webbadresser.](reference-connect-instances.md)
* Azure AD Connect (version 1.1.614.0 och efter) använder som standard TLS 1.2 för att kryptera kommunikation mellan synkroniseringsmotorn och Azure AD. Om TLS 1.2 inte är tillgängligt i det underliggande operativsystemet faller Azure AD Connect stegvis tillbaka till äldre protokoll (TLS 1.1 och TLS 1.0).
* Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1.0 för att kryptera kommunikation mellan synkroniseringsmotorn och Azure AD. Om du vill ändra till TLS 1.2 följer du stegen i [Aktivera TLS 1.2 för Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Om du använder en utgående proxy för anslutning till Internet måste följande inställning i **filen C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** läggas till för installationsguiden och Azure AD Connect-synkroniseringen för att kunna ansluta till Internet och Azure AD. Texten måste anges längst ned i filen. I den &lt;här koden&gt; representerar PROXYADDRESS den faktiska proxy-IP-adressen eller värdnamnet.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Om proxyservern kräver autentisering måste [tjänstkontot](reference-connect-accounts-permissions.md#adsync-service-account) finnas i domänen och du måste använda installationssökvägen för anpassade inställningar för att ange ett [anpassat tjänstkonto](how-to-connect-install-custom.md#install-required-components). Du behöver också en annan förändring till machine.config. Med den här ändringen i machine.config svarar installationsguiden och synkroniseringsmotorn på autentiseringsbegäranden från proxyservern. På alla sidor i installationsguiden, exklusive sidan **Konfigurera,** används inloggade användares autentiseringsuppgifter. På sidan **Konfigurera** i slutet av installationsguiden växlas kontexten till [det tjänstkonto](reference-connect-accounts-permissions.md#adsync-service-account) som du har skapat. Avsnittet machine.config ska se ut så här.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* När Azure AD Connect skickar en webbbegäran till Azure AD som en del av katalogsynkronisering kan Det ta upp till 5 minuter att svara på Azure AD. Det är vanligt att proxyservrar har konfiguration för inaktiv anslutningsutgång. Kontrollera att konfigurationen är inställd på minst 6 minuter eller mer.

Mer information finns i MSDN om [standardproxyelementet](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Mer information om när du har problem med anslutningen finns i [Felsöka anslutningsproblem](tshoot-connect-connectivity.md).

### <a name="other"></a>Annat
* Valfritt: Ett testanvändarkonto för att verifiera synkroniseringen.

## <a name="component-prerequisites"></a>Komponentförkunskaper
### <a name="powershell-and-net-framework"></a>PowerShell och .NET Framework
Azure AD Connect är beroende av Microsoft PowerShell och .NET Framework 4.5.1. Du behöver den här versionen eller en senare version installerad på servern. Gör följande beroende på din Windows Server-version:

* Windows Server 2012R2
  * Microsoft PowerShell installeras som standard. Ingen åtgärd krävs.
  * .NET Framework 4.5.1 och senare versioner erbjuds via Windows Update. Kontrollera att du har installerat de senaste uppdateringarna för Windows Server på Kontrollpanelen.
* Windows Server 2012
  * Den senaste versionen av Microsoft PowerShell finns i **Windows Management Framework 4.0**, som finns på [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 och senare versioner finns på [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivera TLS 1.2 för Azure AD Connect
Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1.0 för att kryptera kommunikationen mellan synkroniseringsmotorservern och Azure AD. Du kan ändra detta genom att konfigurera .NET-program så att TLS 1.2 som standard används på servern. Mer information om TLS 1.2 finns i [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Kontrollera att snabbkorrigeringen .NET 4.5.1 är installerad för operativsystemet, se [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Snabbkorrigeringen eller en senare version kanske redan är installerad på servern.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319 "SchUseStrongCrypto"=dword:00000001
    ```
4. If you also want to enable TLS 1.2 between the sync engine server and a remote SQL Server, then make sure you have the required versions installed for [TLS 1.2 support for Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## Prerequisites for federation installation and configuration
### Windows Remote Management
When using Azure AD Connect to deploy Active Directory Federation Services or the Web Application Proxy, check these requirements:

* If the target server is domain joined, then ensure that Windows Remote Managed is enabled
  * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
* If the target server is a non-domain joined WAP machine, then there are a couple of additional requirements
  * On the target machine (WAP machine):
    * Ensure the winrm (Windows Remote Management / WS-Management) service is running via the Services snap-in
    * In an elevated PowerShell command window, use command `Enable-PSRemoting –force`
  * On the machine on which the wizard is running (if the target machine is non-domain joined or untrusted domain):
    * In an elevated PowerShell command window, use the command `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * add DMZ WAP host to machine pool (server manager -> Manage -> Add Servers...use DNS tab)
      * Server Manager All Servers tab: right click WAP server and choose Manage As..., enter local (not domain) creds for the WAP machine
      * To validate remote PowerShell connectivity, in the Server Manager All Servers tab: right click WAP server and choose Windows PowerShell. A remote PowerShell session should open to ensure remote PowerShell sessions can be established.

### TLS/SSL Certificate Requirements
* It’s strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
* The certificate must be an X509 certificate.
* You can use a self-signed certificate on federation servers in a test lab environment. However, for a production environment, we recommend that you obtain the certificate from a public CA.
  * If using a certificate that is not publicly trusted, ensure that the certificate installed on each Web Application Proxy server is trusted on both the local server and on all federation servers
* The identity of the certificate must match the federation service name (for example, sts.contoso.com).
  * The identity is either a subject alternative name (SAN) extension of type dNSName or, if there are no SAN entries, the subject name specified as a common name.  
  * Multiple SAN entries can be present in the certificate, provided one of them matches the federation service name.
  * If you are planning to use Workplace Join, an additional SAN is required with the value **enterpriseregistration.** followed by the User Principal Name (UPN) suffix of your organization, for example, **enterpriseregistration.contoso.com**.
* Certificates based on CryptoAPI next generation (CNG) keys and key storage providers are not supported. This means you must use a certificate based on a CSP (cryptographic service provider) and not a KSP (key storage provider).
* Wild-card certificates are supported.

### Name resolution for federation servers
* Set up DNS records for the AD FS federation service name (for example sts.contoso.com) for both the intranet (your internal DNS server) and the extranet (public DNS through your domain registrar). For the intranet DNS record, ensure that you use A records and not CNAME records. This is required for windows authentication to work correctly from your domain joined machine.
* If you are deploying more than one AD FS server or Web Application Proxy server, then ensure that you have configured your load balancer and that the DNS records for the AD FS federation service name (for example sts.contoso.com) point to the load balancer.
* For windows integrated authentication to work for browser applications using Internet Explorer in your intranet, ensure that the AD FS federation service name (for example sts.contoso.com) is added to the intranet zone in IE. This can be controlled via group policy and deployed to all your domain joined computers.

## Azure AD Connect supporting components
The following is a list of components that Azure AD Connect installs on the server where Azure AD Connect is installed. This list is for a basic Express installation. If you choose to use a different SQL Server on the Install synchronization services page, then SQL Express LocalDB is not installed locally.

* Azure AD Connect Health
* Microsoft SQL Server 2012 Command Line Utilities
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## Hardware requirements for Azure AD Connect
The table below shows the minimum requirements for the Azure AD Connect sync computer.

| Number of objects in Active Directory | CPU | Memory | Hard drive size |
| --- | --- | --- | --- |
| Fewer than 10,000 |1.6 GHz |4 GB |70 GB |
| 10,000–50,000 |1.6 GHz |4 GB |70 GB |
| 50,000–100,000 |1.6 GHz |16 GB |100 GB |
| For 100,000 or more objects the full version of SQL Server is required | | | |
| 100,000–300,000 |1.6 GHz |32 GB |300 GB |
| 300,000–600,000 |1.6 GHz |32 GB |450 GB |
| More than 600,000 |1.6 GHz |32 GB |500 GB |

The minimum requirements for computers running AD FS or Web Application Proxy Servers is the following:

* CPU: Dual core 1.6 GHz or higher
* MEMORY: 2 GB or higher
* Azure VM: A2 configuration or higher

## Next steps
Learn more about [Integrating your on-premises identities with Azure Active Directory](whatis-hybrid-identity.md).
