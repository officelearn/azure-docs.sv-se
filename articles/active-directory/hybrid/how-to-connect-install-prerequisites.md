---
title: 'Azure AD Connect: Krav och maskinvara | Microsoft Docs'
description: Det här avsnittet beskriver kraven och maskinvarukrav för Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a36868e5bab64883036e0f93352bea5341ff7fe7
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384077"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Förhandskrav för Azure AD Connect
Det här avsnittet beskriver kraven och maskinvarukrav för Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Innan du installerar Azure AD Connect
Innan du installerar Azure AD Connect, finns det några saker som du behöver.

### <a name="azure-ad"></a>Azure AD
* En Azure AD-klientorganisation. Du får en med en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/). Du kan använda något av följande portaler för att hantera Azure AD Connect:
  * Den [Azure-portalen](https://portal.azure.com).
  * Den [Office-portalen](https://portal.office.com).  
* [Lägga till och verifiera domänen](../active-directory-domains-add-azure-portal.md) du planerar att använda i Azure AD. Om du planerar att använda contoso.com för dina användare och sedan kontrollera att den här domänen har verifierats och du bara använder inte standarddomän contoso.onmicrosoft.com.
* En Azure AD-klient kan som 50k-standardobjekt. När du verifierar din domän, ökat gränsen på 300 kB-objekt. Om du behöver ännu fler objekt i Azure AD, måste du öppna ett supportärende om du vill att den har höjts ytterligare. Om du behöver fler än 500 k-objekt kan behöver du en licens, till exempel Office 365, Azure AD Basic, Azure AD Premium eller Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Förbereda dina lokala data
* Använd [reparationsverktyg](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) identifiera fel, till exempel dubbletter och formateringsproblem i katalogen innan du synkroniserar till Azure AD och Office 365.
* Granska [valfritt synkroniseringsfunktioner som du kan aktivera i Azure AD](how-to-connect-syncservice-features.md) och utvärdera vilka funktioner som du bör aktivera.

### <a name="on-premises-active-directory"></a>Lokalt Active Directory
* AD-schemat version och skogens funktionsnivå måste vara Windows Server 2003 eller senare. Domänkontrollanter kan köra någon version så länge schemat och skog nivå krav är uppfyllda.
* Om du planerar att använda funktionen **tillbakaskrivning av lösenord**, domänkontrollanterna måste vara i Windows Server 2008 R2 eller senare.
* Domänkontrollanten som används av Azure AD måste vara skrivbar. Det är **stöds inte** för att använda en skrivskyddad Domänkontrollant (skrivskyddade domänkontrollanten) och Azure AD Connect inte följer alla skrivåtgärder omdirigeringar.
* Det är **stöds inte** att använda lokala skogar/domäner med ”prickade” (namn innehåller en punkt ””.) NetBIOS-namn.
* Det rekommenderas att [aktivera Active Directory-Papperskorgen](how-to-connect-sync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect-servern
* Azure AD Connect kan inte installeras på Small Business Server eller Windows Server Essentials innan 2019 (Windows Server Essentials 2019 stöds). Servern måste använda Windows Server standard- eller bättre.
* Azure AD Connect-servern måste ha ett fullständigt grafiskt användargränssnitt installerad. Det är **stöds inte** att installera på server core.
* Azure AD Connect måste installeras på Windows Server 2008 R2 eller senare. Den här servern kan vara en domänkontrollant eller en medlemsserver när med standardinställningar. Om du använder anpassade inställningar, servern kan också vara fristående och behöver inte vara ansluten till en domän.
* Om du installerar Azure AD Connect på Windows Server 2008 R2, se till att tillämpa de senaste snabbkorrigeringarna från Windows Update. Installationen kan inte börja med en okorrigerad server.
* Om du planerar att använda funktionen **Lösenordssynkronisering**, Azure AD Connect-servern måste vara i Windows Server 2008 R2 SP1 eller senare.
* Om du planerar att använda en **grupphanterat tjänstkonto**, Azure AD Connect-servern måste vara i Windows Server 2012 eller senare.
* Azure AD Connect-servern måste ha [.NET Framework 4.5.1](#component-prerequisites) eller senare och [Microsoft PowerShell 3.0](#component-prerequisites) eller senare installerat.
* Azure AD Connect-servern får inte ha PowerShell avskrift grupp principen är aktiverad.
* Om Active Directory Federation Services distribueras de servrar där AD FS eller Web Application Proxy är installerade måste vara Windows Server 2012 R2 eller senare. [Windows fjärrhantering](#windows-remote-management) måste vara aktiverat på dessa servrar för fjärrinstallation.
* Om Active Directory Federation Services distribueras måste [SSL-certifikat](#ssl-certificate-requirements).
* Om Active Directory Federation Services distribueras så måste du konfigurera [namnmatchning](#name-resolution-for-federation-servers).
* Om dina globala administratörer har MFA aktiverad, sedan URL: en **https://secure.aadcdn.microsoftonline-p.com** måste finnas i listan med betrodda platser. Du uppmanas att lägga till den här platsen i listan med betrodda platser när du ombeds ange en MFA-kontrollen och det inte lagts till innan du. Du kan använda Internet Explorer för att lägga till den till dina betrodda platser.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server som används av Azure AD Connect
* Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. En SQL Server 2012 Express LocalDB (ett ljust version av SQL Server Express) installeras som standard. SQL Server Express har en gräns på 10GB storlek som hjälper dig att hantera cirka 100 000 objekt. Om du behöver hantera ett ökat antal katalogobjekt, måste du peka guiden Installera på en annan installation av SQL Server.
* Om du använder en separat SQL Server gäller dessa krav:
  * Azure AD Connect stöder alla versioner av Microsoft SQL Server från SQL Server 2008 (med senaste Service Pack) till SQL Server 2017. Microsoft Azure SQL Database är **stöds inte** som en databas.
  * Du måste använda en icke skiftlägeskänslig sortering i SQL. Dessa sorteringar identifieras med en \_CI_ i sina namn. Det är **stöds inte** att använda en skiftlägeskänslig sortering, identifieras av \_CS_ i sina namn.
  * Du kan bara ha en Synkroniseringsmotorn per SQL-instans. Det är **stöds inte** att dela en SQL-instans med FIM/MIM-synkronisering, DirSync eller Azure AD Sync.

### <a name="accounts"></a>Konton
* En Global administratör för Azure AD-konto för Azure AD-klient som du vill integrera med. Det här kontot måste vara en **school-eller organisationskonto** och får inte vara en **microsoftkonto**.
* Om du använder standardinställningar eller uppgradera från DirSync, måste du ha ett Enterprise-administratörskonto för din lokala Active Directory.
* [Konton i Active Directory](reference-connect-accounts-permissions.md) om du använder anpassade inställningar installationssökväg eller ett Enterprise-administratörskonto för din lokala Active Directory.

### <a name="connectivity"></a>Anslutning
* Azure AD Connect-servern måste DNS-matchning för både intranät och internet. DNS-server måste kunna matcha namn både till din lokala Active Directory och Azure AD-slutpunkter.
* Om du har brandväggar på intranätet och du måste öppna portarna mellan Azure AD Connect-servrar och domänkontrollanter och sedan se [Azure AD Connect portar](reference-connect-ports.md) för mer information.
* Om proxyservern eller brandväggen begränsa vilka webbadresser som kan nås så URL: erna som beskrivs i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) måste öppnas.
  * Om du använder Microsoft Cloud i Tyskland eller Microsoft Azure Government-molnet, läser [Azure AD Connect-synkroniseringstjänsten instanser överväganden](reference-connect-instances.md) för URL: er.
* Azure AD Connect (version 1.1.614.0 och efter) använder TLS 1.2 som standard för att kryptera kommunikationen mellan Synkroniseringsmotorn och Azure AD. Om TLS 1.2 är inte tillgängligt i det underliggande operativsystemet, använder Azure AD Connect stegvis till äldre protokoll (TLS 1.1 och TLS 1.0).
* Före version 1.1.614.0 använder TLS 1.0 i Azure AD Connect som standard för att kryptera kommunikationen mellan Synkroniseringsmotorn och Azure AD. Om du vill ändra till TLS 1.2, följer du stegen i [Aktivera TLS 1.2 för Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Om du använder en utgående proxy för att ansluta till Internet, med följande inställning i den **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** fil måste läggas till för installationsguiden och Azure AD Connect-synkronisering för att kunna ansluta till Internet och Azure AD. Den här texten måste anges längst ned i filen. I den här koden &lt;PROXYADRESS&gt; representerar faktiska proxy IP-adressen eller värdnamnet namnet.

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

* Om proxyservern kräver autentisering, kommer [tjänstkontot](reference-connect-accounts-permissions.md#adsync-service-account) måste finnas i domänen och du måste använda installationssökvägen anpassade inställningar för att ange en [anpassade tjänstkonto](how-to-connect-install-custom.md#install-required-components). Du behöver också en annan ändring i machine.config. Med den här ändringen i machine.config: installationen guiden och synkronisera motor svara på begäranden om autentisering från proxyservern. I alla guidesidor för installation, förutom den **konfigurera** sidan den signerade i användarens autentiseringsuppgifter som ska användas. På den **konfigurera** sidan i slutet av installationsguiden kontexten växlas till den [tjänstkontot](reference-connect-accounts-permissions.md#adsync-service-account) som har skapats av dig. Avsnittet machine.config bör se ut så här.

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

* När Azure AD Connect skickar en webbegäran till Azure AD som en del av katalogsynkronisering, kan Azure AD ta upp till 5 minuter att svara. Det är vanligt att proxyservrar ska ha Anslutningskonfiguration för timeout för inaktivitet. Kontrollera att konfigurationen är inställd på minst 6 minuter eller mer.

Mer information finns i MSDN den [standard proxy elementet](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Läs mer om när du har problem med anslutningen [Felsöka anslutningsproblem](tshoot-connect-connectivity.md).

### <a name="other"></a>Annat
* Valfritt: Ett test-användarkonto att verifiera synkronisering.

## <a name="component-prerequisites"></a>Komponent-krav
### <a name="powershell-and-net-framework"></a>PowerShell och .net Framework
Azure AD Connect är beroende av Microsoft PowerShell och .NET Framework 4.5.1. Du behöver den här versionen eller senare installerat på servern. Beroende på din Windows Server-version kan du göra följande:

* Windows Server 2012R2
  * Microsoft PowerShell installeras som standard. Ingen åtgärd krävs.
  * .NET framework 4.5.1 eller senare versioner erbjuds via Windows Update. Kontrollera att du har installerat de senaste uppdateringarna till Windows Server i Kontrollpanelen.
* Windows Server 2008 R2 och Windows Server 2012
  * Den senaste versionen av Microsoft PowerShell finns i **Windows Management Framework 4.0**är tillgängligt på [Microsoft Download Center](https://www.microsoft.com/downloads).
  * .NET framework 4.5.1 och senare versioner är tillgängliga på [Microsoft Download Center](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivera TLS 1.2 för Azure AD Connect
Före version 1.1.614.0 använder TLS 1.0 i Azure AD Connect som standard för att kryptera kommunikationen mellan synkronisering engine-server och Azure AD. Du kan ändra detta genom att konfigurera .net-program att använda TLS 1.2 som standard på servern. Mer information om TLS 1.2 finns i [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 kan inte aktiveras före Windows Server 2008 R2 eller senare. Se till att du har .net 4.5.1 snabbkorrigeringen installeras för ditt operativsystem, se [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Du kan ha den här snabbkorrigeringen eller en senare version redan installerat på servern.
2. Om du använder Windows Server 2008 R2 kan du sedan kontrollera att TLS 1.2 är aktiverad. TLS 1.2 måste redan vara aktiverat på servern för Windows Server 2012 och senare versioner.
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. Ange registernyckeln för alla operativsystem och starta om servern.
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. Om du vill aktivera TLS 1.2 mellan synkronisering engine-server och en fjärransluten SQL Server och sedan kontrollera att du har de nödvändiga versionerna som installerats för [TLS 1.2-stöd för Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Krav för federation installation och konfiguration
### <a name="windows-remote-management"></a>Windows Remote Management
När du använder Azure AD Connect för att distribuera Active Directory Federation Services eller Web Application Proxy kan du kontrollera dessa krav:

* Om målservern är domänansluten ser du till att Windows Remote hanterade är aktiverat
  * I en upphöjd PSH kommandofönstret använder du kommandot `Enable-PSRemoting –force`
* Om målservern är en icke-domänanslutna WAP-datorn och sedan det finns ett antal ytterligare krav
  * På måldatorn (WAP-dator):
    * Kontrollera att winrm (Windows Remote Management / WS-Management) tjänsten körs via snapin-modulen tjänster
    * I en upphöjd PSH kommandofönstret använder du kommandot `Enable-PSRemoting –force`
  * På den dator där guiden körs (om måldatorn är inte tillhör en domän domänansluten eller ej betrodd domän):
    * Använd kommandot i en upphöjd PSH kommandofönster `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * I Serverhanteraren:
      * lägger till DMZ WAP-värd i datorpoolen (Serverhanteraren -> Hantera -> Lägg till servrar... DNS-fliken)
      * Fliken Serverhanteraren alla servrar: Högerklicka på WAP-servern och välj Hantera som..., ange inloggningsuppgifter för lokal (inte domän) för WAP-datorn
      * Validera fjärranslutningar för PSH, på fliken Serverhanteraren alla servrar: Högerklicka på WAP-servern och välj Windows PowerShell. En fjärrsession PSH ska öppna för att säkerställa fjärrsessioner i PowerShell kan upprättas.

### <a name="ssl-certificate-requirements"></a>SSL-certifikatskrav
* Vi rekommenderar starkt att du använder samma SSL-certifikat på alla noder i AD FS-gruppen och alla Web Application proxy-servrar.
* Certifikatet måste vara en X509 certifikat.
* Du kan använda ett självsignerat certifikat på federationsservrar i en testmiljö. För en produktionsmiljö rekommenderar vi dock att du skaffar certifikatet från en offentlig Certifikatutfärdare.
  * Om du använder ett certifikat som inte är offentligt betrott, kontrollera att certifikatet installeras på varje Web Application Proxy-server är betrott på både den lokala servern och på alla federationsservrar
* Identiteten för certifikatet måste matcha federationstjänstens namn (t.ex, sts.contoso.com).
  * Identiteten är antingen ett ämne alternativt namn (SAN)-tillägg av typen dNSName eller om det finns inga poster i SAN, namn på certifikatmottagare anges som eget namn.  
  * Flera SAN-poster kan finnas i certifikatet, förutsatt att en av dem matchar federationstjänstens namn.
  * Om du tänker använda Anslut till arbetsplats, krävs en ytterligare SAN med värdet **enterpriseregistration.** följt av UPN User Principal Name ()-suffix i din organisation, till exempel **enterpriseregistration.contoso.com**.
* Certifikat som baseras på CryptoAPI nästa generations (CNG)-nycklar och nyckellagrings-providers stöds inte. Det innebär att du måste använda ett certifikat baserat på en CSP (cryptographic service provider) och inte en KSP (key storage provider).
* Jokertecken certifikat stöds.

### <a name="name-resolution-for-federation-servers"></a>Namnmatchning för federationsservrar
* Konfigurera DNS-poster för AD FS federationstjänstens namn (t.ex. sts.contoso.com) för både intranät (din interna DNS-server) och extranät (offentlig DNS via din domänregistrator). Om intranätets DNS-post, kontrollera att du använder A-poster och inte CNAME-poster. Detta krävs för windows-autentisering ska fungera korrekt från din domänansluten dator.
* Om du distribuerar flera AD FS-servern eller Web Application Proxy server kontrollerar du att du har konfigurerat belastningsutjämnaren och att DNS-posterna för AD FS-federationstjänstens namn (t.ex. sts.contoso.com) pekar på belastningsutjämnaren.
* Se till att AD FS-federationstjänstens namn (t.ex. sts.contoso.com) har lagts till i intranätzonen i Internet Explorer för windows-integrerad autentisering ska fungera för webbläsarbaserade program med hjälp av Internet Explorer i intranätet. Detta kan styras via grupprinciper och distribueras till alla domänanslutna datorer.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect stöder komponenter
Här följer en lista över komponenter som Azure AD Connect installerar på den server där Azure AD Connect är installerad. Den här listan är för en grundläggande Expressinstallation. Om du vill använda en annan SQL Server på sidan Installera synkronisering har sedan SQL Express LocalDB inte installerats lokalt.

* Azure AD Connect Health
* Microsoft SQL Server 2012-kommandoradsverktyg
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Vidaredistribution paket

## <a name="hardware-requirements-for-azure-ad-connect"></a>Maskinvarukrav för Azure AD Connect
Tabellen nedan visar de lägsta kraven för Azure AD Connect sync-datorn.

| Antalet objekt i Active Directory | Processor | Minne | Hårddiskstorlek |
| --- | --- | --- | --- |
| Färre än 10 000 |1,6 GHz |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| För 100 000 eller fler objekt krävs den fullständiga versionen av SQL Server | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Mer än 600 000 |1,6 GHz |32 GB |500 GB |

De lägsta kraven för datorer som kör AD FS eller Webbprogramservrarna är följande:

* PROCESSOR: Dubbla core 1,6 GHz eller högre
* MINNE: 2 GB eller högre
* Azure VM: A2 configuration eller senare

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
