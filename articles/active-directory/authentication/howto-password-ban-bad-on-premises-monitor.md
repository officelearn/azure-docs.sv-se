---
title: Övervaka lokalt Azure AD-lösenordsskydd
description: Lär dig hur du övervakar och granskar loggar för Azure AD-lösenordsskydd för en lokal Active Directory Domain Services-miljö
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d67d867249286ad1591b441bbe5ea2637971e104
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652616"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Övervaka och granska loggar för lokala Azure AD-lösenordsskyddsmiljöer

Efter distributionen av Azure AD Password Protection är övervakning och rapportering viktiga uppgifter. Den här artikeln innehåller detaljerad information som hjälper dig att förstå olika övervakningstekniker, inklusive var varje tjänst loggar information och hur du rapporterar om användningen av Azure AD Password Protection.

Övervakning och rapportering görs antingen av händelseloggmeddelanden eller genom att köra PowerShell-cmdlets. Dc-agenten och proxytjänsterna loggar båda händelseloggmeddelanden. Alla PowerShell-cmdletar som beskrivs nedan är endast tillgängliga på proxyservern (se AzureADPasswordProtection PowerShell-modulen). DC-agentprogramvaran installerar inte en PowerShell-modul.

## <a name="dc-agent-event-logging"></a>Dc-agent händelseloggning

På varje domänkontrollant skriver DC-agenttjänstens programvara resultaten av varje enskild lösenordsverifieringsåtgärd (och annan status) till en lokal händelselogg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

DS-agentadministratörsloggen är den primära informationskällan för hur programvaran beter sig.

Observera att spårningsloggen är inaktiverad som standard.

Händelser som loggas av de olika DC-agentkomponenterna ligger inom följande intervall:

|Komponent |Intervall för händelse-ID|
| --- | --- |
|DLL-lösenordsfilter för DC-agent| 10000-19999|
|Värdprocess för DC-agenttjänster| 20000-29999|
|Valideringslogik för DC-agenttjänstprincip| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Händelselogg för DC-agentadministratör

### <a name="password-validation-outcome-events"></a>Händelser för lösenordsvalidering

På varje domänkontrollant skriver DC-agenttjänstens programvara resultatet av varje enskild lösenordsvalidering till händelseloggen för DC-agentadministratör.

För en lyckad lösenordsverifieringsåtgärd loggas vanligtvis en händelse från DLL-agentens lösenordsfilter dll. För en misslyckad lösenordsverifieringsåtgärd loggas vanligtvis två händelser, en från DC-agenttjänsten och en från DLL-filtret DC Agent-lösenordsfilter dll.

Diskreta händelser för att fånga dessa situationer loggas, baserat på följande faktorer:

* Om ett visst lösenord ställs in eller ändras.
* Om valideringen av ett visst lösenord har skickats eller misslyckats.
* Om valideringen misslyckades på grund av Microsofts globala princip, organisationsprincipen eller en kombination.
* Om granskningsläget bara är aktiverat eller inaktiverat för den aktuella lösenordsprincipen.

De viktigaste lösenordsvalideringen är följande:

|   |Lösenordsändring |Lösenordsuppsättning|
| --- | :---: | :---: |
|Passera |10014 |10015|
|Misslyckas (på grund av principen om kundlösenord)| 10016, 30002| 10017, 30003|
|Misslyckas (på grund av Microsofts lösenordsprincip)| 10016, 30004| 10017, 30005|
|Misslyckas (på grund av kombinerade Microsoft- och kundlösenordsprinciper)| 10016, 30026| 10017, 30027|
|Granskningspass (skulle ha misslyckats med lösenordsprincip för kunder)| 10024, 30008| 10025, 30007|
|Granskningspass (skulle ha misslyckats med Microsofts lösenordsprincip)| 10024, 30010| 10025, 30009|
|Granskningspass (skulle ha misslyckats kombinerade Microsoft- och kundlösenordsprinciper)| 10024, 30028| 10025, 30029|

De fall i tabellen ovan som hänvisar till "kombinerade principer" refererar till situationer där en användares lösenord befanns innehålla minst en token från både Microsofts förbjudna lösenordslista och listan över förbjudna lösenord för kunder.

När ett par händelser loggas tillsammans associeras båda händelserna uttryckligen genom att ha samma CorrelationId.

### <a name="password-validation-summary-reporting-via-powershell"></a>Sammanfattningsrapportering av lösenordsvalidering via PowerShell

Cmdleten `Get-AzureADPasswordProtectionSummaryReport` kan användas för att skapa en sammanfattande vy över lösenordsverifieringsaktivitet. Ett exempel på denna cmdlet är följande:

```powershell
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

Omfattningen av cmdlelets rapportering kan påverkas med hjälp av en av parametrarna –Skog, Domän eller –DomainController. Att inte ange en parameter innebär –Skog.

Cmdlet `Get-AzureADPasswordProtectionSummaryReport` fungerar genom att fråga dc-agenten admin händelselogg, och sedan räkna det totala antalet händelser som motsvarar varje visas resultatkategori. Följande tabell innehåller mappningar mellan varje resultat och motsvarande händelse-ID:

|Egenskapen Get-AzureADPasswordProtectionSummaryReport |Motsvarande händelse-ID|
| :---: | :---: |
|LösenordÄndringarValiderad |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Observera att `Get-AzureADPasswordProtectionSummaryReport` cmdleten levereras i PowerShell-skriptform och vid behov kan refereras direkt på följande plats:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Den här cmdleten fungerar genom att öppna en PowerShell-session för varje domänkontrollant. För att lyckas måste PowerShell-fjärrsessionsstöd aktiveras på varje domänkontrollant och klienten måste ha tillräckliga privilegier. Om du vill ha mer information om powershell-fjärrsessionskrav kör du Get-Help about_Remote_Troubleshooting i ett PowerShell-fönster.

> [!NOTE]
> Den här cmdleten fungerar genom att fjärrfrågaa varje DC-agenttjänsts administratörshändelselogg. Om händelseloggarna innehåller ett stort antal händelser kan det ta lång tid att slutföra cmdleten. Dessutom kan massnätverksfrågor för stora datauppsättningar påverka domänkontrollantens prestanda. Därför bör denna cmdlet användas noggrant i produktionsmiljöer.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Exempel på händelseloggmeddelande för händelse-ID 10014 (lyckad lösenordsändring)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exempel på händelseloggmeddelande för händelse-ID 10017 och 30003 (fellös lösenordsuppsättning)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Exempel på händelseloggmeddelande för händelse-ID 30001 (lösenord accepteras på grund av ingen princip tillgänglig)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Exempel på händelseloggmeddelande för händelse-ID 30006 (ny princip tillämpas)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Exempel på händelseloggmeddelande för händelse-ID 30019 (Azure AD Password Protection är inaktiverat)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Operativ logg för DC-agent

Dc-agenttjänsten loggar även driftrelaterade händelser i följande logg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Spårningslogg för DC-agent

Dc-agenttjänsten kan också logga spårningshändelser på verbose debug-nivå till följande logg:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Spårningsloggning är inaktiverad som standard.

> [!WARNING]
> När den är aktiverad tar spårningsloggen emot en stor mängd händelser och kan påverka domänkontrollantens prestanda. Därför bör den här förbättrade loggen endast aktiveras när ett problem kräver djupare undersökning och sedan bara för en minimal tid.

## <a name="dc-agent-text-logging"></a>Dc Agent textloggning

Dc-agenttjänsten kan konfigureras för att skriva till en textlogg genom att ange följande registervärde:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Textloggning är inaktiverat som standard. En omstart av dc-agenttjänsten krävs för att ändringar av det här värdet ska börja gälla. När den är aktiverad skrivs DC-agenttjänsten till en loggfil under:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Textloggen tar emot samma poster på felsökningsnivå som kan loggas till spårningsloggen, men som i allmänhet är i ett enklare format att granska och analysera.

> [!WARNING]
> När den här loggen är aktiverad får den här loggen en stor mängd händelser och kan påverka domänkontrollantens prestanda. Därför bör den här förbättrade loggen endast aktiveras när ett problem kräver djupare undersökning och sedan bara för en minimal tid.

## <a name="dc-agent-performance-monitoring"></a>Övervakning av DC-agentens prestanda

Dc-agenttjänsten installerar ett prestandaräknareobjekt med namnet **Azure AD Password Protection**. Följande perf-räknare är för närvarande tillgängliga:

|Perf-räknarnamn | Beskrivning|
| --- | --- |
|Bearbetade lösenord |Den här räknaren visar det totala antalet bearbetade lösenord (accepterade eller avvisade) sedan den senaste omstarten.|
|Lösenord accepteras |Den här räknaren visar det totala antalet lösenord som har accepterats sedan senaste omstarten.|
|Avvisade lösenord |Den här räknaren visar det totala antalet lösenord som avvisats sedan senaste omstarten.|
|Begäran om lösenordsfilter pågår |Den här räknaren visar antalet pågående begäranden för lösenordsfilter.|
|Begäranden om maximalt lösenordsfilter |Den här räknaren visar det högsta antalet samtidiga lösenordsfilterbegäranden sedan den senaste omstarten.|
|Fel i begäran om lösenordsfilter |Den här räknaren visar det totala antalet lösenordsfilterbegäranden som inte kunde ändras på grund av ett fel sedan den senaste omstarten. Fel kan uppstå när Dc-agenttjänsten för azure AD-lösenordsskydd inte körs.|
|Begäranden om lösenordsfilter/sek |Den här räknaren visar hur hög hastighet lösenord bearbetas.|
|Bearbetningstid för lösenordsfilterbegäran |Den här räknaren visar den genomsnittliga tid som krävs för att bearbeta en begäran om lösenordsfilter.|
|Bearbetningstid för högsta lösenordsfilterbegäran |Den här räknaren visar den maximala handläggningstiden för lösenordsfilterbegäran sedan den senaste omstarten.|
|Lösenord som accepteras på grund av granskningsläge |Den här räknaren visar det totala antalet lösenord som normalt skulle ha avvisats, men accepterades eftersom lösenordsprincipen har konfigurerats för att vara i granskningsläge (sedan senaste omstart).|

## <a name="dc-agent-discovery"></a>Identifiering av DC-agent

Cmdleten `Get-AzureADPasswordProtectionDCAgent` kan användas för att visa grundläggande information om de olika DC-agenter som körs i en domän eller skog. Den här informationen hämtas från serviceConnectionPoint-objekt som registrerats av de dc-agenttjänster som körs.

Ett exempel på denna cmdlet är följande:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

De olika egenskaperna uppdateras av varje DC-agenttjänst ungefär per timme. Data är fortfarande föremål för Active Directory-replikeringsfördröjning.

Omfattningen av cmdletens fråga kan påverkas med hjälp av parametrarna –Skog eller –Domän.

Om HeartbeatUTC-värdet blir inaktuellt kan det vara ett symptom på att AZURE AD Password Protection DC Agent på domänkontrollanten inte körs eller har avinstallerats, eller så har datorn degraderats och inte längre är en domänkontrollant.

Om värdet PasswordPolicyDateUTC blir inaktuellt kan det vara ett symptom på att AZURE AD Password Protection DC Agent på den datorn inte fungerar som den ska.

## <a name="dc-agent-newer-version-available"></a>DC-agent nyare version tillgänglig

Dc-agenttjänsten loggar en 30034-varningshändelse till driftloggen när du upptäcker att en nyare version av DC-agentprogramvaran är tillgänglig, till exempel:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Händelsen ovan anger inte versionen av den nyare programvaran. Du bör gå till länken i händelsemeddelandet för den informationen.

> [!NOTE]
> Trots hänvisningarna till "autoupgrade" i händelsemeddelandet ovan stöder DC-agentprogramvaran för närvarande inte den här funktionen.

## <a name="proxy-service-event-logging"></a>Händelseloggning av proxytjänst

Proxy-tjänsten avger en minimal uppsättning händelser till följande händelseloggar:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Observera att spårningsloggen är inaktiverad som standard.

> [!WARNING]
> När den är aktiverad tar spårningsloggen emot en stor mängd händelser och detta kan påverka proxyvärdens prestanda. Därför bör den här loggen endast aktiveras när ett problem kräver djupare undersökning och sedan bara för en minimal tid.

Händelser loggas av de olika proxykomponenterna med hjälp av följande intervall:

|Komponent |Intervall för händelse-ID|
| --- | --- |
|Proxytjänstvärdprocess| 10000-19999|
|Affärslogik för proxytjänst| 20000-29999|
|PowerShell-cmdletar| 30000-39999|

## <a name="proxy-service-text-logging"></a>Textloggning av proxytjänst

Proxytjänsten kan konfigureras för att skriva till en textlogg genom att ange följande registervärde:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parametrar! EnableTextLogging = 1 (REG_DWORD värde)

Textloggning är inaktiverat som standard. En omstart av proxytjänsten krävs för att ändringar av det här värdet ska börja gälla. När proxytjänsten är aktiverad skrivs till en loggfil under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Textloggen tar emot samma poster på felsökningsnivå som kan loggas till spårningsloggen, men som i allmänhet är i ett enklare format att granska och analysera.

> [!WARNING]
> När den här loggen är aktiverad får den här loggen en stor mängd händelser och kan påverka maskinens prestanda. Därför bör den här förbättrade loggen endast aktiveras när ett problem kräver djupare undersökning och sedan bara för en minimal tid.

## <a name="powershell-cmdlet-logging"></a>PowerShell-cmdletloggning

PowerShell-cmdletar som resulterar i en tillståndsändring (till exempel Register-AzureADPasswordProtectionProxy) loggar normalt en resultathändelse till driftloggen.

Dessutom skriver de flesta azure AD Password Protection PowerShell-cmdlets till en textlogg under:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Om ett cmdletfel inträffar och orsaken och\eller lösningen inte är lätt uppenbar, kan dessa textloggar också konsulteras.

## <a name="proxy-discovery"></a>Identifiering av proxy

Cmdleten `Get-AzureADPasswordProtectionProxy` kan användas för att visa grundläggande information om de olika Azure AD Password Protection Proxy-tjänsterna som körs i en domän eller skog. Den här informationen hämtas från serviceConnectionPoint-objekt som registrerats av proxytjänsten/proxytjänsten.This information is retrieved from the serviceConnectionPoint object(s) registered by the running Proxy service(s).

Ett exempel på denna cmdlet är följande:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

De olika egenskaperna uppdateras av varje proxytjänst ungefär en timme. Data är fortfarande föremål för Active Directory-replikeringsfördröjning.

Omfattningen av cmdletens fråga kan påverkas med hjälp av parametrarna –Skog eller –Domän.

Om HeartbeatUTC-värdet blir inaktuellt kan det vara ett symptom på att Azure AD Password Protection Proxy på den datorn inte körs eller har avinstallerats.

## <a name="proxy-agent-newer-version-available"></a>Proxy agent nyare version tillgänglig

Proxy-tjänsten loggar en 20002 varningshändelse till driftloggen när du upptäcker att en nyare version av proxyprogramvaran är tillgänglig, till exempel:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Händelsen ovan anger inte versionen av den nyare programvaran. Du bör gå till länken i händelsemeddelandet för den informationen.

Den här händelsen kommer att skickas ut även om proxyagenten är konfigurerad med automatisk uppgrad aktiverad.

## <a name="next-steps"></a>Nästa steg

[Felsökning för Lösenordsskydd för Azure AD](howto-password-ban-bad-on-premises-troubleshoot.md)

Mer information om de globala och anpassade förbjudna [lösenordslistorna](concept-password-ban-bad.md) finns i artikeln Ban bad passwords
