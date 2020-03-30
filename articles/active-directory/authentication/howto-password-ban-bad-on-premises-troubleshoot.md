---
title: Felsöka lokalt azure AD-lösenordsskydd
description: Lär dig hur du felsöker Azure AD-lösenordsskydd för en lokal Active Directory Domain Services-miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263652"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Felsöka: Lokalt Azure AD-lösenordsskydd

Efter distributionen av Azure AD Password Protection kan felsökning krävas. Den här artikeln innehåller detaljerad information som hjälper dig att förstå några vanliga felsökningssteg.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC-agenten kan inte hitta en proxy i katalogen

Det största symptomet på det här problemet är 30017-händelser i dc-agenten Admin händelselogg.

Den vanliga orsaken till problemet är att en proxy ännu inte har registrerats. Om en proxy har registrerats kan det uppstå vissa fördröjningar på grund av AD-replikeringsfördröjning tills en viss DC-agent kan se proxyn.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC-agenten kan inte kommunicera med en proxy

Det största symptomet på det här problemet är 30018 händelser i DC agent Admin händelselogg. Det här problemet kan ha flera möjliga orsaker:

1. DC-agenten finns i en isolerad del av nätverket som inte tillåter nätverksanslutning till de registrerade proxyerna. Det här problemet kan vara godartade så länge andra DC-agenter kan kommunicera med proxyn(er) för att hämta lösenordsprinciper från Azure. När dessa principer har hämtats kommer de att erhållas av den isolerade domänkontrollanten via replikering av principfilerna i sysvol-resursen.

1. Proxyvärdens dator blockerar åtkomsten till slutpunktsmappningsslutpunkten för RPC (port 135)

   Azure AD Password Protection Proxy-installationsprogrammet skapar automatiskt en inbound-regel för Windows-brandväggen som ger åtkomst till port 135. Om den här regeln senare tas bort eller inaktiveras kan DC-agenter inte kommunicera med proxytjänsten. Om den inbyggda Windows-brandväggen har inaktiverats i stället för en annan brandväggsprodukt måste du konfigurera brandväggen så att den tillåter åtkomst till port 135.

1. Proxyvärdens dator blockerar åtkomsten till RPC-slutpunkten (dynamisk eller statisk) som proxytjänsten lyssnar på

   Azure AD Password Protection Proxy-installationsprogrammet skapar automatiskt en inbound-regel för Windows-brandväggen som ger åtkomst till alla inkommande portar som lyssnas på av Azure AD Password Protection Proxy-tjänsten. Om den här regeln senare tas bort eller inaktiveras kan DC-agenter inte kommunicera med proxytjänsten. Om den inbyggda Windows-brandväggen har inaktiverats i stället för en annan brandväggsprodukt måste du konfigurera brandväggen så att den tillåter åtkomst till inkommande portar som lyssnas på av Azure AD Password Protection Proxy-tjänsten. Den här konfigurationen kan göras mer specifik om proxytjänsten har konfigurerats för `Set-AzureADPasswordProtectionProxyConfiguration` att lyssna på en specifik statisk RPC-port (med hjälp av cmdleten).

1. Proxyvärden är inte konfigurerad så att domänkontrollanter kan logga in på datorn. Detta styrs via användarbehörighetstilldelningen "Åtkomst till den här datorn från nätverket". Alla domänkontrollanter i alla domäner i skogen måste beviljas det här privilegiet. Den här inställningen är ofta begränsad som en del av en större nätverkshärdningsinsats.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Proxytjänsten kan inte kommunicera med Azure

1. Kontrollera att proxydatorn har anslutning till slutpunkterna som anges i [distributionskraven](howto-password-ban-bad-on-premises-deploy.md).

1. Kontrollera att skogen och alla proxyservrar är registrerade mot samma Azure-klientorganisation.

   Du kan kontrollera det `Get-AzureADPasswordProtectionProxy` här `Get-AzureADPasswordProtectionDCAgent` kravet genom att köra cmdlets och PowerShell och sedan jämföra egenskapen `AzureTenant` för varje returnerad artikel. För korrekt åtgärd måste det rapporterade klientnamnet vara detsamma för alla DC-agenter och proxyservrar.

   Om det finns ett villkor för registrering av Azure-klientorganisation kan `Register-AzureADPasswordProtectionForest` det här problemet åtgärdas genom att köra cmdlets och/eller `Register-AzureADPasswordProtectionProxy` PowerShell som behövs och se till att använda autentiseringsuppgifter från samma Azure-klient för alla registreringar.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC-agenten kan inte kryptera eller dekryptera lösenordsprincipfiler

Azure AD Password Protection har ett kritiskt beroende av krypterings- och dekrypteringsfunktionen som tillhandahålls av Microsoft Key Distribution Service. Kryptering eller dekrypteringsfel kan manifesteras med en mängd olika symptom och har flera potentiella orsaker.

1. Kontrollera att KDS-tjänsten är aktiverad och funktionell på alla Windows Server 2012- och senare domänkontrollanter i en domän.

   Som standard är KDS-tjänstens tjänststartläge konfigurerat som manuell (triggerstart). Den här konfigurationen innebär att första gången en klient försöker använda tjänsten startas den på begäran. Det här standardstartläget för tjänsten är acceptabelt för Azure AD Password Protection att fungera.

   Om startläget för KDS-tjänsten har konfigurerats till Inaktiverat måste den här konfigurationen åtgärdas innan Azure AD-lösenordsskydd fungerar korrekt.

   Ett enkelt test för det här problemet är att manuellt starta KDS-tjänsten, antingen via MMC-konsolen Service management eller med andra hanteringsverktyg (kör till exempel "net start kdssvc" från en kommandotolkskonsol). KDS-tjänsten förväntas starta och fortsätta att köras.

   Den vanligaste grundorsaken till att KDS-tjänsten inte kan starta är att Active Directory-domänkontrollantobjektet finns utanför standardenheten för domänkontrollanter. Den här konfigurationen stöds inte av KDS-tjänsten och är inte en begränsning som införts av Azure AD Password Protection. Korrigeringen för det här villkoret är att flytta domänkontrollantobjektet till en plats under standardenheten för domänkontrollanter.

1. Inkompatibel ändring av KDS-krypterat buffertformat från Windows Server 2012 R2 till Windows Server 2016

   En KDS-säkerhetskorrigering introducerades i Windows Server 2016 som ändrar formatet på KDS-krypterade buffertar. Dessa buffertar misslyckas ibland med att dekryptera på Windows Server 2012 och Windows Server 2012 R2. Den omvända riktningen är okej - buffertar som är KDS-krypterade på Windows Server 2012 och Windows Server 2012 R2 kommer alltid att dekryptera på Windows Server 2016 och senare. Om domänkontrollanterna i Active Directory-domänerna kör en blandning av dessa operativsystem kan tillfälliga dekrypteringsfel för Azure AD-lösenordsskydd rapporteras. Det är inte möjligt att exakt förutsäga tidpunkten eller symptomen på dessa fel med tanke på säkerhetskorrigeringens natur, och med tanke på att det inte är deterministiskt vilken Azure AD Password Protection DC Agent som domänkontrollanten krypterar data vid en viss tidpunkt.

   Microsoft undersöker en korrigering för det här problemet, men ingen ETA är tillgänglig ännu. Under tiden finns det ingen lösning på problemet annat än att inte köra en blandning av dessa inkompatibla operativsystem i Active Directory-domänen(erna). Med andra ord bör du bara köra Windows Server 2012- och Windows Server 2012 R2-domänkontrollanter, ELLER så ska du bara köra Windows Server 2016 och över domänkontrollanter.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Svaga lösenord accepteras men bör inte

Det här problemet kan ha flera orsaker.

1. Dc-agenterna kör en offentlig version av förhandsversionen av förhandsversionen som har upphört att gälla. Se [Dc-agentprogramvara för offentlig förhandsgranskning har upphört att gälla](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Din DC-agent kan inte hämta en princip eller kan inte dekryptera befintliga principer. Kontrollera om det finns några orsaker i ovanstående avsnitt.

1. Tvingande läge för lösenordsprincipen är fortfarande inställt på Granskning. Om den här konfigurationen är i kraft konfigurerar du om den så att den verkställs med hjälp av portalen för lösenordsskydd i Azure AD. Mer information finns i [Driftsätt](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Lösenordsprincipen har inaktiverats. Om den här konfigurationen är i kraft konfigurerar du om den så att den aktiveras med hjälp av Portalen för lösenordsskydd i Azure AD. Mer information finns i [Driftsätt](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Du har inte installerat DC-agentprogramvaran på alla domänkontrollanter i domänen. I det här fallet är det svårt att se till att fjärrklienter i Windows inriktas på en viss domänkontrollant under en lösenordsändring. Om du tror att du har riktat in dig på en viss domänkontrollant där DC-agentprogramvaran är installerad kan du verifiera genom att dubbelkolla händelseloggen för DC-agentens administratör: oavsett resultatet kommer det att finnas minst en händelse för att dokumentera resultatet av lösenordet Validering. Om det inte finns någon händelse för användaren vars lösenord ändras, bearbetades troligen lösenordsändringen av en annan domänkontrollant.

   Som ett alternativt test kan du prova att ange\ändra lösenord när du är inloggad direkt i en domänkontroll där DC-agentprogrammet är installerat. Den här tekniken rekommenderas inte för produktion av Active Directory-domäner.

   Även om inkrementell distribution av DC-agentprogramvaran stöds med förbehåll för dessa begränsningar, rekommenderar Microsoft starkt att DC-agentprogramvaran installeras på alla domänkontrollanter i en domän så snart som möjligt.

1. Algoritmen för lösenordsvalidering kan faktiskt fungera som förväntat. Se [Hur utvärderas lösenord](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe misslyckas med att ställa in ett svagt DSRM-lösenord

Active Directory validerar alltid ett nytt lösenord för reparationsläge för katalogtjänster för att se till att det uppfyller domänens lösenordskomplexitetskrav. Den här valideringen anropar också lösenordsfilter dlls som Azure AD Password Protection. Om det nya DSRM-lösenordet avvisas visas följande felmeddelande:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

När Azure AD Password Protection loggar händelseloggen för lösenordsverifiering för ett Active Directory DSRM-lösenord förväntas händelseloggmeddelandena inte innehålla ett användarnamn. Detta beror på att DSRM-kontot är ett lokalt konto som inte ingår i den faktiska Active Directory-domänen.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Replikkampanj för domänkontrollant misslyckas på grund av ett svagt DSRM-lösenord

Under dc-kampanjprocessen skickas det nya lösenordet för reparationsläge för katalogtjänster till en befintlig domänkontrollant i domänen för validering. Om det nya DSRM-lösenordet avvisas visas följande felmeddelande:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Precis som i ovanstående problem har alla Azure AD Password Protection lösenordsverifieringsresultathändelse tomma användarnamn för det här scenariot.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Degraderingen av domänkontrollanten misslyckas på grund av ett svagt lokalt administratörslösenord

Det stöds för att nedgradera en domänkontrollant som fortfarande kör DC-agentprogramvaran. Administratörer bör dock vara medvetna om att DC-agentprogramvaran fortsätter att tillämpa den aktuella lösenordsprincipen under degraderingsproceduren. Det nya lösenordet för det lokala administratörskontot (som anges som en del av degraderingsåtgärden) valideras som vilket annat lösenord som helst. Microsoft rekommenderar att säkra lösenord väljs för lokala administratörskonton som en del av en dc-degraderingsprocedur.

När degraderingen har lyckats och domänkontrollanten har startats om och körs igen som en vanlig medlemsserver återgår DC-agentprogramvaran till att köras i ett passivt läge. Det kan sedan avinstalleras när som helst.

## <a name="booting-into-directory-services-repair-mode"></a>Starta i reparationsläge för katalogtjänster

Om domänkontrollanten startas i reparationsläge för katalogtjänster identifierar DLL-lösenordsfiltret dll för DC-agenten det här villkoret och gör att alla lösenordsverifierings- eller tvingande åtgärder inaktiveras, oavsett vilken princip som för närvarande är aktiv. Konfiguration. Dll-filen för DLL-agentens lösenordsfilter loggar en 10023-varningshändelse till händelseloggen Admin, till exempel:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Dc-agentprogram för offentlig förhandsversion har upphört att gälla

Under den offentliga förhandsversionen av Azure AD Password Protection var DC-agentprogramvaran hårdkodad för att stoppa bearbetningen av begäranden om lösenordsverifiering på följande datum:

* Version 1.2.65.0 slutar behandla begäranden om lösenordsverifiering den 1 september 2019.
* Version 1.2.25.0 och tidigare slutade bearbeta begäranden om lösenordsverifiering den 1 juli 2019.

När tidsgränsen närmar sig kommer alla tidsbegränsade DC-agentversioner att avge en 10021-händelse i DC-agenten Admin-händelseloggen vid uppstart som ser ut så här:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

När tidsfristen har passerat, alla tidsbegränsade DC agent versioner kommer att avge en 10022 händelse i DC agent Admin händelselogg vid uppstart som ser ut så här:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Eftersom tidsgränsen endast kontrolleras vid den första uppstarten kanske du inte ser dessa händelser förrän långt efter att kalenderdeadline har passerats. När tidsgränsen har identifierats kommer inga negativa effekter på domänkontrollanten eller den större miljön att inträffa annat än alla lösenord automatiskt.

> [!IMPORTANT]
> Microsoft rekommenderar att utgångna dc-agenter för offentlig förhandsversion omedelbart uppgraderas till den senaste versionen.

Ett enkelt sätt att upptäcka DC-agenter i din miljö `Get-AzureADPasswordProtectionDCAgent` som behöver uppgraderas är genom att köra cmdlet, till exempel:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

För detta ämne är SoftwareVersion fältet uppenbarligen nyckeln egenskap att titta på. Du kan också använda PowerShell-filtrering för att filtrera bort DC-agenter som redan finns på eller över den nödvändiga baslinjeversionen, till exempel:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD Password Protection Proxy-programvaran är inte tidsbegränsad i någon version. Microsoft rekommenderar fortfarande att både DC och proxyagenter uppgraderas till de senaste versionerna när de släpps. Cmdleten `Get-AzureADPasswordProtectionProxy` kan användas för att hitta proxyagenter som kräver uppgraderingar, liknande exemplet ovan för DC-agenter.

Mer information om specifika uppgraderingsprocedurer finns i [Uppgradera DC-agenten](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) och [uppgradera proxytjänsten.](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)

## <a name="emergency-remediation"></a>Nödsanering

Om en situation uppstår där dc-agenttjänsten orsakar problem kan dc-agenttjänsten omedelbart stängas av. Dll-filtret DLL för DC-agenten försöker fortfarande anropa tjänsten som inte körs och loggar varningshändelser (10012, 10013), men alla inkommande lösenord accepteras under den tiden. Dc-agenttjänsten kan sedan också konfigureras via Windows Service Control Manager med en starttyp av "Inaktiverad" efter behov.

En annan åtgärd skulle vara att ange aktivera läge till Nej i Azure AD Password Protection portal. När den uppdaterade principen har hämtats går varje DC-agenttjänst in i ett quiescent-läge där alla lösenord accepteras som de är. Mer information finns i [Driftsätt](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Borttagning

Om det beslutas att avinstallera Azure AD lösenordsskydd programvara och rensa alla relaterade tillstånd från domäner och skog, kan denna uppgift utföras med hjälp av följande steg:

> [!IMPORTANT]
> Det är viktigt att utföra dessa steg i ordning. Om någon instans av proxytjänsten körs kommer den regelbundet att återskapa sitt serviceConnectionPoint-objekt. Om någon instans av dc-agenttjänsten lämnas igång återskapas dess serviceConnectionPoint-objekt och sysvol-tillståndet.

1. Avinstallera proxyprogramvaran från alla maskiner. Det här steget kräver **ingen** omstart.
2. Avinstallera DC Agent-programvaran från alla domänkontrollanter. Det här steget **kräver** en omstart.
3. Ta bort alla anslutningspunkter för proxytjänsten manuellt i varje domännamngivningskontext. Platsen för dessa objekt kan identifieras med följande Active Directory PowerShell-kommando:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Utelämna inte asterisken ("*") i slutet av $keywords variabelvärdet.

   De objekt som blir resultatet `Get-ADObject` som hittas via `Remove-ADObject`kommandot kan sedan ledas till eller tas bort manuellt.

4. Ta bort alla dc-agentanslutningspunkter manuellt i varje domännamnskontext. Det kan finnas ett av dessa objekt per domänkontrollant i skogen, beroende på hur mycket programvaran har distribuerats. Platsen för det objektet kan identifieras med följande Active Directory PowerShell-kommando:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   De objekt som blir resultatet `Get-ADObject` som hittas via `Remove-ADObject`kommandot kan sedan ledas till eller tas bort manuellt.

   Utelämna inte asterisken ("*") i slutet av $keywords variabelvärdet.

5. Ta bort konfigurationstillståndet på skogsnivå manuellt. Skogskonfigurationstillståndet underhålls i en behållare i namnkontexten för Active Directory-konfiguration. Det kan upptäckas och tas bort enligt följande:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Ta bort alla sysvol-relaterade tillstånd manuellt genom att manuellt ta bort följande mapp och allt dess innehåll:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Om det behövs kan den här sökvägen också nås lokalt på en viss domänkontrollant. standardplatsen skulle vara ungefär så här:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Den här sökvägen är annorlunda om sysvol-resursen har konfigurerats på en plats som inte är standard.

## <a name="next-steps"></a>Nästa steg

[Vanliga frågor och svar om lösenordsskydd för Azure AD](howto-password-ban-bad-on-premises-faq.md)

Mer information om de globala och anpassade förbjudna [lösenordslistorna](concept-password-ban-bad.md) finns i artikeln Ban bad passwords
