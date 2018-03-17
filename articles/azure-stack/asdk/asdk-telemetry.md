---
title: Azure Stack telemetri | Microsoft Docs
description: "Beskriver hur du konfigurerar Azure Stack telemetriinställningar med hjälp av PowerShell."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d48b6a02666348f2ef7c1b2a73982d219c79bf54
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/17/2018
---
# <a name="azure-stack-telemetry"></a>Azure Stack-telemetri

Azure Stack systemdata eller telemetri, överförs automatiskt till Microsoft via användarupplevelsen ansluten. Data som samlas in från Azure-stacken telemetri används av Microsoft-teamen främst för att förbättra vår kundupplevelser och för säkerhet, hälsa, kvalitet och prestanda analys.

Som operatör Azure Stack telemetri kan ge värdefulla insikter om enterprise-distributioner och ger dig en röst som hjälper till att formen framtida versioner av Azure-stacken.

> [!NOTE]
> Azure-stacken kan också konfigureras att vidarebefordra användningsinformation till Azure för fakturering. Detta krävs för flera noder Azure Stack-kunder som väljer lön-som-du-Använd fakturering. Användningsrapport styrs oberoende från telemetri och krävs inte för flera noder kunder som väljer modellen kapacitet eller för Azure-stacken Development Kit användare. För dessa scenarier användningsrapport kan stängas av [med hjälp av skriptet registrering](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting). 

Azure Stack telemetri baseras på komponenten anslutna användarupplevelsen för Windows Server 2016 och telemetri som använder den [ETW Event Tracing for Windows ()](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) spåra loggning teknik för att samla in och lagra telemetriska händelser och data. Azure Stack-komponenter använder samma teknik för loggning för att publicera händelser och data som har samlats in med hjälp av offentliga operativsystemet loggning och spårning API: er. Exempel på Azure-stacken komponenter är Nätverksresursprovidern, Lagringsresursprovidern, övervakning av Resursprovidern och uppdatera Resource Provider. Komponenten anslutna användarupplevelse och telemetri krypterar data med hjälp av SSL och använder certifikat fästning för att sända telemetridata över HTTPS till Microsoft Data Management-tjänsten.

> [!NOTE]
> För att stödja telemetri dataflöde, öppnas port 443 (HTTPS) i nätverket. Komponenten anslutna användarupplevelse och telemetri ansluter till Microsoft Data Management-tjänsten på https://v10.vortex-win.data.microsoft.com. Komponenten anslutna användarupplevelse och telemetri också ansluter till https://settings-win.data.microsoft.com att hämta konfigurationsinformation.

## <a name="privacy-considerations"></a>Överväganden för sekretess
ETW-service skickar telemetridata tillbaka till skyddade molnlagring. Principen om lägsta möjliga guider åtkomst till telemetridata. Endast Microsoft-personal med en giltig affärsbehov tillåts åtkomst till telemetridata. Microsoft delar inte personliga data på våra kunder med tredje part, utom gottfinnande kundens eller för begränsad beskrivs i den [sekretesspolicy för Azure-stacken](http://windows.microsoft.com/windows/preview-privacy-statement). Vi delar business rapporter med OEM-tillverkare och partners som innehåller aggregerade, avidentifierade telemetri information. Datadelning beslut görs av en intern Microsoft-teamet inklusive sekretess och juridiska data management intressenter.

Microsoft anser i och praxis minimering av information. Vi strävar efter att samla in bara den information som behövs och vi lagra den bara så länge som det behövs för att tillhandahålla en tjänst eller för analys. Information om hur Azure-stacken system och Azure-tjänster fungerar tas bort i sex månader. Sammanfattas eller sammanställda data hålls under en längre period.

Vi förstår att sekretess och säkerhet för våra kunder information är viktig. Vi har tagit en fina och omfattande metod till kundens integritet och skydd av kundinformation med Azure-stacken. IT-administratörer har kontroller för att anpassa funktioner och inställningar när som helst. Vårt arbete för genomskinlighet och förtroende är tydligt:
- Vi är öppna med kunder om vilka typer av data som vi samlar in.
- Vi har gjort företagskunder kontrollen – de kan anpassa sina egna sekretessinställningar.
- Vi placera först kundens integritet och säkerhet.
- Vi är transparenta om hur du hämtar för telemetri.
- Vi använder telemetri för att förbättra kundupplevelser.

Microsoft inte har för avsikt att samla in känslig information, till exempel kreditkortsnummer, användarnamn och lösenord, e-postadresser eller annan liknande känslig information. Om vi upptäcker att känslig information har mottagits av misstag, vi ta bort den.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exempel på hur Microsoft använder telemetridata
Telemetri spelar en viktig roll i hjälper oss att snabbt identifiera och åtgärda kritiska tillförlitlighetsproblem i våra kunder distributioner och konfigurationer. Insikter om telemetridata som vi samlar in hjälp oss att snabbt identifiera problem med tjänsterna eller maskinvarukonfigurationer. Microsofts möjlighet att hämta informationen från kunder och enheten förbättringar i ekosystemet hjälper höjer standarden för kvaliteten på våra integrerade Azure Stack-lösningar. 

Telemetri hjälper också till Microsoft för att bättre förstå hur kunder distribuera komponenter, använda funktioner och använda tjänster för att uppnå affärsmål. Hämtar insikter från informationen prioritera hjälper dig att tekniska investeringar i områden som kan direkt påverka våra kunder upplevelser och arbetsbelastningar.

Några exempel är kundens användning av behållare, lagring och nätverkskonfigurationer som är associerade med Azure Stack-roller. Vi använder också insikter om enheten förbättringar och information om några av våra hantering och övervakning av lösningar. Detta hjälper kunder att diagnosticera kvalitetsproblem och spara pengar genom att göra färre stöd anrop till Microsoft.

## <a name="manage-telemetry-collection"></a>Hantera telemetri samling
Vi rekommenderar inte att inaktivera telemetri i din organisation som telemetri innehåller data som driver förbättrad funktionalitet och stabilitet. Vi känner dock att i vissa situationer kan det vara nödvändigt. 

I så fall måste konfigurera du nivån telemetri som skickas till Microsoft genom att använda registret inställningar före distributionen eller med hjälp av telemetri slutpunkter efter distributionen.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Ange telemetri i Windows-registret
Registereditorn används för att manuellt ange nivån telemetri för den fysiska värddatorn innan du distribuerar Azure stacken. Om en princip för hantering redan finns, till exempel en grupprincip åsidosätter den här registerinställningen. 

Starta i CloudBuilder.vhdx och kör följande skript i ett upphöjt PowerShell-fönster innan du distribuerar Azure Stack på development kit värden:

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host 
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Telemetri nivåerna är kumulativa och kategoriserade i fyra nivåer (0-3):

**0 (security)**. Säkerhetsdata. Information som krävs för att hålla operativsystemet säker, inklusive data om anslutna användarupplevelse och telemetri inställningar och Windows Defender. Ingen specifik telemetri för Azure-stacken genereras på den här nivån.

**1 (grundläggande)**. Säkerhetsdata och grundläggande hälso-och kvalitet. Grundläggande enhetsinformation, inklusive: quality-relaterad data, app-kompatibilitet, appanvändningsdata och data från säkerhetsnivån. Anger din telemetri nivån till grundläggande aktiverar Azure Stack telemetri. Information som samlats in på den här nivån omfattar:

- **Grundläggande enhetsinformation** som ger en förståelse om typer och konfigurationer av interna och virtualiserade Windows Server 2016-instanser i ekosystemet, inklusive:
 - Datorn attribut, till exempel OEM, modell 
 - Nätverk attribut, till exempel antal och hastighet för nätverkskort
 - Processor och minne attribut, till exempel antalet kärnor, minnesstorlek, 
 - Storage-attribut, till exempel antalet enheter, typ och storlek.
- **Telemetri funktioner**, inklusive procent av överförda händelser, ignorerade händelser och senaste överför tid.
- **Quality-relaterad information** som hjälper Microsoft att utveckla en grundläggande förståelse av hur Azure-stacken utförs. Ett exempel är antalet kritiska aviseringar på en viss maskinvarukonfiguration.
- ** Kompatibilitet data, vilket ger insikt om vilka Resursproviders är installerade på en dator och virtuell dator och identifierar potentiella problem med programkompatibilitet.

**2 (förbättrad)**. Ytterligare information, inklusive: hur operativsystemet och andra Azure-Stack-tjänster används, hur de fungerar, avancerad tillförlitlighetsdata och data från både den grundläggande och säkerhetsnivåer. 

**3 (fullständig)**. Alla data som behövs för att identifiera och hjälpa till att lösa problem, plus data från den **säkerhet**, **grundläggande**, och **utökad** nivåer.

> [!NOTE]
> Standardvärdet för telemetri nivå är 2 (förbättrad).

Inaktivera telemetri för Windows och Azure-stacken inaktiverar SQL telemetri. För ytterligare information om effekterna av telemetriinställningar för Windows Server, referera till den [Windows telemetri Whitepaper](https://aka.ms/winservtelemetry). 

> [!IMPORTANT]
> Dessa nivåer telemetri tillämpas endast på Microsoft Azure-stacken komponenter. Icke-Microsoft-programvarukomponenter och tjänster som körs i maskinvara livscykel värden från Azure-stacken maskinvarupartners kan kommunicera med sina molntjänster utanför dessa telemetri nivåer. Du ska arbeta med din Azure-stacken maskinvaruleverantör att förstå sin princip telemetri och hur du kan anmäla eller avanmäla. 

### <a name="enable-or-disable-telemetry-after-deployment"></a>Aktivera eller inaktivera telemetri efter distribution

Om du vill aktivera eller inaktivera telemetri efter distributionen, som du behöver ha åtkomst till detta Privilegierade slutpunkt (program) som är exponerad på ERCS virtuella datorer.
1.  Så här aktiverar du: `Set-Telemetry -Enable`
2.  Inaktivera: `Set-Telemetry -Disable`

PARAMETERN detaljer: 
> . PARAMETERN aktivera – aktivera telemetri dataöverföringen 

> . PARAMETERN inaktivera - inaktivera telemetri dataöverföringen  

**Skript för att aktivera telemetri:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Skript för att inaktivera telemetri:**
```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Nästa steg
[Lägga till ett marketplace-objekt](asdk-marketplace-item.md)

