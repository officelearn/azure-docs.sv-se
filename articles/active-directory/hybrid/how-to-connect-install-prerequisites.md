---
title: 'Azure AD Connect: krav och maskin vara | Microsoft Docs'
description: I det här avsnittet beskrivs krav och maskin varu krav för Azure AD Connect
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
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11d5c1bb133f0aea241fbc55f96ab5f8818e5ed6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518121"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Förhandskrav för Azure AD Connect
I det här avsnittet beskrivs krav och maskin varu krav för Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Innan du installerar Azure AD Connect
Innan du installerar Azure AD Connect finns det några saker du behöver.

### <a name="azure-ad"></a>Azure AD
* En Azure AD-klientorganisation. Du får ett med en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). Du kan använda någon av följande portaler för att hantera Azure AD Connect:
  * [Azure Portal](https://portal.azure.com).
  * [Office-portalen](https://portal.office.com).  
* [Lägg till och verifiera den domän](../active-directory-domains-add-azure-portal.md) som du planerar att använda i Azure AD. Om du till exempel planerar att använda contoso.com för dina användare ser du till att den här domänen har verifierats och att du inte bara använder contoso.onmicrosoft.com standard domän.
* En Azure AD-klient tillåter som standard 50 000-objekt. När du verifierar din domän ökas gränsen till över 300 000-objekt. Om du behöver ännu fler objekt i Azure AD måste du öppna ett support ärende om du vill öka gränsen ytterligare. Om du behöver fler än 500 000-objekt måste du ha en licens, till exempel Office 365, Azure AD Premium eller Enterprise Mobility och Security.

### <a name="prepare-your-on-premises-data"></a>Förbered dina lokala data
* Använd [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) för att identifiera fel som dubbletter och formateringsfel i din katalog innan du synkroniserar till Azure AD och Office 365.
* Granska [valfria Sync-funktioner som du kan aktivera i Azure AD](how-to-connect-syncservice-features.md) och utvärdera vilka funktioner du bör aktivera.

### <a name="on-premises-active-directory"></a>Lokalt Active Directory
* AD-schema versionen och skogens funktions nivå måste vara Windows Server 2003 eller senare. Domän kontrol Lanterna kan köra vilken version som helst så länge som kraven på schemat och på skogs nivån är uppfyllda.
* Om du planerar att använda tillbakaskrivning av funktions **lösen ord**måste domän kontrol Lanterna vara på Windows Server 2008 R2 eller senare.
* Domänkontrollanten som används av Azure AD måste vara skrivbar. Det finns **inte stöd** för att använda en skrivskyddad domänkontrollant (skrivskyddad domänkontrollant) och Azure AD Connect inte följer några Skriv omdirigeringar.
* Det **går inte** att använda lokala skogar/domäner med "prickad" (namnet innehåller en punkt ".") NetBios-namn.
* Vi rekommenderar att du [aktiverar Active Directory pappers korgen](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect Server
Azure AD Connect servern innehåller kritiska identitets data. Det är viktigt att administrativ åtkomst till den här servern är korrekt skyddad, enligt rikt linjerna som dokumenteras i [skydda privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Azure AD Connect-servern måste behandlas som en nivå 0-komponent som dokumenteras i [Active Directory administrativ nivå modell](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).  

Mer information om hur du skyddar din Active Directory-miljö finns i [metod tips för att skydda Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Installationskrav 

- Azure AD Connect måste installeras på en domänansluten Windows Server 2012 eller senare. 
- Azure AD Connect kan inte installeras på Small Business Server eller Windows Server Essentials före 2019 (Windows Server Essentials 2019 stöds). Servern måste använda Windows Server standard eller bättre.  
- Den Azure AD Connect servern måste ha ett fullständigt GUI installerat. Det finns inte stöd för att installera Azure AD Connect på Windows Server Core. 
- Den Azure AD Connect servern får inte ha PowerShell-avskrifts grupprincip aktiverat om du använder Azure AD Connect guiden för att hantera ADFS-konfiguration. Du kan aktivera PowerShell-avskriftning om du använder Azure AD Connects guiden för att hantera synkronisering av konfigurationen. 
- Om Active Directory Federation Services (AD FS) distribueras: 
    - servrarna där AD FS eller Webbprogramproxy installeras måste vara Windows Server 2012 R2 eller senare. Windows Remote Management måste vara aktiverat på dessa servrar för fjärrinstallation. 
    - Du måste konfigurera TLS/SSL-certifikat.  Se [Hantera SSL/TLS-protokoll och chiffersviter för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) och [Hantera SSL-certifikat i AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Du måste konfigurera namn matchning. 
- Om dina globala administratörer har MFA aktiverat måste URL: en https://secure.aadcdn.microsoftonline-p.com **must** vara i listan över betrodda platser. Du uppmanas att lägga till den här platsen i listan över betrodda platser när du uppmanas att ange en MFA-utmaning och inte har lagt till tidigare. Du kan använda Internet Explorer för att lägga till den på dina betrodda platser.  

#### <a name="hardening-your-azure-ad-connect-server"></a>Härdning av Azure AD Connect-servern 
Microsoft rekommenderar att du skärper Azure AD Connect-servern för att minska säkerhets attack ytan för den här viktiga komponenten i din IT-miljö. Genom att följa rekommendationerna nedan kan du hjälpa till att minimera vissa säkerhets risker för din organisation.

- Du måste behandla Azure AD Connect samma som en domänkontrollant och andra nivå 0-resurser:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material 
- Du bör begränsa administrativ åtkomst till Azure AD Connect-servern till endast domän administratörer eller andra tätt kontrollerade säkerhets grupper.
- Du bör skapa ett [dedikerat konto för all personal med privilegie rad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). Administratörer bör inte surfa på webben, kontrol lera sin e-post och utföra dagliga produktivitets uppgifter med konton med hög behörighet.
- Du bör följa vägledningen som ges i [skydda privilegie rad åtkomst](https://docs.microsoft.com/windows-server/security/credentials-protection-and-management/how-to-configure-protected-accounts). 
- Se till att varje dator har ett unikt lokalt administratörs lösen ord. Den [lokala administratörens lösen ords lösning (upphörde)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) kan konfigurera unika slumpmässiga lösen ord på varje arbets Station och Server lagrar dem i Active Directory (AD) som skyddas av en ACL. Endast berättigade behöriga användare kan läsa eller begära återställning av lösen ord för det lokala administratörs kontot. Du kan få en uppgång för användning på arbets stationer och servrar från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=46899#:~:text=The%20%22Local%20Administrator%20Password%20Solution,it%20or%20request%20its%20reset.). Ytterligare vägledning för att använda en miljö med en fördröjning och Paw finns i [operativa standarder baserade på principen om ren källa](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Du bör implementera dedikerade [arbets stationer för privilegie rad åtkomst (Paw)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) för all personal med privilegie rad åtkomst till din organisations informations system. 
- Du bör följa dessa [rikt linjer](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) för att minska risken för angrepp i Active Directory miljön.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server som används av Azure AD Connect
* Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Som standard är en SQL Server 2012 Express-LocalDB (en låg version av SQL Server Express) installerad. SQL Server Express har en storleks gräns på 10 GB som gör att du kan hantera cirka 100 000 objekt. Om du behöver hantera en större mängd katalog objekt måste du peka installations guiden till en annan installation av SQL Server. SQL Server-installationens typ kan påverka [prestandan för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Om du använder en annan installation av SQL Server gäller dessa krav:
  * Azure AD Connect stöder alla versioner av Microsoft SQL Server från 2012 (med senaste Service Pack) till SQL Server 2019. Microsoft Azure SQL Database **stöds inte** som en databas.
  * Du måste använda en Skift läges okänslig SQL-sortering. Dessa sorteringar identifieras med ett \_ CI_ i sitt namn. Det finns **inte stöd** för att använda en Skift läges känslig sortering som identifieras av \_ CS_ i sitt namn.
  * Du kan bara ha en Sync-motor per SQL-instans. Det finns **inte stöd** för att dela en SQL-instans med FIM/MIM Sync, DirSync eller Azure AD Sync.

### <a name="accounts"></a>Konton
* Ett globalt administratörs konto i Azure AD för den Azure AD-klient som du vill integrera med. Kontot måste vara ett **skol-eller organisations konto** och får inte vara ett **Microsoft-konto**.
* Om du använder [Express inställningar](reference-connect-accounts-permissions.md#express-settings-installation) eller uppgraderar från DirSync måste du ha ett företags administratörs konto för din lokala Active Directory.
* Om du använder installations Sök vägen för anpassade inställningar har du fler alternativ. Mer information finns i [anpassade installations inställningar](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Anslutningar
* Den Azure AD Connect servern behöver DNS-matchning för både intranätet och Internet. DNS-servern måste kunna matcha namn både till din lokala Active Directory och Azure AD-slutpunkter.
* Om du har brand väggar i intranätet och du behöver öppna portar mellan Azure AD Connect-servrar och domän kontrol Lanterna, kan du se [Azure AD Connect portar](reference-connect-ports.md) för mer information.
* Om proxyn eller brand väggen begränsar vilka URL: er som kan nås måste URL: erna som dokumenteras i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) öppnas.
  * Om du använder Microsoft Cloud i Tyskland eller Microsoft Azure Government molnet kan du läsa mer om att [tänka på Azure AD Connect Sync service-instanser](reference-connect-instances.md) för URL: er.
* Azure AD Connect (version 1.1.614.0 och efter) som standard använder TLS 1,2 för kryptering av kommunikation mellan Synkroniseringsmotorn och Azure AD. Om TLS 1,2 inte är tillgängligt i det underliggande operativ systemet går Azure AD Connect stegvis tillbaka till äldre protokoll (TLS 1,1 och TLS 1,0).
* Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1,0 för kryptering av kommunikation mellan Synkroniseringsmotorn och Azure AD. Om du vill ändra till TLS 1,2 följer du stegen i [Aktivera tls 1,2 för Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Om du använder en utgående proxy för att ansluta till Internet måste följande inställning i **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** -filen läggas till för installations guiden och Azure AD Connect synkronisering för att kunna ansluta till Internet och Azure AD. Texten måste anges längst ned i filen. I den här koden &lt; &gt; representerar PROXYADDRESS den faktiska proxy-IP-adressen eller värd namnet.

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

* Om proxyservern kräver autentisering måste [tjänst kontot](reference-connect-accounts-permissions.md#adsync-service-account) finnas i domänen och du måste använda installations Sök vägen för anpassade inställningar för att ange ett [anpassat tjänst konto](how-to-connect-install-custom.md#install-required-components). Du behöver också en annan ändring i machine.config. Med den här ändringen i machine.config svarar installations guiden och synkroniseringen av autentiseringsbegäranden från proxyservern. I alla installations guide sidor, förutom sidan **Konfigurera** , används den inloggade användarens autentiseringsuppgifter. På sidan **Konfigurera** i slutet av installations guiden växlas kontexten till det [tjänst konto](reference-connect-accounts-permissions.md#adsync-service-account) som skapades av dig. Avsnittet machine.config bör se ut så här.

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

* När Azure AD Connect skickar en webbegäran till Azure AD som en del av Active Directory-synkroniseringen kan det ta upp till 5 minuter för Azure AD att svara. Det är vanligt att proxyservrar har en timeout-konfiguration för inaktivitet. Kontrol lera att konfigurationen är minst 6 minuter eller mer.

Mer information finns i MSDN om [default proxy-elementet](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Mer information om problem med anslutningen finns i [Felsöka anslutnings problem](tshoot-connect-connectivity.md).

### <a name="other"></a>Annat
* Valfritt: ett test användar konto för att verifiera synkroniseringen.

## <a name="component-prerequisites"></a>Komponent krav
### <a name="powershell-and-net-framework"></a>PowerShell och .NET Framework
Azure AD Connect är beroende av Microsoft PowerShell och .NET Framework 4.5.1. Du behöver den här versionen eller en senare version som är installerad på servern. Gör följande beroende på din Windows Server-version:

* Windows Server-2012R2
  * Microsoft PowerShell installeras som standard. Ingen åtgärd krävs.
  * .NET Framework 4.5.1 och senare versioner erbjuds genom Windows Update. Kontrol lera att du har installerat de senaste uppdateringarna för Windows Server på kontroll panelen.
* Windows Server 2012
  * Den senaste versionen av Microsoft PowerShell är tillgänglig i **Windows Management Framework 4,0**, som finns på [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 och senare versioner finns på [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivera TLS 1,2 för Azure AD Connect
Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1,0 för kryptering av kommunikationen mellan Sync-databasmotorn och Azure AD. Du kan ändra detta genom att konfigurera .NET-program att använda TLS 1,2 som standard på servern. Mer information om TLS 1,2 finns i [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1.  Kontrol lera att snabb korrigeringen .NET 4.5.1 har installerats för operativ systemet, se [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Du kanske har den här snabb korrigeringen eller också har en senare version installerad på servern redan.
    ```
2. For all operating systems, set this registry key and restart the server.
    ```
    HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft \. NETFramework\v4.0.30319 "SchUseStrongCrypto" = DWORD: 00000001
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
* It's strongly recommended to use the same TLS/SSL certificate across all nodes of your AD FS farm and all Web Application proxy servers.
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
