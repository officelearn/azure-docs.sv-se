---
title: Felsökning och loggning i förhandsversionen av Azure AD lösenord protection
description: Förstå Azure AD-lösenordsskydd Förhandsgranska loggning och felsökning av vanliga problem
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 1e5782ce3421cc5f0d2e0e51484d4bbe6b9eb6ab
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978646"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Förhandsversion: Azure AD lösenord protection övervakning, rapportering och felsökning

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Är viktiga uppgifter efter distribution av Azure AD-lösenordsskydd, övervakning och rapportering. Den här artikeln innehåller information för att du förstår där varje tjänst loggar information och rapportera om användning av Azure AD-lösenordsskydd.

## <a name="on-premises-logs-and-events"></a>Lokala loggar och händelser

### <a name="dc-agent-admin-log"></a>DC-agentloggen admin

På varje domänkontrollant skriver DC service Agentprogrammet som är resultatet av sitt lösenord verifieringar (och andra status) till en lokal händelselogg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

Händelser som loggas av olika DC agent-komponenter med hjälp av följande intervall:

|Komponent |Händelse-ID-intervall|
| --- | --- |
|DC-agenten lösenord DLL-fil| 10000 19999|
|Värdprocessen för DC agent-tjänsten| 20000-29999|
|Validering av logik för DC agent-tjänsten princip| 30000 39999|

För en lyckad validering åtgärd, det är vanligtvis en händelse loggas från DLL-filen DC agenten lösenord filter. För en misslyckad åtgärd för verifiering, finns vanligtvis två händelser som loggats, en från DC-agenttjänsten och en från DC agenten lösenord filter DLL-filen.

Diskret händelser att samla in dessa fall loggas, baserat på följande faktorer:

* Om en viss lösenord håller på att ställa in eller ändrats.
* Om valideringen av en viss lösenordet skickas eller misslyckades.
* Om valideringen misslyckades på grund av Microsoft global princip vs organisationens lösenordsprincip.
* Om endast granskningsläge har för närvarande aktiverats eller inaktiverats för den aktuella lösenordsprincipen.

Viktiga lösenord-verifiering-relaterade händelser är följande:

|   |Lösenordsändring |Lösenordsändring|
| --- | :---: | :---: |
|Godkänd |10014 |10015|
|Ett fel (inte klarade kunden Lösenordsprincip)| 10016, 30002| 10017, 30003|
|Ett fel (inte klarade Microsoft Lösenordsprincip)| 10016, 30004| 10017, 30005|
|Endast granskning Pass (skulle ha misslyckats kunden Lösenordsprincip)| 10024, 30008| 10025, 30007|
|Endast granskning Pass (skulle ha misslyckats Microsoft Lösenordsprincip)| 10024, 30010| 10025, 30009|

> [!TIP]
> Inkommande lösenord verifieras mot listan Microsoft global lösenord först. Om det misslyckas, utförs ingen ytterligare bearbetning. Det här är samma beteende som utförs på ändringar av lösenord i Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Exemplet händelseloggmeddelande för händelse-ID 10014 (lösenord set)

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exemplet händelseloggmeddelande för händelse-ID 10017 och 30003 (misslyckade lösenord set)

10017:

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Exemplet händelseloggmeddelande för händelse-ID 30001 (lösenord accepteras på grund av någon princip som är tillgänglig)

```
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Exemplet händelseloggmeddelande för händelse-ID 30006 (ny princip tillämpas)

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>DC-agenten arbetslogg

DC-agenttjänsten loggar även operativa händelser in i följande:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>DC-agenten spårningsloggen

DC-Agenttjänsten kan också logga utförliga felsökningsnivå spårningshändelser in i följande:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Spårningsloggning är inaktiverad som standard.

> [!WARNING]
>  När aktiverad, tar emot ett stort antal händelser i spårningsloggen och kan påverka domänkontrollantens prestanda. Den här förbättrade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

#### <a name="dc-agent-text-logging"></a>DC-agenten textloggning

DC-agent-tjänsten kan konfigureras för att skriva till en textlogg genom att ange följande registervärde:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters! EnableTextLogging = registernyckelvärdet 1 (REG_DWORD)

Textloggning är inaktiverad som standard. En omstart av DC-agenttjänsten måste anges för ändringar i det här värdet ska börja gälla. När aktiverat domänkontrollanten agent-tjänsten skriver till en loggfil som finns under:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Textlogg får samma felsökningsnivå poster som kan loggas till spårningsloggen, men är vanligtvis i ett format som är enklare att granska och analysera.

> [!WARNING]
> När aktiverat den här loggfilen tar emot ett stort antal händelser och kan påverka domänkontrollantens prestanda. Den här förbättrade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD lösenord protection proxy-tjänst

#### <a name="proxy-service-event-logs"></a>Händelseloggar för proxy-tjänsten

Proxy-tjänsten genererar en minimal uppsättning händelser till följande händelseloggar:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

Proxy-tjänsten kan också logga utförliga felsökningsnivå spårningshändelser in i följande:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Spårningsloggning är inaktiverad som standard.

> [!WARNING]
> När aktiverad spårningsloggen tar emot ett stort antal händelser och detta kan påverka prestanda för proxy-värden. Den här loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

#### <a name="proxy-service-text-logging"></a>Textloggning för proxy-tjänsten

Proxy-tjänsten kan konfigureras för att skriva till en textlogg genom att ange följande registervärde:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = registernyckelvärdet 1 (REG_DWORD)

Textloggning är inaktiverad som standard. En omstart av Proxy-tjänsten krävs för ändringar i det här värdet ska börja gälla. När aktiverat proxyn skriver till en loggfil som finns under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Textlogg får samma felsökningsnivå poster som kan loggas till spårningsloggen, men är vanligtvis i ett format som är enklare att granska och analysera.

> [!WARNING]
> När aktiverat den här loggfilen tar emot ett stort antal händelser och kan påverka domänkontrollantens prestanda. Den här förbättrade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

#### <a name="powershell-cmdlet-logging"></a>PowerShell-cmdlet-loggning

De flesta av Azure AD-lösenordsskydd Powershell-cmdlets skriver till en textlogg som finns under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Om en cmdlet-fel inträffar inte och orsak ut lösningen gång, kan även dessa textloggar samråda.

### <a name="emergency-remediation"></a>Vid akutfall reparation

Om en situation där DC-agenttjänsten orsakar problem visas kan DC agent-tjänsten vara stängs omedelbart. DLL-filen DC agenten lösenord filtret fortfarande försöker anropa tjänsten inte körs och loggar händelser (10012, 10013), men alla inkommande lösenord godkänns under den tiden. DC-Agenttjänsten kan sedan också konfigureras via Windows Service Control Manager med en starttyp ”inaktiverad” efter behov.

### <a name="performance-monitoring"></a>Prestandaövervakning

DC-agentprogramvaran för tjänsten installerar ett prestandaräknarobjektet med namnet **Azure AD-lösenordsskydd**. Följande prestandaräknare är tillgängliga:

|Perf räknarnamnet | Beskrivning|
| --- | --- |
|Lösenord som bearbetats |Den här räknaren visar det totala antalet lösenord som bearbetas (godkännas eller avvisas) sedan senaste omstarten.|
|Lösenord som har accepterat |Den här räknaren visar det totala antalet lösenord som godkändes sedan senaste omstarten.|
|Lösenord som har avvisats |Den här räknaren visar det totala antalet lösenord som avvisas sedan senaste omstarten.|
|Lösenord filtrera begäranden pågår |Räknaren visar antalet lösenord filtrera begäranden som för närvarande pågår.|
|Högsta lösenord filtrera begäranden |Den här räknaren visar det högsta antalet samtidiga lösenord filtrera begäranden sedan den senaste omstarten.|
|Lösenord filterfel för begäran |Den här räknaren visar det totala antalet lösenord filtrera begäranden som misslyckades pga ett fel sedan senaste omstarten. Fel kan inträffa när Azure AD lösenord protection DC agent-tjänsten inte körs.|
|Lösenord filter begäranden/sek |Den här räknaren visar det pris som lösenord som bearbetas.|
|Bearbetningstid för lösenord filter begäran |Den här räknaren visar Genomsnittlig tid för att bearbeta en begäran om lösenord filter.|
|Bearbetningstid för högsta lösenord filter begäran |Den här räknaren visar det högsta lösenord filter behandling av tid sedan den senaste omstarten.|
|Lösenord som accepteras på grund av granskningsläge |Den här räknaren visar det totala antalet lösenord som skulle normalt har avvisats, men godkändes eftersom lösenordsprincipen som har konfigurerats för att vara i läget granska (sedan senaste omstarten).|

## <a name="directory-services-repair-mode"></a>Reparationsläge för katalogtjänster

Om domänkontrollanten startas i reparationsläge för katalogtjänster, DC-agenttjänsten som identifierar den här orsakar alla lösenordsverifieringen eller tvingande aktiviteter inaktiveras oavsett aktiva principkonfigurationen.

## <a name="domain-controller-demotion"></a>Degraderingen av domänkontrollanten

Det går för att degradera en domänkontrollant som fortfarande kör DC-agentprogramvaran. Administratörer bör vara medveten men att DC-agentprogramvaran körs och fortsätter att tillämpa principen aktuella lösenord under degraderingen proceduren. Nya lokala administratörslösenordet (anges som en del av degraderingen) verifieras som andra lösenord. Microsoft rekommenderar att väljas säkra lösenord för lokala administratörskonton som en del av en DC degradering procedur; men verifiering av ny lokala administratörslösenordet av DC-agentprogramvaran kan vara söndrande för befintlig degradering operativa procedurer.

När degraderingen har slutförts och domänkontrollanten har startats och körs igen som en normal medlemsserver, återgår DC-agentprogramvaran till som körs i passivt läge. Det kan sedan att avinstallera när som helst.

## <a name="removal"></a>Borttagning

Om det är valt att avinstallera den allmänna förhandsversionen av programvaran och rensa alla relaterade tillstånd från de domäner och skog, kan den här uppgiften utföras med hjälp av följande steg:

> [!IMPORTANT]
> Det är viktigt att utföra dessa steg i ordning. Om valfri instans av tjänsten Proxy lämnas körs skapas med jämna mellanrum igen dess serviceConnectionPoint-objektet. Om valfri instans av DC-agenttjänsten lämnas körs skapas med jämna mellanrum igen dess serviceConnectionPoint-objektet och sysvol-status.

1. Avinstallera lösenordsskydd proxyprogrammet från alla datorer. Det här steget har **inte** kräver en omstart.
2. Avinstallera klientprogrammet DC från alla domänkontrollanter. Det här steget **kräver** en omstart.
3. Ta manuellt bort alla tjänstanslutningspunkter för Proxy i varje domännamngivningskontexten. Platsen för de här objekten kan identifieras med följande Active Directory Powershell-kommando:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Utelämna inte asterisken (”*”) i slutet av $keywords variabelvärdet.

   De resulterande objekten som hittades den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt. 

4. Ta bort alla anslutningspunkter för DC-agenten manuellt i varje domännamngivningskontexten. Det kan finnas en dessa objekt per domänkontrollant i skogen, beroende på hur mycket den allmänna förhandsversionen av programvaran har distribuerats. Platsen för det objektet kan identifieras med följande Active Directory Powershell-kommando:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   De resulterande objekten som hittades den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt.

5. Ta manuellt bort Konfigurationsstatus för skogsnivå. Konfigurationsstatus för skogen underhålls i en behållare i Active Directory konfigurationsnamngivningen. Den kan identifieras och tas bort enligt följande:

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Manuellt ta bort alla sysvol relaterade tillstånd genom att manuellt ta bort följande mapp och dess innehåll:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Om det behövs kan den här sökvägen även komma åt lokalt på en viss domänkontrollant; Standardplatsen är något som liknar följande sökväg:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Den här sökvägen skiljer sig om sysvol-resursen har konfigurerats i en icke-standardplats.

## <a name="next-steps"></a>Nästa steg

Mer information om listor med globala och anpassade förbjudna lösenord, finns i artikeln [förbjuda felaktiga lösenord](concept-password-ban-bad.md)
