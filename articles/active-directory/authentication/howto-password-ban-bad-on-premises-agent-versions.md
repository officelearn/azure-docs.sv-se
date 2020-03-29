---
title: Releasehistorik för lösenordsskyddsagenter - Azure Active Directory
description: Dokumentversionsversion och beteendeändringshistorik
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71fd33388cb1bdf7c87c44fb3273c6850122a0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847857"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Versionshistorik för Azure AD-lösenordsskyddsagent

## <a name="121250"></a>1.2.125.0

Utgivningsdatum: 2019-03-22

* Åtgärda mindre stavfel i händelseloggmeddelanden
* Uppdatera LICENSAVTALET till slutlig version av allmän tillgänglighet

> [!NOTE]
> Build 1.2.125.0 är den allmänna tillgängligheten bygga. Tack igen till alla har gett feedback på produkten!

## <a name="121160"></a>1.2.116.0

Utgivningsdatum: 2019-03-13

* Cmdlets Get-AzureADPasswordProtectionProxy och Get-AzureADPasswordProtectionDCAgent rapporterar nu programvaruversion och den aktuella Azure-klienten med följande begränsningar:
  * Programvaruversion och Azure-klientdata är endast tillgängliga för DC-agenter och proxyservrar som kör version 1.2.116.0 eller senare.
  * Azure-klientdata kanske inte rapporteras förrän en omregistrering (eller förnyelse) av proxyn eller skogen har inträffat.
* Proxy-tjänsten kräver nu att .NET 4.7 är installerat.
  * .NET 4.7 bör redan installeras på en helt uppdaterad Windows Server. Om så inte är fallet hämtar och kör du installationsprogrammet som finns i [offlineinstallationsprogrammet för Windows .](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)
  * På Server Core-system kan det vara nödvändigt att skicka flaggan /q till installationsprogrammet .NET 4.7 för att få den att lyckas.
* Proxy-tjänsten stöder nu automatisk uppgradering. Automatisk uppgradering använder tjänsten Microsoft Azure AD Connect Agent Updater som installeras sida vid sida med proxytjänsten. Automatisk uppgradering är aktiverat som standard.
* Automatisk uppgradering kan aktiveras eller inaktiveras med cmdleten Set-AzureADPasswordProtectionProxyConfiguration. Den aktuella inställningen kan efterfrågas med cmdleten Get-AzureADPasswordProtectionProxyConfiguration.
* Tjänstbinären för DC-agenttjänsten har bytt namn till AzureADPasswordProtectionDCAgent.exe.
* Tjänsten binary för proxytjänsten har bytt namn till AzureADPasswordProtectionProxy.exe. Brandväggsregler kan behöva ändras i enlighet med detta om en brandvägg från tredje part används.
  * OBS: Om en http-proxy config-fil användes i en tidigare proxyinstallation måste den byta namn (från *proxyservice.exe.config* till *AzureADPasswordProtectionProxy.exe.config*) efter den här uppgraderingen.
* Alla tidsbegränsade funktionskontroller har tagits bort från DC-agenten.
* Mindre buggar fixar och loggning förbättringar.

## <a name="12650"></a>1.2.65.0

Utgivningsdatum: 2019-02-01

Ändringar:

* DC-agent och proxytjänst stöds nu på Server Core. Kraven för Mininimum OS är oförändrade från tidigare: Windows Server 2012 för DC-agenter och Windows Server 2012 R2 för proxyservrar.
* Register-AzureADPasswordProtectionProxy och Register-AzureADPasswordProtectionForest cmdlets stöder nu enhetskodbaserade Azure-autentiseringslägen.
* Cmdlet get-AzureADPasswordProtectionDCAgent ignorerar manglad och/eller ogiltiga tjänstanslutningspunkter. Detta åtgärdar felet där domänkontrollanter ibland skulle dyka upp flera gånger i utdata.
* Cmdlet get-AzurePASSwordProtectionSummaryReport ignorerar manglad och/eller ogiltiga tjänstanslutningspunkter. Detta åtgärdar felet där domänkontrollanter ibland skulle dyka upp flera gånger i utdata.
* Proxy powershell-modulen har nu registrerats från %ProgramFiles%\WindowsPowerShell\Modules. Datorns PSModulePath-miljövariabel ändras inte längre.
* En ny Get-AzureADPasswordProtectionProxy cmdlet har lagts till för att identifiera registrerade proxyservrar i en skog eller domän.
* DC-agenten använder en ny mapp i sysvol-resursen för att replikera lösenordsprinciper och andra filer.

   Gammal mappplats:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ny mappplats:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Den här ändringen gjordes för att undvika falska positiva varningar om "överblivna dokument från allmänna preferensobjekt".)

   > [!NOTE]
   > Ingen migrering eller delning av data kommer att göras mellan den gamla mappen och den nya mappen. Äldre DC-agentversioner fortsätter att använda den gamla platsen tills den uppgraderas till den här versionen eller senare. När alla DC-agenter körs version 1.2.65.0 eller senare kan den gamla sysvol-mappen tas bort manuellt.

* DC-agenten och proxytjänsten identifierar och tar nu bort mangled kopior av sina respektive tjänstanslutningspunkter.
* Varje DC-agent tar regelbundet bort manglad och inaktuella tjänstanslutningspunkter i domänen, för både DC-agent och proxytjänstanslutningspunkter. Både DC-agent och proxy tjänstanslutningspunkter anses inaktuella om dess pulsslag tidsstämpel är äldre än sju dagar.
* DC-agenten förnyar nu skogscertifikatet efter behov.
* Proxy-tjänsten förnyar nu proxycertifikatet efter behov.
* Uppdateringar av lösenordsvalideringsalgoritmen: den globala listan över förbjudna lösenord och kundspecifik lista över förbjudna lösenord (om den är konfigurerad) kombineras före lösenordsvalideringar. Ett visst lösenord kan nu avvisas (endast underkänt eller granskning) om det innehåller token från både den globala och kundspecifika listan. Dokumentationen för händelseloggen har uppdaterats för att återspegla detta. se [Övervaka Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-monitor.md).
* Prestanda och robusthet fixar
* Förbättrad loggning

> [!WARNING]
> Tidsbegränsad funktionalitet: DC-agenttjänsten i den här versionen (1.2.65.0) slutar behandla begäranden om lösenordsverifiering från och med den 1 september 2019.  DC-agenttjänster i tidigare versioner (se listan nedan) kommer att sluta bearbeta från och med 1 juli 2019. Dc-agenttjänsten i alla versioner loggar 10021 händelser till administratörshändelseloggen under de två månader som föred kommer. Alla tidsbegränsningar kommer att tas bort i den kommande GA-versionen. Proxy agent-tjänsten är inte tidsbegränsad i någon version men bör fortfarande uppgraderas till den senaste versionen för att dra nytta av alla efterföljande buggfixar och andra förbättringar.

## <a name="12250"></a>1.2.25.0

Utgivningsdatum: 2018-01-11

Fixar:

* DC-agent och proxytjänst bör inte längre misslyckas på grund av certifikatförtroendefel.
* DC-agent och proxytjänst har ytterligare korrigeringar för FIPS-kompatibla datorer.
* Proxytjänsten fungerar nu korrekt i en TLS 1.2-nätverksmiljö.
* Mindre prestanda och robusthet fixar
* Förbättrad loggning

Ändringar:

* Den lägsta os-nivån för proxytjänsten är nu Windows Server 2012 R2. Den lägsta erforderliga OS-nivån för DC-agenttjänsten finns kvar på Windows Server 2012.
* Proxy-tjänsten kräver nu .NET version 4.6.2.
* Lösenordsvalideringsalgoritmen använder en utökad teckennormaliseringstabell. Detta kan leda till att lösenord avvisas som har accepterats i tidigare versioner.

## <a name="12100"></a>1.2.10.0

Utgivningsdatum: 2018-08-17

Fixar:

* Registrera-AzureADPasswordProtectionProxy och Register-AzureADPasswordProtectionForest stöder nu multifaktorautentisering
* Register-AzureADPasswordProtectionProxy kräver en WS2012 eller senare domänkontrollant i domänen för att undvika krypteringsfel.
* DC-agenttjänsten är mer tillförlitlig när du begär en ny lösenordsprincip från Azure vid start.
* DC-agenttjänsten kommer att begära en ny lösenordsprincip från Azure varje timme om det behövs, men kommer nu att göra det på en slumpmässigt vald starttid.
* DC-agenttjänsten kommer inte längre att orsaka en obegränsad fördröjning i ny DC-annons när den installeras på en server innan den befordras som en replik.
* DC-agenttjänsten respekterar nu konfigurationsinställningen "Aktivera lösenordsskydd på Windows Server Active Directory"
* Både DC-agent och proxyinstallationsprogram stöder nu uppgradering på plats vid uppgradering till framtida versioner.

> [!WARNING]
> Uppgradering på plats från version 1.1.10.3 stöds inte och resulterar i ett installationsfel. Om du vill uppgradera till version 1.2.10 eller senare måste du först avinstallera DC-agenten och proxytjänsten och sedan installera den nya versionen från grunden. Omregistrering av Azure AD lösenordsskydd Proxy-tjänsten krävs.  Det krävs inte att omregistrera skogen.

> [!NOTE]
> Uppgraderingar på plats av DC-agentprogramvaran kräver en omstart.

* DC-agent och proxytjänst stöder nu körning på en server som konfigurerats för att endast använda FIPS-kompatibla algoritmer.
* Mindre prestanda och robusthet fixar
* Förbättrad loggning

## <a name="11103"></a>1.1.10.3

Utgivningsdatum: 2018-06-15

Första offentliga förhandsversionen

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
