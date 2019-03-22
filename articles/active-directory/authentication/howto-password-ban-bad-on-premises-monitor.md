---
title: Övervakning och loggning i Azure AD-lösenordsskydd – Azure Active Directory
description: Förstå Azure AD lösenordsskydd övervakning och loggning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: b79e9e1a274002514561ef3f96c364bf9bc27071
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309613"
---
# <a name="azure-ad-password-protection-monitoring-and-logging"></a>Azure AD-lösenordsskydd övervakning och loggning

Är viktiga uppgifter efter distribution av Azure AD-lösenordsskydd, övervakning och rapportering. Den här artikeln innehåller information för att du lär dig vilka olika övervakning tekniker, inklusive där varje tjänst loggar information och rapportera om användning av Azure AD-lösenordsskydd.

## <a name="dc-agent-event-logging"></a>Händelseloggning för DC-agent

På varje domänkontrollant skriver DC service Agentprogrammet som är resultatet av varje enskild åtgärd för verifiering (och andra status) till en lokal händelselogg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

DC-agentloggen för administratören är den primära källan för information om hur programvaran fungerar.

Observera att spårningsloggen är inaktiverat som standard.

Händelser som loggats av olika DC-agentkomponenterna faller inom följande intervall:

|Komponent |Händelse-ID-intervall|
| --- | --- |
|DC-agenten lösenord DLL-fil| 10000-19999|
|Värdprocessen för DC agent-tjänsten| 20000-29999|
|Validering av logik för DC agent-tjänsten princip| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>DC-agenten Admin-händelseloggen

### <a name="password-validation-outcome-events"></a>Lösenord verifiering resultatet händelser

På varje domänkontrollant skriver DC service Agentprogrammet som är resultatet av varje enskild lösenordsverifieringen DC agenten admin-händelseloggen.

För en lyckad validering åtgärd, det är vanligtvis en händelse loggas från DLL-filen DC agenten lösenord filter. För en misslyckad åtgärd för verifiering, finns vanligtvis två händelser som loggats, en från DC-agenttjänsten och en från DC agenten lösenord filter DLL-filen.

Diskret händelser att samla in dessa fall loggas, baserat på följande faktorer:

* Om en viss lösenord håller på att ställa in eller ändrats.
* Om valideringen av en viss lösenordet skickas eller misslyckades.
* Om valideringen misslyckades på grund av den globala principen för Microsoft, organisationens princip eller en kombination.
* Om endast granskningsläge har för närvarande aktiverats eller inaktiverats för den aktuella lösenordsprincipen.

Viktiga lösenord-verifiering-relaterade händelser är följande:

|   |Lösenordsändring |Lösenordsändring|
| --- | :---: | :---: |
|Godkänd |10014 |10015|
|Misslyckas (på grund av kunden Lösenordsprincip)| 10016, 30002| 10017, 30003|
|Misslyckas (på grund av Microsoft Lösenordsprincip)| 10016, 30004| 10017, 30005|
|Misslyckas (på grund av kombinerade Microsoft och kundens lösenordsprinciper)| 10016, 30026| 10017, 30027|
|Endast granskning Pass (skulle ha misslyckats kunden Lösenordsprincip)| 10024, 30008| 10025, 30007|
|Endast granskning Pass (skulle ha misslyckats Microsoft Lösenordsprincip)| 10024, 30010| 10025, 30009|
|Endast granskning Pass (skulle ha misslyckats kombinerade Microsoft och kundens lösenordsprinciper)| 10024, 30028| 10025, 30029|

Ärenden i tabellen ovan som refererar till ”kombineras principer” refererar till situationer där en användares lösenord hittades som innehåller minst en token från både Microsoft förbjudna lösenord listan och kundlistan som förbjuds lösenord.

När ett par med händelser loggas tillsammans, associerade både händelser uttryckligen genom att använda samma CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Lösenord verifieringssammanfattning reporting via PowerShell

Den `Get-AzureADPasswordProtectionSummaryReport` cmdlet kan användas för att generera en övergripande vy över aktiviter för verifiering av lösenord. Ett exempel på utdata från denna cmdlet är följande:

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Omfånget för cmdletens reporting kan påverkas med någon av parametrarna – skog, - domän eller – DomainController. Inte anger en parameter innebär – skog.

Den `Get-AzureADPasswordProtectionSummaryReport` cmdlet fungerar genom att fråga DC agenten admin-händelseloggen och sedan räkna det totala antalet händelser som motsvarar varje kategori visas resultatet. I följande tabell innehåller mappningar mellan varje resultat och dess motsvarande händelse-id:

|Get-AzureADPasswordProtectionSummaryReport property |Motsvarande händelse-id|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Observera att den `Get-AzureADPasswordProtectionSummaryReport` cmdlet levererat i form av PowerShell-skript och vid behov kan refereras direkt på följande plats:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Denna cmdlet fungerar genom att öppna en PowerShell-session till varje domänkontrollant. För att lyckas, stöd för PowerShell-fjärrsession måste vara aktiverat på varje domänkontrollant och klienten måste ha tillräcklig behörighet. Mer information om kraven för PowerShell-fjärrsession kör du ”Get-Help about_Remote_Troubleshooting” i ett PowerShell-fönster.

> [!NOTE]
> Denna cmdlet fungerar genom att via fjärranslutning fråga varje DC agenttjänsten Admin-händelseloggen. Om Loggboken innehåller stora mängder händelser, kan cmdlet: en ta lång tid att slutföra. Massinläsning nätverket förfrågningar av stora datauppsättningar kan också påverka domänkontrollantens prestanda. Den här cmdleten bör därför användas noggrant i produktionsmiljöer.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Exemplet händelseloggmeddelande för händelse-ID 10014 (lyckad lösenordsändring)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exemplet händelseloggmeddelande för händelse-ID 10017 och 30003 (misslyckade lösenord set)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Exemplet händelseloggmeddelande för händelse-ID 30001 (lösenord accepteras på grund av någon princip som är tillgänglig)

```text
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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Exemplet händelseloggmeddelande för händelse-ID 30006 (ny princip tillämpas)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Exemplet händelseloggmeddelande för händelse-ID 30019 (Azure AD-lösenordsskydd är inaktiverat)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>DC-agenten arbetslogg

DC-agenttjänsten loggar även operativa händelser in i följande:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>DC-agenten spårningsloggen

DC-Agenttjänsten kan också logga utförliga felsökningsnivå spårningshändelser in i följande:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Spårningsloggning är inaktiverad som standard.

> [!WARNING]
> När aktiverad, tar emot ett stort antal händelser i spårningsloggen och kan påverka domänkontrollantens prestanda. Den här förbättrade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

## <a name="dc-agent-text-logging"></a>DC-agenten textloggning

DC-agent-tjänsten kan konfigureras för att skriva till en textlogg genom att ange följande registervärde:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Textloggning är inaktiverad som standard. En omstart av DC-agenttjänsten måste anges för ändringar i det här värdet ska börja gälla. När aktiverat domänkontrollanten agent-tjänsten skriver till en loggfil som finns under:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Textlogg får samma felsökningsnivå poster som kan loggas till spårningsloggen, men är vanligtvis i ett format som är enklare att granska och analysera.

> [!WARNING]
> När aktiverat den här loggfilen tar emot ett stort antal händelser och kan påverka domänkontrollantens prestanda. Den här förbättrade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

## <a name="dc-agent-performance-monitoring"></a>Prestandaövervakning för DC-agent

DC-agentprogramvaran för tjänsten installerar ett prestandaräknarobjektet med namnet **Azure AD-lösenordsskydd**. Följande prestandaräknare är tillgängliga:

|Perf räknarnamnet | Beskrivning|
| --- | --- |
|Lösenord som bearbetats |Den här räknaren visar det totala antalet lösenord som bearbetas (godkännas eller avvisas) sedan senaste omstarten.|
|Lösenord som har accepterat |Den här räknaren visar det totala antalet lösenord som godkändes sedan senaste omstarten.|
|Lösenord som har avvisats |Den här räknaren visar det totala antalet lösenord som avvisas sedan senaste omstarten.|
|Lösenord filtrera begäranden pågår |Räknaren visar antalet lösenord filtrera begäranden som för närvarande pågår.|
|Högsta lösenord filtrera begäranden |Den här räknaren visar det högsta antalet samtidiga lösenord filtrera begäranden sedan den senaste omstarten.|
|Lösenord filterfel för begäran |Den här räknaren visar det totala antalet lösenord filtrera begäranden som misslyckades pga ett fel sedan senaste omstarten. Fel kan inträffa när Azure AD-lösenord DC Protection agent-tjänsten inte körs.|
|Lösenord filter begäranden/sek |Den här räknaren visar det pris som lösenord som bearbetas.|
|Bearbetningstid för lösenord filter begäran |Den här räknaren visar Genomsnittlig tid för att bearbeta en begäran om lösenord filter.|
|Bearbetningstid för högsta lösenord filter begäran |Den här räknaren visar det högsta lösenord filter behandling av tid sedan den senaste omstarten.|
|Lösenord som accepteras på grund av granskningsläge |Den här räknaren visar det totala antalet lösenord som skulle normalt har avvisats, men godkändes eftersom lösenordsprincipen som har konfigurerats för att vara i läget granska (sedan senaste omstarten).|

## <a name="dc-agent-discovery"></a>Identifiering av DC-Agent

Den `Get-AzureADPasswordProtectionDCAgent` cmdlet kan användas för att visa grundläggande information om de olika DC-agenter som körs i en domän eller skog. Den här informationen hämtas från serviceConnectionPoint-objekt som har registrerats av körs DC agenten tjänster.

Ett exempel på utdata från denna cmdlet är följande:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

De olika egenskaperna uppdateras med varje DC-agenttjänsten ungefärliga timme. Data kan fortfarande komma replikeringsfördröjning för Active Directory.

Omfånget för cmdletens fråga kan påverkas med hjälp av antingen parametrarna – skog eller -domän.

Om värdet HeartbeatUTC blir inaktuella, kan detta vara ett tecken på att Azure AD-lösenord Protection DC-agenten på den domänkontrollanten körs inte eller har avinstallerats eller den virtuella datorn har degraderas och inte längre är en domänkontrollant.

Om PasswordPolicyDateUTC värdet hämtar inaktuella, detta kan vara ett tecken på att Azure AD-lösenord DC Skyddsagenten på den datorn har inte fungerar korrekt.

## <a name="proxy-service-event-logging"></a>Händelseloggning för proxy-tjänsten

Proxy-tjänsten genererar en minimal uppsättning händelser till följande händelseloggar:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Observera att spårningsloggen är inaktiverat som standard.

> [!WARNING]
> När aktiverad spårningsloggen tar emot ett stort antal händelser och detta kan påverka prestanda för proxy-värden. Den här loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

Händelser som loggas av olika Proxy-komponenter med hjälp av följande intervall:

|Komponent |Händelse-ID-intervall|
| --- | --- |
|Värdprocessen för proxy-tjänst| 10000-19999|
|Proxy-tjänsten core affärslogik| 20000-29999|
|PowerShell-cmdletar| 30000-39999|

## <a name="proxy-service-text-logging"></a>Textloggning för proxy-tjänsten

Proxy-tjänsten kan konfigureras för att skriva till en textlogg genom att ange följande registervärde:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD value)

Textloggning är inaktiverad som standard. En omstart av Proxy-tjänsten krävs för ändringar i det här värdet ska börja gälla. När aktiverat proxyn skriver till en loggfil som finns under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Textlogg får samma felsökningsnivå poster som kan loggas till spårningsloggen, men är vanligtvis i ett format som är enklare att granska och analysera.

> [!WARNING]
> När aktiverat den här loggfilen tar emot ett stort antal händelser och kan påverka datorns prestanda. Den här förbättrade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

## <a name="powershell-cmdlet-logging"></a>PowerShell-cmdlet-loggning

PowerShell-cmdletar som resulterar i en tillståndsändring (till exempel registrera-AzureADPasswordProtectionProxy) loggar normalt en händelse i resultatet till arbetsloggen.

Dessutom kan skriver de flesta av Azure AD-lösenord Protection PowerShell-cmdlets till en textlogg som finns under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Om en cmdlet-fel inträffar inte och orsak ut lösningen gång, kan även dessa textloggar samråda.

## <a name="proxy-discovery"></a>Proxy-identifiering

Den `Get-AzureADPasswordProtectionProxy` cmdlet kan användas för att visa grundläggande information om de olika Azure AD-lösenord Protection Proxy-tjänsterna som körs i en domän eller skog. Den här informationen hämtas från serviceConnectionPoint-objekt som har registrerats av körs Proxy-tjänster.

Ett exempel på utdata från denna cmdlet är följande:

```PowerShell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

De olika egenskaperna uppdateras med varje proxytjänsten ungefärliga timme. Data kan fortfarande komma replikeringsfördröjning för Active Directory.

Omfånget för cmdletens fråga kan påverkas med hjälp av antingen parametrarna – skog eller -domän.

Om värdet HeartbeatUTC blir inaktuella, kan detta vara ett tecken på att Azure AD-lösenord Protection Proxy på den datorn inte körs eller har avinstallerats.

## <a name="next-steps"></a>Nästa steg

[Felsökning för Azure AD-lösenordsskydd](howto-password-ban-bad-on-premises-troubleshoot.md)

Mer information om listor med globala och anpassade förbjudna lösenord, finns i artikeln [förbjuda felaktiga lösenord](concept-password-ban-bad.md)
