---
title: Felsökning och loggning i förhandsversionen av Azure AD lösenord protection
description: Förstå Azure AD-lösenordsskydd Förhandsgranska loggning och felsökning av vanliga problem
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e5b3dc1bfa7c7890be83529e863907ec056f188f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295661"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Förhandsversion: Azure AD lösenord skydd övervakning, rapportering och felsökning

|     |
| --- |
| Azure AD-lösenordsskydd och lösenordslistan över anpassade förbjudna är förhandsversion funktioner i Azure Active Directory. Läs mer om förhandsgranskningar [kompletterande användningsvillkor för Microsoft Azure-förhandsgranskningar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Är viktiga uppgifter efter distributionen av Azure AD-lösenordsskydd övervakning och rapportering. Den här artikeln innehåller information för att du förstår där varje tjänst loggar information och rapportera om användning av Azure AD-lösenordsskydd.

## <a name="on-premises-logs-and-events"></a>Lokala loggar och händelser

### <a name="dc-agent-service"></a>DC-agenttjänsten

På varje domänkontrollant skriver DC service Agentprogrammet som är resultatet av dess lösenord verifieringar (och andra status) till en lokal händelselogg: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

Händelser som loggas av olika DC agent-komponenter med hjälp av följande intervall:

|Komponent |Händelse-ID-intervall|
| --- | --- |
|DC Agent lösenord DLL-fil| 10000 19999|
|Värdprocessen för DC agent-tjänsten| 20000 29999|
|Logik för DC agent-tjänsten verifiering| 30000 39999|

För en lyckad klusterverifieringen finns vanligtvis en händelse loggas från den DC agent lösenord DLL-fil. För en misslyckad åtgärd för verifiering, finns vanligtvis två händelser som loggats, en från DC-agenttjänsten och en från DLL-filen för DC Agent lösenord filter.

Diskreta händelser att samla in dessa situationer loggas baserat runt följande faktorer:

* Om en lösenordet som anges eller ändras.
* Om valideringen av en viss lösenordet skickas eller misslyckades.
* Om valideringen misslyckades på grund av Microsoft global princip vs organisationens principer.
* Om endast granskningsläge är för närvarande aktiverad eller inaktiverad för den aktuella lösenordsprincipen.

Viktiga lösenord-validering relaterade händelser är följande:

|   |Lösenordsändring |Ange användarlösenord|
| --- | :---: | :---: |
|Godkänd |10014 |10015|
|Ett fel (inte klarade kunden Lösenordsprincip)| 10016, 30002| 10017, 30003|
|Ett fel (inte klarade Microsoft Lösenordsprincip)| 10016, 30004| 10017, 30005|
|Endast granskning Pass (skulle ha misslyckats kunden Lösenordsprincip)| 10024, 30008| 10025, 30007|
|Endast granskning Pass (skulle ha misslyckats Microsoft Lösenordsprincip)| 10024, 30010| 10025, 30009|

> [!TIP]
> Inkommande lösenord verifieras mot Microsoft global lösenord listan först. Om detta misslyckas utförs ingen ytterligare bearbetning. Detta är samma beteende som utförs på ändring av lösenord i Azure.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Exempel händelseloggmeddelande för händelse-ID 10014 lyckade lösenord

Ändra lösenord för den angivna användaren har verifierats som kompatibla med den aktuella Azure lösenordsprincipen.

 Användarnamn: BPL_02885102771 fullständigt namn:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Exempel händelseloggmeddelande för händelse-ID 10017 och 30003 misslyckades lösenord

10017:

Återställ lösenordet för den angivna användaren avvisades eftersom den inte uppfyller den aktuella Azure lösenordsprincipen. Se meddelandet korrelerade händelseloggen för mer information.

 Användarnamn: BPL_03283841185 fullständigt namn:

30003:

Återställ lösenordet för den angivna användaren avvisades eftersom den matchade minst en av token som finns i listan per klient-förbjudna lösenord för den aktuella Azure lösenordsprincipen.

 Användarnamn: BPL_03283841185 fullständigt namn:

Vissa andra viktiga händelseloggmeddelanden vara medveten om är:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Exempel händelseloggmeddelande för händelse-ID 30001

Lösenordet för den angivna användaren accepterades eftersom ett lösenord för Azure-principen inte är tillgänglig ännu

Användarnamn: <user> FullName: <user>

Det här tillståndet kan bero på en eller flera av följande orsaker: % n

1. Skogen har ännu inte har registrerats med Azure.

   Lösningssteg: en administratör måste registrera skogen med hjälp av cmdleten Register-AzureADPasswordProtectionForest.

2. En Azure AD-lösenordsskydd Proxy ännu inte finns tillgänglig på minst en dator i den aktuella skogen.

   Lösningssteg: en administratör måste installera och registrera en proxy cmdleten Register-AzureADPasswordProtectionProxy.

3. Den här domänkontrollanten har inte nätverksanslutning till alla Azure AD lösenord skydd Proxy-instanser.

   Lösningssteg: se till att det finns en nätverksanslutning till minst en Azure AD lösenord skydd Proxy-instans.

4. Den här domänkontrollanten har inte anslutning till andra domänkontrollanter i domänen.

   Lösningssteg: se till att det finns en nätverksanslutning till domänen.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Exempel händelseloggmeddelande för händelse-ID 30006

Tjänsten är nu tvingande följande Azure lösenordsprincip.

 AuditOnly: 1

 Global princip datum: 2018-05-15T00:00:00.000000000Z

 Klient princip datum: 2018-06-10T20:15:24.432457600Z

 Tillämpa princip för klient: 1

#### <a name="dc-agent-log-locations"></a>DC Agent loggfilernas placering

DC-agenttjänsten loggas också operativa-relaterade händelser i följande loggen: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

DC agent-tjänsten kan också logga utförliga debug-nivå spårningshändelser till följande loggen: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> Spårningsloggen är inaktiverad som standard. När aktiverat den här loggfilen tar emot en stor volym med händelser och kan påverka domänkontrollantens prestanda. Den här utökade loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD lösenord skydd proxy-tjänst

Lösenordsskydd tjänsten Proxy skickar en minimal uppsättning händelser till händelseloggen i följande: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

Lösenordsskydd Proxy-tjänsten kan också logga utförliga debug-nivå spårningshändelser till följande loggen: \Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> Spårningsloggen är inaktiverad som standard. När aktiverat den här loggen tar emot en stor volym med händelser och detta kan påverka prestanda för proxy-värden. Den här loggfilen ska därför bara aktiveras när ett problem som kräver mer ingående undersökning och sedan endast för kortast möjliga tid.

### <a name="dc-agent-discovery"></a>Identifiering av DC-Agent

Den `Get-AzureADPasswordProtectionDCAgent` cmdlet kan användas för att visa grundläggande information om de olika DC-agenter som körs i en domän eller skog. Den här informationen hämtas från serviceConnectionPoint-objektet som registrerats av körs DC agent tjänster. Ett exempel på utdata från den här cmdleten är följande:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

De olika egenskaperna uppdateras med varje DC-agenttjänsten ungefärliga timme. Data har fortfarande följer replikeringsfördröjning för Active Directory.

Omfånget för den cmdlet fråga påverkas med antingen parametrarna-skog eller -domän.

### <a name="emergency-remediation"></a>Nödfall reparation

Om en olycklig situation uppstår där DC-agenttjänsten orsakar problem, kan DC-agenttjänsten omedelbart stänga av. DLL-filen DC agent lösenord filter försöker anropa tjänsten inte körs och loggar varningshändelser (10012, 10013), men alla inkommande lösenord accepteras under den tiden. DC-Agenttjänsten kan sedan också konfigureras via Windows Service Control Manager med en starttyp ”inaktiverad” efter behov.

### <a name="performance-monitoring"></a>Prestandaövervakning

Programvaran för DC agent-tjänsten installeras en prestandaräknarobjektet med namnet **Azure AD-lösenordsskydd**. Följande perf räknare är tillgängliga:

|Perf räknarens namn | Beskrivning|
| --- | --- |
|Lösenord som bearbetats |Den här räknaren visar det totala antalet lösenord som bearbetas (godkännas eller avvisas) sedan senaste omstarten.|
|Lösenord accepteras |Den här räknaren visar det totala antalet lösenord som godkändes sedan senaste omstarten.|
|Avvisade lösenord |Den här räknaren visar det totala antalet lösenord som avvisats sedan senaste omstarten.|
|Lösenord filtrera begäranden pågår |Den här räknaren visar antalet lösenord filtrera begäranden som för närvarande pågår.|
|Belastning lösenord filtrera begäranden |Den här räknaren visar det högsta antalet samtidiga lösenord filtrera begäranden sedan den senaste omstarten.|
|Lösenord filterfel för begäran |Den här räknaren visar det totala antalet lösenord filtrera begäranden som misslyckades på grund av ett fel sedan senaste omstarten. Fel kan inträffa när Azure AD lösenord skydd DC agent-tjänsten inte körs.|
|Lösenord filtrera begäranden per sekund |Den här räknaren visar den hastighet med vilken lösenord som bearbetas.|
|Bearbetningstid för lösenord filter begäran |Den här räknaren visar Genomsnittlig tid som krävs för att bearbeta en begäran om lösenord filter.|
|Bearbetningstid för belastning lösenord filter begäran |Den här räknaren visar belastning lösenord filterbegäran bearbetningstid sedan den senaste omstarten.|
|Lösenord accepteras på grund av granskningsläge |Den här räknaren visar det totala antalet lösenord som skulle normalt har avvisats, men godkändes eftersom lösenordsprincipen har konfigurerats för att vara i granskningsläge (sedan senaste omstarten).|

## <a name="directory-services-repair-mode"></a>Reparationsläge för katalogtjänster

Om domänkontrollanten startas i reparationsläge för katalogtjänster, identifierar DC-agenttjänsten detta orsakar alla lösenordsverifieringen eller tvingande aktiviteter inaktiveras oavsett aktiva principkonfigurationen.

## <a name="domain-controller-demotion"></a>Degradering av domänkontrollanter

Det går för att degradera en domänkontrollant som fortfarande kör Agentprogrammet DC. Administratörer bör vara medveten men att Agentprogrammet DC körs och fortsätter att tillämpa principen aktuella lösenord under degraderingen proceduren. Nya lokala administratörslösenordet (anges som en del av degraderingen) verifieras som andra lösenord. Microsoft rekommenderar att väljas säkra lösenord för lokala administratörskonton som en del av en Domänkontrollant degradering procedur; valideringen av nya lokala administratörslösenordet av Agentprogrammet som domänkontrollanten kan vara störande för befintliga degradering operativa procedurer.
När degraderingen har slutförts och domänkontrollanten har startats och kör igen som en normal medlemsserver, återgår Agentprogrammet DC till körs i passivt läge. Det kan sedan avinstalleras när som helst.

## <a name="removal"></a>Borttagning

Om det är valt att avinstallera förhandsversion programvara och rensa alla relaterade tillstånd från domäner och skogar, kan den här uppgiften åstadkommas med hjälp av följande steg:

> [!IMPORTANT]
> Det är viktigt att utföra stegen i ordning. Om en instans av lösenordsskydd proxytjänst lämnas kommer körs regelbundet skapa nytt objekt för serviceConnectionPoint samt regelbundet återskapar sysvol-tillstånd.

1. Avinstallera lösenordsskydd Proxy programvara från alla datorer. Det här steget har **inte** kräver en omstart.
2. Avinstallera DC Agentprogrammet från alla domänkontrollanter. Det här steget **kräver** en omstart.
3. Ta manuellt bort alla proxy tjänstanslutningspunkter i varje domännamngivning. Platsen för de här objekten kan identifieras med följande Active Directory Powershell-kommando:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Inte utelämna en asterisk (”*”) i slutet av $keywords variabelvärdet.

   Det resulterande objekt som hittats den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tas bort manuellt. 

4. Ta bort alla anslutningspunkter för DC-agenten manuellt i varje domännamngivning. Det kan finnas en objekten per domänkontrollant i skogen, beroende på hur ofta den allmänna testversionen har distribuerats. Platsen för det objektet kan identifieras med följande Active Directory Powershell-kommando:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Det resulterande objekt som hittats den `Get-ADObject` kommando kan sedan skickas till `Remove-ADObject`, eller tas bort manuellt.

5. Ta manuellt bort skogsnivå konfigurationen. Tillståndet skog configuration underhålls i en behållare i Active Directory konfigurationsnamngivningskontexten. Det kan identifieras och tas bort enligt följande:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Manuellt bort alla sysvol-relaterade tillstånd genom att manuellt ta bort följande mapp och dess innehåll:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Om det behövs kan den här sökvägen även komma åt lokalt på en viss domänkontrollant; Standardplatsen är något som liknar följande sökväg:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Den här sökvägen skiljer sig om sysvol-resursen har konfigurerats i en icke-standardplatsen.

## <a name="next-steps"></a>Nästa steg

Mer information om globala och anpassade förbjudna lösenord listorna finns i artikeln [förbjuda felaktiga lösenord](concept-password-ban-bad.md)
