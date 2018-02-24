---
title: 'Azure AD Connect: Krav och maskinvara | Microsoft Docs'
description: "Det här avsnittet beskrivs kraven och maskinvarukraven för Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: d82a91aa51b6684e6bf88de142d00705a0ceddba
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="prerequisites-for-azure-ad-connect"></a>Krav för Azure AD Connect
Det här avsnittet beskrivs kraven och maskinvarukraven för Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Innan du installerar Azure AD Connect
Innan du installerar Azure AD Connect, finns det några saker som du behöver.

### <a name="azure-ad"></a>Azure AD
* En Azure-prenumeration eller en [Azure-provprenumeration](https://azure.microsoft.com/pricing/free-trial/). Den här prenumerationen har bara krävs för åtkomst till Azure portal och inte med Azure AD Connect. Om du använder PowerShell eller Office 365, behöver du inte ha en Azure-prenumeration att använda Azure AD Connect. Om du har en licens för Office 365 kan du också använda Office 365-portalen. Med en betald Office 365-licens kan du också få till Azure portal från Office 365-portalen.
  * Du kan också använda den [Azure-portalen](https://portal.azure.com). Den här portalen kräver inte en Azure AD-licens.
* [Lägg till och verifiera domänen](../active-directory-domains-add-azure-portal.md) du planerar att använda i Azure AD. Om du planerar att använda contoso.com för din användare och sedan kontrollera att den här domänen har verifierats och du bara använder inte contoso.onmicrosoft.com standarddomän.
* En Azure AD-klient kan av standardobjekt 50 kB. När du verifierar din domän ökas gränsen på 300 k-objekt. Om du behöver ännu mer objekt i Azure AD, måste du öppna ett supportärende så att den har ökat ytterligare gränsen. Om du behöver mer än 500 kB objekt behöver du en licens, till exempel Office 365, Azure AD Basic, Azure AD Premium eller Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Förbereda dina lokala data
* Använd [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) identifiera fel, till exempel dubbletter och formatering problem i din katalog innan du synkroniserar till Azure AD och Office 365.
* Granska [valfria sync-funktioner som du kan aktivera i Azure AD](active-directory-aadconnectsyncservice-features.md) och utvärdera vilka funktioner som du bör aktivera.

### <a name="on-premises-active-directory"></a>Lokalt Active Directory
* AD-schemat version och skogens funktionsnivå måste vara Windows Server 2003 eller senare. Domänkontrollanter kan köra någon version förutsatt att schema- och nivå kraven är uppfyllda.
* Om du planerar att använda funktionen **tillbakaskrivning av lösenord**, domänkontrollanter måste vara på Windows Server 2008 (med senaste SP) eller senare. Om din domänkontrollanter är 2008 (pre-R2) och du måste också använda [snabbkorrigering KB2386717](http://support.microsoft.com/kb/2386717).
* Domänkontrollanten som används av Azure AD måste vara skrivbar. Det är **stöds inte** för att använda en RODC (skrivskyddad domänkontrollant) och Azure AD Connect inte följer någon skrivåtgärder omdirigeringar.
* Det är **stöds inte** att använda lokala skogar och domäner med SLDs (enkel etikett domäner).
* Det är **stöds inte** att använda lokala skogar och domäner med ”prickad” (med en punkt i namnet ””.) NetBIOS-namn.
* Det rekommenderas att [aktivera Active Directory-Papperskorgen](active-directory-aadconnectsync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect server
* Azure AD Connect kan inte installeras på Small Business Server eller Windows Server Essentials. Servern måste använda Windows Server standard eller bättre.
* Azure AD Connect-servern måste ha ett fullständigt grafiskt användargränssnitt installerat. Det är **stöds inte** att installera på server core.
* Azure AD Connect måste installeras på Windows Server 2008 eller senare. Den här servern kan vara en domänkontrollant eller en medlemsserver när du använder standardinställningar. Om du använder anpassade inställningar, servern kan också vara fristående och behöver inte vara ansluten till en domän.
* Om du installerar Azure AD Connect på Windows Server 2008 eller Windows Server 2008 R2, kontrollera att tillämpa de senaste snabbkorrigeringarna från Windows Update. Installationen kan inte börja med en okorrigerade server.
* Om du planerar att använda funktionen **Lösenordssynkronisering**, Azure AD Connect-servern måste vara Windows Server 2008 R2 SP1 eller senare.
* Om du planerar att använda en **grupp Hanterat tjänstkonto**, Azure AD Connect-servern måste vara Windows Server 2012 eller senare.
* Azure AD Connect-servern måste ha [.NET Framework 4.5.1](#component-prerequisites) eller senare och [Microsoft PowerShell 3.0](#component-prerequisites) eller senare installerat.
* Azure AD Connect-servern får inte ha PowerShell skrivfel Grupprincip har aktiverats.
* Om Active Directory Federation Services distribueras de servrar där AD FS eller Web Application Proxy är installerade måste vara Windows Server 2012 R2 eller senare. [Windows fjärrhantering](#windows-remote-management) måste vara aktiverat på dessa servrar för fjärrinstallation.
* Om Active Directory Federation Services distribueras måste [SSL-certifikat](#ssl-certificate-requirements).
* Om Active Directory Federation Services distribueras, så måste du konfigurera [namnmatchning](#name-resolution-for-federation-servers).
* Om dina globala administratörer har MFA aktiverat får URL: en **https://secure.aadcdn.microsoftonline-p.com** måste finnas i listan med betrodda platser. Du uppmanas att lägga till den här platsen i listan med betrodda platser när du tillfrågas om MFA-kontrollen och har inte lagt till innan. Du kan använda Internet Explorer för att lägga till dina betrodda webbplatser.

### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server som används av Azure AD Connect
* Azure AD Connect kräver en SQL Server-databas för att lagra identitetsdata. En SQL Server 2012 Express LocalDB (enkel version av SQL Server Express) installeras som standard. SQL Server Express har en 10GB storleksgräns som gör att du kan hantera cirka 100 000 objekt. Om du behöver hantera ett ökat antal katalogobjekt måste du peka installationsguiden till en annan installation av SQL Server.
* Om du använder en separat SQL Server, gäller dessa krav:
  * Azure AD Connect stöder alla versioner av Microsoft SQL Server från SQL Server 2008 (med senaste Service Pack) till SQL Server 2016 SP1. Microsoft Azure SQL Database är **stöds inte** som en-databas.
  * Du måste använda en icke skiftlägeskänslig sortering i SQL. Dessa sorteringar identifieras med en \_CI_ i sina namn. Det är **stöds inte** att använda en skiftlägeskänslig sortering som identifieras av \_CS_ i sina namn.
  * Du kan bara ha en Synkroniseringsmotorn per SQL-instans. Det är **stöds inte** att dela en SQL-instans med FIM/MIM-synkronisering, DirSync eller Azure AD Sync.

### <a name="accounts"></a>Konton
* En Global administratör för Azure AD-konto för Azure AD-klient som du vill integrera med. Det här kontot måste vara en **Skol- eller organisation konto** och kan inte vara en **Microsoft-konto**.
* Om du använder standardinställningar eller uppgradera från DirSync måste du ha ett Enterprise-administratörskonto för din lokala Active Directory.
* [Konton i Active Directory](active-directory-aadconnect-accounts-permissions.md) om du använder anpassade inställningar installationssökvägen.

### <a name="connectivity"></a>Anslutning
* Azure AD Connect-servern behöver DNS-matchning för både intranät och internet. DNS-servern måste kunna matcha namn både till din lokala Active Directory och Azure AD-slutpunkter.
* Om du har brandväggar på intranätet och du måste öppna portar mellan Azure AD Connect-servrarna och domänkontrollanterna och sedan se [Azure AD Connect-portar](active-directory-aadconnect-ports.md) för mer information.
* Om proxyservern eller brandväggen begränsa vilka webbadresser som kan nås och URL: erna som beskrivs i [Office 365-URL: er och IP-adressintervall](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) måste öppnas.
  * Om du använder Microsoft-Cloud i Tyskland eller Microsoft Azure Government-molnet, se [Azure AD Connect-synkroniseringstjänsten instanser överväganden](active-directory-aadconnect-instances.md) för URL: er.
* Azure AD Connect (version 1.1.614.0 och efter) används TLS 1.2 som standard för att kryptera kommunikationen mellan Synkroniseringsmotorn och Azure AD. Om TLS 1.2 är inte tillgänglig för det underliggande operativsystemet, använder Azure AD Connect inkrementellt till äldre protokoll (TLS 1.1 och TLS 1.0). Till exempel använder TLS 1.0 i Azure AD Connect körs på Windows Server 2008 eftersom Windows Server 2008 inte stöder TLS 1.1 och TLS 1.2.
* Före version 1.1.614.0 använder TLS 1.0 i Azure AD Connect som standard för att kryptera kommunikationen mellan Synkroniseringsmotorn och Azure AD. Om du vill ändra TLS 1.2, följer du stegen i [Aktivera TLS 1.2 för Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Om du använder en utgående proxy för att ansluta till Internet, följande inställning i den **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** filen måste läggas till för installationsguiden och Azure AD Connect-synkronisering för att kunna ansluta till Internet och Azure AD. Den här texten måste anges längst ned i filen. I den här koden &lt;PROXYADRESS&gt; representerar faktiska proxyserverns IP-adressen eller värdnamnet namn.

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

* Om proxyservern kräver autentisering, sedan [tjänstkonto](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) måste finnas i domänen och du måste använda installationssökvägen anpassade inställningar för att ange en [anpassade tjänstkonto](active-directory-aadconnect-get-started-custom.md#install-required-components). Du måste också en annan ändring i machine.config. Med den här ändringen i machine.config installationen guiden och sync motorn svara på begäranden om autentisering från proxyservern. I alla guidesidor för installation, förutom de **konfigurera** sidan den signerade i användarens autentiseringsuppgifter används. På den **konfigurera** sida i slutet av installationsguiden kontexten växlas till den [tjänstkonto](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) som har skapats av dig. Avsnittet machine.config bör se ut så här.

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

* När Azure AD Connect skickar en webbegäran till Azure AD som en del av katalogsynkronisering, kan Azure AD ta upp till 5 minuter att svara. Det är vanligt för proxyservrar ha Anslutningskonfiguration för timeout för inaktivitet. Kontrollera att konfigurationen är inställd på minst 6 minuter eller mer.

Mer information finns i MSDN den [standard proxy elementet](https://msdn.microsoft.com/library/kd3cf2ex.aspx).  
Mer information om du har problem med anslutningen finns [Felsöka anslutningsproblem](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Annat
* Valfritt: Ett test användarkonto att verifiera synkronisering.

## <a name="component-prerequisites"></a>Förutsättningar för komponent
### <a name="powershell-and-net-framework"></a>PowerShell och .net Framework
Azure AD Connect är beroende av Microsoft PowerShell och .NET Framework 4.5.1. Du behöver den här versionen eller en senare version installerad på servern. Beroende på din Windows Server-version, gör du följande:

* Windows Server 2012R2
  * Microsoft PowerShell installeras som standard. Ingen åtgärd krävs.
  * .NET framework 4.5.1 eller senare versioner erbjuds via Windows Update. Kontrollera att du har installerat de senaste uppdateringarna till Windows Server på Kontrollpanelen.
* Windows Server 2008R2 och Windows Server 2012
  * Den senaste versionen av Microsoft PowerShell finns i **Windows Management Framework 4.0**, tillgänglig på [Microsoft Download Center](http://www.microsoft.com/downloads).
  * .NET framework 4.5.1 och senare versioner är tillgängliga på [Microsoft Download Center](http://www.microsoft.com/downloads).
* Windows Server 2008
  * Den senaste versionen av PowerShell finns i **Windows Management Framework 3.0**, tillgänglig på [Microsoft Download Center](http://www.microsoft.com/downloads).
  * .NET framework 4.5.1 och senare versioner är tillgängliga på [Microsoft Download Center](http://www.microsoft.com/downloads).

### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivera TLS 1.2 för Azure AD Connect
Före version 1.1.614.0 använder TLS 1.0 i Azure AD Connect som standard för att kryptera kommunikationen mellan motorn synkroniseringsserver och Azure AD. Du kan ändra detta genom att konfigurera .net-program att använda TLS 1.2 som standard på servern. Mer information om TLS 1.2 kan hittas i [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 kan inte aktiveras på Windows Server 2008. Du behöver Windows Server 2008R2 eller senare. Se till att .net 4.5.1 snabbkorrigeringen installerad för ditt operativsystem kan du se [Microsoft Security Advisory 2960358](https://technet.microsoft.com/security/advisory/2960358). Du kanske snabbkorrigeringen eller en senare version redan installerat på servern.
2. Om du använder Windows Server 2008R2 Kontrollera TLS 1.2 är aktiverad. På Windows Server 2012-server och senare versioner, vara TLS 1.2 redan aktiverat.
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
4. Om du vill aktivera TLS 1.2 mellan motorn synkroniseringsserver och en fjärransluten SQL Server och sedan kontrollera att du har de operativsystemversioner som krävs för [TLS 1.2-stöd för Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Krav för federation installation och konfiguration
### <a name="windows-remote-management"></a>Windows Remote Management
När du använder Azure AD Connect för att distribuera Active Directory Federation Services eller Web Application Proxy, kontrollera dessa krav:

* Om målservern är ansluten till en domän, kontrollera att Windows Remote hanteras är aktiverad
  * I ett upphöjt PSH kommando-fönster kommandot `Enable-PSRemoting –force`
* Om målservern är en icke-domänanslutna WAP-dator och det finns ett antal ytterligare krav
  * På måldatorn (WAP-dator):
    * Kontrollera att winrm (Windows Remote Management / WS-Management) tjänsten körs via snapin-modulen tjänster
    * I ett upphöjt PSH kommando-fönster kommandot `Enable-PSRemoting –force`
  * På den dator där guiden körs (om måldatorn är icke-domän domänansluten eller ej betrodd domän):
    * Använd kommandot i ett upphöjt PSH kommando-fönster `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * I Serverhanteraren:
      * Lägg till en värd i Perimeternätverket WAP datorpool (Serverhanteraren -> Hantera -> Lägg till servrar... Använd fliken DNS)
      * Fliken Serverhanteraren alla servrar: Högerklicka på server för WAP och välj Hantera som..., ange inloggningsuppgifter för lokala (inte domän) för WAP-dator
      * Att verifiera fjärråtkomst PSH anslutningarna i fliken Serverhanteraren alla servrar: server för WAP Högerklicka och välj Windows PowerShell. En fjärrsession PSH ska öppnas för att säkerställa PowerShell fjärrsessioner kan upprättas.

### <a name="ssl-certificate-requirements"></a>Kraven för SSL-certifikat
* Det starkt rekommenderar vi för att använda samma certifikat för SSL över alla noder i AD FS-gruppen och alla proxyservrar för webbprogram.
* Certifikatet måste vara X509 certifikat.
* Du kan använda ett självsignerat certifikat på federationsservrar i en testmiljö. För en produktionsmiljö rekommenderar vi dock att du hämtar certifikatet från en offentlig Certifikatutfärdare.
  * Om du använder ett certifikat som inte är betrodd offentlig, kontrollera att certifikatet installeras på varje server Web Application Proxy är betrott på både den lokala servern och på alla federationsservrar
* Identiteten för certifikatet måste matcha federationstjänstens namn (t.ex, sts.contoso.com).
  * Identiteten är antingen ett ämne alternativt namn (SAN)-tillägg av typen dNSName eller om det finns inget SAN-transaktioner, ämnesnamnet angetts som ett eget namn.  
  * Flera poster för SAN-nätverk kan finnas i certifikatet, under förutsättning att en av dem matchar federationstjänstens namn.
  * Om du planerar att använda Anslut till arbetsplats, krävs en ytterligare SAN med värdet **enterpriseregistration.** följt av suffixet UPN (User Principal Name) för din organisation, till exempel **företagsregistrering.contoso.com**.
* Certifikat som baseras på CryptoAPI nästa generation (CNG) nycklar och nyckellagring stöds inte. Detta innebär att du måste använda ett certifikat som baseras på en CSP (cryptographic service provider) och inte en KSP (key storage provider).
* Jokertecken certifikat som stöds.

### <a name="name-resolution-for-federation-servers"></a>Namnmatchning för federationsservrar
* Konfigurera DNS-poster för AD FS federationstjänstens namn (till exempel sts.contoso.com) för både (interna DNS-servern) för intranät och extranät (offentliga DNS via din domänregistrator). Se till att du använder en för intranät DNS-posten poster och inte CNAME-poster. Detta krävs för windows-autentisering ska fungera korrekt från en domänansluten dator.
* Om du distribuerar flera AD FS-servern eller webbprogramproxyservern kontrollerar du att du har konfigurerat din belastningsutjämnare och att DNS-posterna för AD FS federationstjänstens namn (till exempel sts.contoso.com) pekar på belastningsutjämnaren.
* Se till att AD FS federationstjänstens namn (till exempel sts.contoso.com) har lagts till i intranätzonen i Internet Explorer för windows-integrerad autentisering ska fungera för webbläsarprogram som använder Internet Explorer i intranätet. Detta kan kontrolleras via en Grupprincip och distribueras till alla domänanslutna datorer.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect stöder komponenter
Följande är en lista över komponenter som Azure AD Connect installeras på den server där Azure AD Connect är installerat. Den här listan är för en grundläggande Expressinstallation. Om du väljer att använda en annan SQL Server på sidan Installera synkronisering är sedan SQL Express LocalDB inte installerad lokalt.

* Azure AD Connect Health
* Microsoft Online Services-inloggningsassistent för IT-proffs (installerad men inga beroende)
* Microsoft SQL Server 2012-kommandoradsverktyg
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistribution Package

## <a name="hardware-requirements-for-azure-ad-connect"></a>Maskinvarukrav för Azure AD Connect
Tabellen nedan visar de lägsta kraven för Azure AD Connect sync-dator.

| Antal objekt i Active Directory | Processor | Minne | Hårddiskstorlek |
| --- | --- | --- | --- |
| Färre än 10 000 |1,6 GHz |4 GB |70 GB |
| 10,000–50,000 |1,6 GHz |4 GB |70 GB |
| 50,000–100,000 |1,6 GHz |16 GB |100 GB |
| För 100 000 eller fler objekt krävs den fullständiga versionen av SQL Server | | | |
| 100,000–300,000 |1,6 GHz |32 GB |300 GB |
| 300,000–600,000 |1,6 GHz |32 GB |450 GB |
| Mer än 600 000 |1,6 GHz |32 GB |500 GB |

De lägsta kraven för datorer som kör AD FS eller webbprogramservrar är följande:

* Processor: Dual core 1,6 GHz eller snabbare
* MINNE: 2 GB eller högre
* Azure VM: A2-konfiguration eller högre

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
