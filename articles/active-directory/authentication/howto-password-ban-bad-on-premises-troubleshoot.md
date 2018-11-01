---
title: Felsökning och loggning i förhandsversionen av Azure AD lösenord protection
description: Förstå Azure AD-lösenordsskydd Förhandsgranska loggning och felsökning av vanliga problem
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6832f6f9d09cbbfea6ccaa69160ad93209c7ac8c
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741189"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Förhandsversion: Azure AD lösenord protection övervakning, rapportering och felsökning

|     |
| --- |
| Azure AD-lösenordsskydd är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Är viktiga uppgifter efter distributionen av Azure AD-lösenordsskydd övervakning och rapportering. Den här artikeln innehåller information för att du förstår där varje tjänst loggar information och rapportera om användning av Azure AD-lösenordsskydd.

## <a name="on-premises-logs-and-events"></a>Lokala loggar och händelser

### <a name="dc-agent-service"></a>DC-agenttjänsten

På varje domänkontrollant skriver DC service Agentprogrammet som är resultatet av sitt lösenord verifieringar (och andra status) till en lokal händelselogg: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

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

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Exemplet händelseloggmeddelande för händelse-ID 10014 lösenord

Lösenordet för den angivna användaren har verifierats som kompatibel med den aktuella lösenord för Azure-principen.

 Användarnamn: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exemplet händelseloggmeddelande för händelse-ID 10017 och 30003 misslyckades lösenord

10017:

Återställning av lösenord för den angivna användaren avvisades eftersom den inte uppfyller den aktuella lösenord för Azure-principen. Se korrelerad händelseloggmeddelande för mer information.

 Användarnamn: BPL_03283841185 FullName:

30003:

Återställning av lösenord för den angivna användaren avvisades eftersom den matchade minst en av token i per klient-förbjudna lösenord lista över aktuella lösenord för Azure.

 Användarnamn: BPL_03283841185 FullName:

Vissa andra viktiga meddelanden känna till är:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Exemplet händelseloggmeddelande för händelse-ID 30001

Lösenordet för den angivna användaren godkändes eftersom en princip för lösenord för Azure inte är tillgänglig än

Användarnamn: SomeUser FullName: vissa användare

Det här tillståndet kan ha orsakats av en eller flera av följande orsaker: % n

1. Skogen har ännu inte har registrerats med Azure.

   Lösningssteg: en administratör måste registrera skogen med hjälp av cmdleten Register-AzureADPasswordProtectionForest.

2. En Azure AD-lösenordsskydd Proxy ännu inte finns tillgänglig på minst en dator i den aktuella skogen.

   Lösningssteg: en administratör måste installera och registrera en proxy med hjälp av cmdleten Register-AzureADPasswordProtectionProxy.

3. Den här domänkontrollanten har inte nätverksanslutning till alla Azure AD lösenord protection Proxy-instanser.

   Lösningssteg: se till att det finns en nätverksanslutning till minst en Azure AD lösenord protection Proxy-instans.

4. Den här domänkontrollanten har inte anslutning till andra domänkontrollanter i domänen.

   Lösningssteg: se till att det finns en nätverksanslutning till domänen.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Exemplet händelseloggmeddelande för händelse-ID 30006

Tjänsten är nu tillämpa följande Azure lösenordsprincip.

 AuditOnly: 1

 Global princip datum: 2018-05-15T00:00:00.000000000Z

 Klient-princip datum: 2018-06-10T20:15:24.432457600Z

 Framtvinga princip för klient: 1

#### <a name="dc-agent-log-locations"></a>DC-agenten loggfilernas placering

DC-agenttjänsten loggar även operativa händelser in i följande: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

DC-Agenttjänsten kan också logga utförliga felsökningsnivå spårningshändelser in i följande: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> Spårningsloggen är inaktiverad som standard. När aktiverat den här loggfilen tar emot ett stort antal händelser och kan påverka domänkontrollantens prestanda. Den här förbättrade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD lösenord protection proxy-tjänst

Lösenordsskydd Proxy-tjänsten genererar en minimal uppsättning händelser till händelseloggen i följande: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

Lösenordsskydd Proxy-tjänsten kan också logga utförliga felsökningsnivå spårningshändelser in i följande: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> Spårningsloggen är inaktiverad som standard. När aktiverat den här loggfilen tar emot ett stort antal händelser och detta kan påverka prestanda för proxy-värden. Den här loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

### <a name="dc-agent-discovery"></a>Identifiering av DC-Agent

Den `Get-AzureADPasswordProtectionDCAgent` cmdlet kan användas för att visa grundläggande information om de olika DC-agenter som körs i en domän eller skog. Den här informationen hämtas från serviceConnectionPoint-objekt som har registrerats av körs DC agenten tjänster. Ett exempel på utdata från denna cmdlet är följande:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

De olika egenskaperna uppdateras med varje DC-agenttjänsten ungefärliga timme. Data kan fortfarande komma replikeringsfördröjning för Active Directory.

Omfånget för cmdletens fråga kan påverkas med hjälp av antingen parametrarna – skog eller -domän.

### <a name="emergency-remediation"></a>Vid akutfall reparation

Om en olycklig situation uppstår där DC-agenttjänsten orsakar problem, kan DC agent-tjänsten vara stängs omedelbart. DLL-filen DC agenten lösenord filter försöker anropa tjänsten inte körs och loggar händelser (10012, 10013), men alla inkommande lösenord godkänns under den tiden. DC-Agenttjänsten kan sedan också konfigureras via Windows Service Control Manager med en starttyp ”inaktiverad” efter behov.

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
> Det är viktigt att utföra dessa steg i ordning. Om en instans av lösenordsskydd proxytjänsten lämnas kommer köra det regelbundet återskapa dess serviceConnectionPoint objekt samt regelbundet återskapar sysvol-status.

1. Avinstallera lösenordsskydd proxyprogrammet från alla datorer. Det här steget har **inte** kräver en omstart.
2. Avinstallera klientprogrammet DC från alla domänkontrollanter. Det här steget **kräver** en omstart.
3. Ta manuellt bort alla tjänstanslutningspunkter för proxy i varje domännamngivningskontexten. Platsen för de här objekten kan identifieras med följande Active Directory Powershell-kommando:
   ```
   $scp = "serviceConnectionPoint"
   $keywords = "{EBEFB703-6113-413D-9167-9F8DD4D24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Utelämna inte asterisken (”*”) i slutet av $keywords variabelvärdet.

   Det resulterande objektet för att hitta den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt. 

4. Ta bort alla anslutningspunkter för DC-agenten manuellt i varje domännamngivningskontexten. Det kan finnas en dessa objekt per domänkontrollant i skogen, beroende på hur mycket den allmänna förhandsversionen av programvaran har distribuerats. Platsen för det objektet kan identifieras med följande Active Directory Powershell-kommando:

   ```
   $scp = "serviceConnectionPoint"
   $keywords = "{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Det resulterande objektet för att hitta den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tagits bort manuellt.

5. Ta manuellt bort Konfigurationsstatus för skogsnivå. Konfigurationsstatus för skogen underhålls i en behållare i Active Directory konfigurationsnamngivningen. Den kan identifieras och tas bort enligt följande:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Manuellt ta bort alla sysvol relaterade tillstånd genom att manuellt ta bort följande mapp och dess innehåll:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Om det behövs kan den här sökvägen även komma åt lokalt på en viss domänkontrollant; Standardplatsen är något som liknar följande sökväg:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Den här sökvägen skiljer sig om sysvol-resursen har konfigurerats i en icke-standardplats.

## <a name="next-steps"></a>Nästa steg

Mer information om listor med globala och anpassade förbjudna lösenord, finns i artikeln [förbjuda felaktiga lösenord](concept-password-ban-bad.md)
