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
ms.topic: conceptual
ms.date: 05/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2db8d5881b5847adca4fffb72c0a678e1ec550c
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596315"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Krav för Azure AD Connect
I det här avsnittet beskrivs krav och maskin varu krav för Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Innan du installerar Azure AD Connect
Innan du installerar Azure AD Connect finns det några saker du behöver.

### <a name="azure-ad"></a>Azure AD
* En Azure AD-klientorganisation. Du får ett med en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). Du kan använda någon av följande portaler för att hantera Azure AD Connect:
  * [Azure Portal](https://portal.azure.com).
  * [Office-portalen](https://portal.office.com).  
* [Lägg till och verifiera den domän](../active-directory-domains-add-azure-portal.md) som du planerar att använda i Azure AD. Om du till exempel planerar att använda contoso.com för dina användare ser du till att den här domänen har verifierats och att du inte bara använder contoso.onmicrosoft.com standard domän.
* En Azure AD-klient tillåter som standard 50 000-objekt. När du verifierar din domän ökas gränsen till över 300 000-objekt. Om du behöver ännu fler objekt i Azure AD måste du öppna ett support ärende om du vill öka gränsen ytterligare. Om du behöver fler än 500 000-objekt måste du ha en licens, till exempel Office 365, Azure AD Basic, Azure AD Premium eller företags mobilitet och säkerhet.

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
>[!IMPORTANT]
>Azure AD Connect servern innehåller kritiska identitets data och bör behandlas som en komponent på nivå 0 som dokumenteras i [den Active Directory administrativa nivå modellen](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

* Azure AD Connect kan inte installeras på Small Business Server eller Windows Server Essentials före 2019 (Windows Server Essentials 2019 stöds). Servern måste använda Windows Server standard eller bättre.
* Det rekommenderas inte att installera Azure AD Connect på en domänkontrollant på grund av säkerhets rutiner och mer restriktiva inställningar som kan förhindra att Azure AD Connect installeras på rätt sätt.
* Den Azure AD Connect servern måste ha ett fullständigt GUI installerat. Det finns **inte stöd** för att installera på Server Core.
>[!IMPORTANT]
>Det finns inte stöd för att installera Azure AD Connect på Small Business Server, Server Essentials eller Server Core.

* Azure AD Connect måste installeras på Windows Server 2008 R2 eller senare. Den här servern måste vara domänansluten och kan vara en domänkontrollant eller en medlems Server.
* Om du installerar Azure AD Connect på Windows Server 2008 R2 ska du kontrol lera att du använder de senaste snabb korrigeringarna från Windows Update. Installationen kan inte starta med en server som inte har korrigerats.
* Om du planerar att använda **synkroniseringen av funktions lösen ord**måste Azure AD Connect servern vara på Windows Server 2008 R2 SP1 eller senare.
* Om du planerar att använda ett **grupphanterat tjänst konto**måste Azure AD Connect servern vara på Windows Server 2012 eller senare.
* Azure AD Connect-servern måste ha [.NET Framework 4.5.1](#component-prerequisites) eller senare och [Microsoft PowerShell 3,0](#component-prerequisites) eller senare installerad.
* Den Azure AD Connect servern får inte ha PowerShell-avskrifts grupprincip aktiverat.
* Om Active Directory Federation Services (AD FS) distribueras måste de servrar där AD FS eller Webbprogramproxy installeras vara Windows Server 2012 R2 eller senare. [Windows Remote Management](#windows-remote-management) måste vara aktiverat på dessa servrar för fjärrinstallation.
* Om Active Directory Federation Services (AD FS) distribueras behöver du [SSL-certifikat](#ssl-certificate-requirements).
* Om Active Directory Federation Services (AD FS) distribueras måste du konfigurera [namn matchning](#name-resolution-for-federation-servers).
* Om dina globala administratörer har MFA aktiverat måste URL: en **https://secure.aadcdn.microsoftonline-p.com** finnas i listan över betrodda platser. Du uppmanas att lägga till den här platsen i listan över betrodda platser när du uppmanas att ange en MFA-utmaning och inte har lagt till tidigare. Du kan använda Internet Explorer för att lägga till den på dina betrodda platser.
* Microsoft rekommenderar att du skärper Azure AD Connect-servern för att minska säkerhets attack ytan för den här viktiga komponenten i din IT-miljö.  Genom att följa rekommendationerna nedan minskar du säkerhets riskerna för din organisation.

* Distribuera Azure AD Connect på en domänansluten Server och begränsa administrativ åtkomst till domän administratörer eller andra tätt kontrollerade säkerhets grupper.

Du kan läsa mer här: 

* [Skydda administratörer grupper](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-g--securing-administrators-groups-in-active-directory)

* [Skydda inbyggda administratörs konton](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-d--securing-built-in-administrator-accounts-in-active-directory)

* [Säkerhets förbättring och skydd genom att minska attack ytorna](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access#2-reduce-attack-surfaces )

* [Minska den Active Directory angrepps ytan](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server som används av Azure AD Connect
* Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. Som standard är en SQL Server 2012 Express-LocalDB (en låg version av SQL Server Express) installerad. SQL Server Express har en storleks gräns på 10 GB som gör att du kan hantera cirka 100 000 objekt. Om du behöver hantera en större mängd katalog objekt måste du peka installations guiden till en annan installation av SQL Server. SQL Server-installationens typ kan påverka [prestandan för Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors#sql-database-factors).
* Om du använder en annan installation av SQL Server gäller dessa krav:
  * Azure AD Connect stöder alla versioner av Microsoft SQL Server från 2008 R2 (med senaste Service Pack) till SQL Server 2019. Microsoft Azure SQL Database **stöds inte** som en databas.
  * Du måste använda en Skift läges okänslig SQL-sortering. Dessa sorteringar identifieras med ett \_CI_ i sitt namn. Det finns **inte stöd** för att använda en Skift läges känslig sortering som identifieras av \_CS_ i sitt namn.
  * Du kan bara ha en Sync-motor per SQL-instans. Det finns **inte stöd** för att dela en SQL-instans med FIM/MIM Sync, DirSync eller Azure AD Sync.

### <a name="accounts"></a>Konton
* Ett globalt administratörs konto i Azure AD för den Azure AD-klient som du vill integrera med. Kontot måste vara ett **skol-eller organisations konto** och får inte vara ett **Microsoft-konto**.
* Om du använder Express inställningar eller uppgraderar från DirSync måste du ha ett företags administratörs konto för din lokala Active Directory.
* [Konton i Active Directory](reference-connect-accounts-permissions.md) om du använder installations Sök vägen för anpassade inställningar eller ett företags administratörs konto för din lokala Active Directory.

### <a name="connectivity"></a>Anslutningsmöjlighet
* Den Azure AD Connect servern behöver DNS-matchning för både intranätet och Internet. DNS-servern måste kunna matcha namn både till din lokala Active Directory och Azure AD-slutpunkter.
* Om du har brand väggar i intranätet och du behöver öppna portar mellan Azure AD Connect-servrar och domän kontrol Lanterna, kan du se [Azure AD Connect portar](reference-connect-ports.md) för mer information.
* Om proxyn eller brand väggen begränsar vilka URL: er som kan nås måste URL: erna som dokumenteras i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) öppnas.
  * Om du använder Microsoft Cloud i Tyskland eller Microsoft Azure Government molnet kan du läsa mer om att [tänka på Azure AD Connect Sync service-instanser](reference-connect-instances.md) för URL: er.
* Azure AD Connect (version 1.1.614.0 och efter) som standard använder TLS 1,2 för kryptering av kommunikation mellan Synkroniseringsmotorn och Azure AD. Om TLS 1,2 inte är tillgängligt i det underliggande operativ systemet går Azure AD Connect stegvis tillbaka till äldre protokoll (TLS 1,1 och TLS 1,0).
* Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1,0 för kryptering av kommunikation mellan Synkroniseringsmotorn och Azure AD. Om du vill ändra till TLS 1,2 följer du stegen i [Aktivera tls 1,2 för Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Om du använder en utgående proxy för att ansluta till Internet måste följande inställning i **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** -filen läggas till för installations guiden och Azure AD Connect synkronisering för att kunna för att ansluta till Internet och Azure AD. Texten måste anges längst ned i filen. I den här koden representerar &lt;PROXYADDRESS &gt; den faktiska proxy-IP-adressen eller värd namnet.

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

* Om proxyservern kräver autentisering måste [tjänst kontot](reference-connect-accounts-permissions.md#adsync-service-account) finnas i domänen och du måste använda installations Sök vägen för anpassade inställningar för att ange ett [anpassat tjänst konto](how-to-connect-install-custom.md#install-required-components). Du behöver också en annan ändring av Machine. config. Med den här ändringen i Machine. config, svarar installations guiden och synkroniseringen av autentiseringsbegäranden från proxyservern. I alla installations guide sidor, förutom sidan **Konfigurera** , används den inloggade användarens autentiseringsuppgifter. På sidan **Konfigurera** i slutet av installations guiden växlas kontexten till det [tjänst konto](reference-connect-accounts-permissions.md#adsync-service-account) som skapades av dig. Avsnittet Machine. config bör se ut så här.

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

### <a name="other"></a>Övrigt
* Valfritt: ett test användar konto för att verifiera synkroniseringen.

## <a name="component-prerequisites"></a>Komponent krav
### <a name="powershell-and-net-framework"></a>PowerShell och .NET Framework
Azure AD Connect är beroende av Microsoft PowerShell och .NET Framework 4.5.1. Du behöver den här versionen eller en senare version som är installerad på servern. Gör följande beroende på din Windows Server-version:

* Windows Server-2012R2
  * Microsoft PowerShell installeras som standard. Ingen åtgärd krävs.
  * .NET Framework 4.5.1 och senare versioner erbjuds genom Windows Update. Kontrol lera att du har installerat de senaste uppdateringarna för Windows Server på kontroll panelen.
* Windows Server 2008 R2 och Windows Server 2012
  * Den senaste versionen av Microsoft PowerShell är tillgänglig i **Windows Management Framework 4,0**, som finns på [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 och senare versioner finns på [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivera TLS 1,2 för Azure AD Connect
Före version 1.1.614.0 använder Azure AD Connect som standard TLS 1,0 för kryptering av kommunikationen mellan Sync-databasmotorn och Azure AD. Du kan ändra detta genom att konfigurera .NET-program att använda TLS 1,2 som standard på servern. Mer information om TLS 1,2 finns i [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1,2 kan inte aktive ras före Windows Server 2008 R2 eller tidigare. Kontrol lera att snabb korrigeringen .NET 4.5.1 har installerats för operativ systemet, se [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Du kanske har den här snabb korrigeringen eller också har en senare version installerad på servern redan.
2. Om du använder Windows Server 2008 R2 kontrollerar du att TLS 1,2 är aktiverat. På Windows Server 2012 Server och senare versioner ska TLS 1,2 redan vara aktiverat.
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    ```
3. För alla operativ system anger du register nyckeln och startar om servern.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
4. Om du även vill aktivera TLS 1,2 mellan Synkroniseringsmotorn och en fjärran sluten SQL Server kontrollerar du att du har de versioner som krävs installerade för [TLS 1,2-stöd för Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Krav för Federations installation och konfiguration
### <a name="windows-remote-management"></a>Windows Remote Management
När du använder Azure AD Connect för att distribuera Active Directory Federation Services (AD FS) eller Webbprogramproxy kontrollerar du följande krav:

* Om mål servern är domänansluten, kontrollerar du att Windows Remote Managed är aktiverat
  * Använd kommandot `Enable-PSRemoting –force` i ett upphöjd kommando fönster för PSH
* Om mål servern är en icke-domänansluten WAP-dator finns det ett par ytterligare krav
  * På mål datorn (WAP-dator):
    * Se till att tjänsten WinRM (Windows Remote Management/WS-Management) körs via snapin-modulen tjänster
    * Använd kommandot `Enable-PSRemoting –force` i ett upphöjd kommando fönster för PSH
  * På den dator där guiden körs (om mål datorn är icke-domänansluten eller obetrodd domän):
    * Använd kommandot `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` i ett upphöjd kommando fönster för PSH
    * I Serverhanteraren:
      * Lägg till DMZ WAP-värd i resurspoolen (Server Manager-> Hantera-> Lägg till servrar... Använd fliken DNS)
      * Fliken Serverhanteraren alla servrar: Högerklicka på WAP server och välj Hantera som..., ange lokala (inte domän) autentiseringsuppgifter för WAP-datorn
      * Om du vill verifiera fjärr anslutning för PSH går du till fliken Serverhanteraren alla servrar: Högerklicka på WAP-server och välj Windows PowerShell. En PSH-session bör vara öppen för att säkerställa att fjärranslutna PowerShell-sessioner kan upprättas.

### <a name="ssl-certificate-requirements"></a>Krav för SSL-certifikat
* Vi rekommenderar starkt att du använder samma SSL-certifikat på alla noder i din AD FS-server grupp och alla Webbprogramproxy-servrar.
* Certifikatet måste vara ett X509-certifikat.
* Du kan använda ett självsignerat certifikat på Federations servrar i en test labb miljö. För en produktions miljö rekommenderar vi dock att du hämtar certifikatet från en offentlig certifikat utfärdare.
  * Om du använder ett certifikat som inte är offentligt betrott kontrollerar du att certifikatet som är installerat på varje Webbprogramproxy är betrott på både den lokala servern och på alla Federations servrar
* Identiteten för certifikatet måste matcha Federations tjänstens namn (till exempel sts.contoso.com).
  * Identiteten är antingen ett alternativt namn för certifikat mottagare (SAN) av typen dNSName eller, om det inte finns några SAN-poster, ämnes namnet anges som ett eget namn.  
  * Flera SAN-poster kan finnas i certifikatet, förutsatt att ett av dem matchar Federations tjänstens namn.
  * Om du planerar att använda Workplace Join krävs ytterligare ett SAN-nätverk med värdet **enterpriseregistration.** följt av användarens UPN-suffix (User Principal Name) för din organisation, till exempel **enterpriseregistration.contoso.com**.
* Certifikat baserade på CryptoAPI Next Generation (CNG)-nycklar och nyckel lagrings leverantörer stöds inte. Det innebär att du måste använda ett certifikat som baseras på en CSP (Cryptographic Service Provider) och inte en KSP (Key Storage Provider).
* Jokertecken stöds.

### <a name="name-resolution-for-federation-servers"></a>Namn matchning för Federations servrar
* Konfigurera DNS-poster för AD FS Federations tjänstens namn (till exempel sts.contoso.com) för både intranätet (din interna DNS-Server) och extra nätet (offentlig DNS via din domän registrator). För DNS-posten för intranätet kontrollerar du att du använder en post och inte CNAME-poster. Detta krävs för att Windows-autentisering ska fungera korrekt från din domänanslutna dator.
* Om du distribuerar fler än en AD FS server eller Webbprogramproxy, kontrollerar du att du har konfigurerat belastningsutjämnaren och att DNS-posterna för AD FS Federations tjänstens namn (till exempel sts.contoso.com) pekar på belastningsutjämnaren.
* För att Windows-integrerad autentisering ska fungera för webb läsar program med Internet Explorer i intranätet kontrollerar du att namnet på AD FS Federations tjänsten (till exempel sts.contoso.com) läggs till i zonen Intranät i IE. Detta kan styras via en grup princip och distribueras till alla domänanslutna datorer.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect stöd komponenter
Följande är en lista över komponenter som Azure AD Connect installerar på den server där Azure AD Connect är installerat. Den här listan är för en grundläggande Express installation. Om du väljer att använda en annan SQL Server på sidan Installera Synchronization Services installeras inte SQL Express-LocalDB lokalt.

* Azure AD Connect Health
* Microsoft SQL Server 2012 kommando rads verktyg
* Microsoft SQL Server 2012 Express-LocalDB
* Microsoft SQL Server 2012 Native Client
* Omdistributions paket för Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Maskin varu krav för Azure AD Connect
I tabellen nedan visas minimi kraven för Azure AD Connect Sync-datorn.

| Antal objekt i Active Directory | Processor | Minne | Hård disk storlek |
| --- | --- | --- | --- |
| Färre än 10 000 |1,6 GHz |4 GB |70 GB |
| 10000 – 50000 |1,6 GHz |4 GB |70 GB |
| 50000 – 100000 |1,6 GHz |16 GB |100 GB |
| För 100 000 eller fler objekt krävs en fullständig version av SQL Server | | | |
| 100000 – 300000 |1,6 GHz |32 GB |300 GB |
| 300000 – 600 000 |1,6 GHz |32 GB |450 GB |
| Mer än 600 000 |1,6 GHz |32 GB |500 GB |

Minimi kraven för datorer som kör AD FS-eller webb program servrar är följande:

* PROCESSOR: Dual Core 1,6 GHz eller snabbare
* MINNE: 2 GB eller högre
* Virtuell Azure-dator: a2-konfiguration eller högre

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
