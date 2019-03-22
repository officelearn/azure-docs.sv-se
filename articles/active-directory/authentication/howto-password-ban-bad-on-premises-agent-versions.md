---
title: Den lokala Azure AD-lösenordsskydd agenten versionshistorik – Azure Active Directory
description: Dokument-versionen och beteende ändringshistorik
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd1a96393f3dd9481705e3de8cc42375de7869e7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311572"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Versionshistorik för Azure AD-lösenordsskydd agent

## <a name="121160"></a>1.2.116.0

Utgivningsdatum: 3/13/2019

* Cmdlet Get-AzureADPasswordProtectionProxy Get-AzureADPasswordProtectionDCAgent: och nu version av rapporten och den aktuella Azure-klienten med följande begränsningar:
  * Programvaruversion och Azure klientdata är endast tillgänglig för DC-agenter och proxyservrar som kör version 1.2.116.0 eller senare.
  * Azure-klientdata kanske inte rapporteras förrän en omregistrering (eller förnyelse) av proxyn eller skog har uppstått.
* Proxy-tjänsten kräver nu att .NET 4.7 är installerad.
  * .NET 4.7 bör vara installerad på en helt uppdaterade Windows-Server. Om detta inte är fallet, hämta och kör installationsprogrammet som finns på [The .NET Framework 4.7 offline installationsprogrammet för Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * På Server Core-system kan det vara nödvändigt att skicka flaggan /q till .NET 4.7 installationsprogrammet för att den ska lyckas.
* Proxy-tjänst har nu stöd för automatisk uppgradering. Automatisk uppgradering använder Microsoft Azure AD Connect-agenten uppdateringstjänsten som är installerade sida-vid-sida med Proxy-tjänsten. Automatisk uppgradering är aktiverat som standard.
* Automatisk uppgradering kan aktiveras eller inaktiveras med hjälp av cmdleten Set-AzureADPasswordProtectionProxyConfiguration. Den aktuella inställningen kan efterfrågas med hjälp av cmdleten Get-AzureADPasswordProtectionProxyConfiguration.
* Binärfilerna för DC-Agenttjänsten har bytt namn till AzureADPasswordProtectionDCAgent.exe.
* Binärfilerna för Proxy-tjänsten har bytt namn till AzureADPasswordProtectionProxy.exe. Brandväggsregler kan behöva anpassas om en brandvägg från tredje part används.
  * Obs: om en HTTP-proxy-konfigurationsfilen användes i tidigare Proxy installerar, måste den ändras (från *proxyservice.exe.config* till *AzureADPasswordProtectionProxy.exe.config*) efter denna uppgradera.
* Alla kontroller med begränsade funktioner har tagits bort från DC-agenten.
* Mindre felkorrigeringar och förbättringar av loggning.

## <a name="12650"></a>1.2.65.0

Utgivningsdatum: 2/1/2019

Ändringar:

* DC-agenten och proxy-tjänsten stöds nu på Server Core. Mininimum OS kraven är desamma som för innan du: Windows Server 2012 för DC-agenter och Windows Server 2012 R2 för proxyservrar.
* Cmdlet: registrera AzureADPasswordProtectionProxy och registrera AzureADPasswordProtectionForest har nu stöd för enhet-kod-baserade Azure autentiseringslägen.
* Cmdleten Get-AzureADPasswordProtectionDCAgent ignorerar felaktig och/eller ogiltig tjänstanslutningspunkter. Det löser buggen där domänkontrollanter skulle ibland visas flera gånger i utdata.
* Cmdleten Get-AzureADPasswordProtectionSummaryReport ignorerar felaktig och/eller ogiltig tjänstanslutningspunkter. Det löser buggen där domänkontrollanter skulle ibland visas flera gånger i utdata.
* Powershell-modulen Proxy är nu registrerad från % ProgramFiles%\WindowsPowerShell\Modules. Miljövariabeln för den datorn PSModulePath ändras inte längre.
* En ny cmdlet Get-AzureADPasswordProtectionProxy har lagts till som gör det enklare att identifiera registrerade proxyservrar i en skog eller domän.
* DC-agenten använder en ny mapp i sysvol-resursen för att replikera lösenordsprinciper och andra filer.

   Gamla mapp:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ny plats:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Den här ändringen gjordes att undvika falska positiva ”ägarlösa Grupprincipobjektet” varningar.)

   > [!NOTE]
   > Ingen migrering eller delning av data ska ske mellan mappen gamla och den nya mappen. Äldre DC-agentversionerna fortsätter att använda den gamla platsen tills uppgraderats till den här versionen eller senare. När alla DC-agenter använder version 1.2.65.0 eller senare, gamla sysvol-mappen raderas manuellt.

* DC-agenten och proxy tjänsten nu identifiera och ta bort felaktig kopior av deras respektive tjänstanslutningspunkter.
* Varje DC-agent tar regelbundet bort felaktig och inaktuella tjänstanslutningspunkter i dess domän, för både DC-agenten och proxy tjänstanslutningspunkter. Båda DC-agenten och proxy tjänstanslutningspunkter betraktas som inaktuella om dess pulsslag tidsstämpel är äldre än sju dagar.
* DC-agenten kommer nu att förnya certifikat över flera skogar efter behov.
* Proxy-tjänsten kommer nu att förnya proxy certifikatet efter behov.
* Uppdateringar av lösenord verifieringsalgoritm: lista med globala förbjudna lösenord och kundspecifika förbjudna lösenordslistan (om konfigurerad) kombineras före lösenord verifieringar. En lösenordet kan nu avvisas (misslyckas eller endast granskning) om den innehåller token från båda globala och kundspecifika listan. Händelseloggen dokumentationen har uppdaterats för att återspegla detta. Se [övervaka Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-monitor.md).
* Prestanda och stabilitet korrigeringar
* Förbättrad loggning

> [!WARNING]
> Begränsade funktioner: DC agent-tjänsten i den här versionen (1.2.65.0) stoppas bearbetar begäranden från lösenord verifiering från och med September 1 2019.  DC-agenttjänsten i tidigare versioner att (se listan nedan) stoppa bearbetningen från och med juli 1 2019. DC-agenttjänsten i alla versioner loggas 10021 händelser i Admin-händelseloggen på två månader fram dessa tidsgränser. Alla tid begränsningar bort i kommande GA-versionen. Proxy agent-tjänsten är inte begränsade i alla versioner, men fortfarande bör uppgraderas till den senaste versionen för att kunna utnyttja alla efterföljande felkorrigeringar och andra förbättringar.

## <a name="12250"></a>1.2.25.0

Utgivningsdatum: 11/01/2018

Korrigeringar:

* DC-agenten och proxy-tjänsten ska inte längre misslyckas på grund av certifikat förtroende fel.
* DC-agenten och proxy-tjänsten har ytterligare korrigeringar för FIPS-kompatibla datorer.
* Proxy-tjänsten fungerar nu korrekt TLS 1.2 endast nätverksmiljön.
* Lägre prestanda och stabilitet korrigeringar
* Förbättrad loggning

Ändringar:

* Den lägsta operativsystemnivå för en Proxy-tjänsten är nu Windows Server 2012 R2. Den minsta nödvändiga operativsystemnivå för en DC-agenttjänsten ligger kvar på Windows Server 2012.
* Proxy-tjänsten kräver nu .NET version 4.6.2 eller senare.
* Lösenord verifieringsalgoritm använder en tabell för normalisering av utökade tecken. Detta kan resultera i lösenord avvisas som godkändes i tidigare versioner.

## <a name="12100"></a>1.2.10.0

Utgivningsdatum: 8/17/2018

Korrigeringar:

* Registrera AzureADPasswordProtectionProxy och registrera AzureADPasswordProtectionForest stöder nu multifaktorautentisering
* Registrera AzureADPasswordProtectionProxy kräver en WS2012 eller senare domänkontrollanten i domänen för att undvika krypteringsfel.
* DC-agenttjänsten är mer tillförlitligt om hur du begär en ny lösenordsprincip för från Azure vid start.
* DC-agenttjänsten begär en ny lösenordsprincip för från Azure varje timme om det behövs, men kommer nu att göra det på ett slumpmässigt valda starttiden.
* DC-agenttjänsten kommer inte längre en obestämd fördröjning i ny DC-annons när installeras på en server innan dess befordran som en replik.
* DC-agenttjänsten följer nu Konfigurationsinställningen ”aktivera lösenordsskydd på Windows Server Active Directory”
* Installationsprogram för både DC-agenten och proxy har nu stöd för uppgradering på plats när du uppgraderar till framtida versioner.

> [!WARNING]
> Uppgradering från version 1.1.10.3 stöds inte och leder till installationsfel. Till uppgradera till version 1.2.10 eller senare, du måste först helt avinstallera programvaran för DC-agenten och proxy-tjänsten och sedan installera den nya versionen från grunden. Omregistrering av Azure AD-lösenordsskydd proxytjänsten krävs.  Det krävs inte registrera skogen.

> [!NOTE]
> Uppgraderingar på plats av DC-agentprogramvaran kräver en omstart.

* DC-agenten och proxy-tjänsten nu kan du köra på en server som konfigurerats att bara använda FIPS-kompatibla algoritmer.
* Lägre prestanda och stabilitet korrigeringar
* Förbättrad loggning

## <a name="11103"></a>1.1.10.3

Utgivningsdatum: 6/15/2018

Första offentliga förhandsversionen

## <a name="next-steps"></a>Nästa steg

[Distribuera Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-deploy.md)
