---
title: Azure Stack-telemetri | Microsoft Docs
description: Beskriver hur du konfigurerar inställningar för Azure Stack-telemetri med PowerShell.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 25a832be432f1d1a6d9b18dfc8f7384c564c3060
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103442"
---
# <a name="azure-stack-telemetry"></a>Azure Stack-telemetri

Azure Stack-system, data eller telemetri, överförs automatiskt till Microsoft via användarupplevelsen är ansluten. Data som samlas in från Azure Stack telemetri används av Microsoft-team främst för att förbättra vår kundupplevelser och för säkerhet, hälsa, kvalitet och prestanda analys.

Azure Stack-operatör, telemetri kan ge värdefulla insikter om enterprise-distributioner och ger dig en röst som hjälper till att forma framtida versioner av Azure Stack.

> [!NOTE]
> Azure Stack kan också konfigureras att vidarebefordra användningsinformation till Azure för fakturering. Detta krävs för flera noder Azure Stack-kunder som väljer att betala som du-användning fakturering. Användningsrapport styrs oberoende från telemetri och krävs inte för flera noder kunder som väljer att modellen kapacitet eller Azure Stack Development Kit användare. Dessa scenarier kan användningsrapportering kan stängas av [med hjälp av registrering skriptet](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure Stack-telemetri baseras på den anslutna användarupplevelsen för Windows Server 2016 och telemetri komponenten, som använder den [för Windows ETW (Event Tracing)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) spåra loggning-teknik för att samla in och lagra telemetrihändelser och data. Azure Stack-komponenter använder samma teknik som loggning för att publicera händelser och data som samlats in genom att använda offentliga operativsystemet händelseloggning och spårning av API: er. Exempel på Azure Stack-komponenterna är Provider för nätverksresurser, Lagringsresursprovidern, övervakning av Resursprovidern och Update-Resursprovidern. Komponenten ansluten användarupplevelse och telemetri krypterar data med hjälp av SSL och använder certifikat fästa för att överföra dessa data via HTTPS till Microsoft Data Management-tjänsten.

> [!NOTE]
> För att stödja telemetridataflöde, måste port 443 (HTTPS) vara öppna i nätverket. Komponenten ansluten användarupplevelse och telemetri som ansluter till Microsoft Data Management-tjänsten på https://v10.vortex-win.data.microsoft.com. Komponenten ansluten användarupplevelse och telemetri som också ansluter till https://settings-win.data.microsoft.com att hämta konfigurationsinformation.

## <a name="privacy-considerations"></a>Överväganden för sekretess
ETW-tjänsten skickar telemetridata tillbaka till skyddade molnlagring. Principen om lägsta möjliga guider åtkomst till dessa data. Endast Microsoft-personal med ett giltigt affärsbehov tillåts åtkomst till dessa data. Microsoft delar inte personliga data för våra kunder med tredje part, förutom kundens eget godtycke eller för de begränsa syften som beskrivs i den [sekretesspolicyn för Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Vi delar företag rapporter med OEM-tillverkare och partner som innehåller aggregerad, avidentifierade telemetriinformation. Datadelning beslut görs av en intern Microsoft-teamet, inklusive sekretess, juridik och hantering av intressenter.

Microsoft arbetar för och praxis minimering av information. Vi strävar efter att samla in bara den information som vi behöver, och vi lagrar det bara så länge det behövs för att tillhandahålla en tjänst eller för analys. Information om hur Azure Stack-system och Azure-tjänster fungerar tas bort inom sex månader. Sammanfattas eller aggregerade data sparas under en längre period.

Vi förstår att integriteten och säkerheten för våra kunders information är viktig. Vi har tagit en genomtänkt och omfattande metod för kundens integritet och skyddet av kundernas data med Azure Stack. IT-administratörer har kontroller för att anpassa funktionerna och sekretessinställningar när som helst. Vårt åtagande att transparens och förtroende är tydligt:
- Vi är öppna med kunder om vilka typer av data som vi samlar in.
- Låter vi företagskunder kontroll – de kan anpassa sina egna sekretessinställningar.
- Vi placera först kundens integritet och säkerhet.
- Vi kan transparent om hur du hämtar för telemetri.
- Vi använder telemetri för att förbättra kundupplevelsen.

Microsoft inte har för avsikt att samla in känslig information, till exempel kreditkortsnummer, användarnamn och lösenord, e-postadresser eller annan på samma sätt känslig information. Om vi fastställer att känslig information har mottagits av misstag, vi ta bort den.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exempel på hur Microsoft använder dessa data
Telemetri spelar en viktig roll i vår strävan att snabbt identifiera och lösa viktiga tillförlitlighetsproblem i våra kunders distributioner och konfigurationer. Insikter om telemetridata som vi samlar in hjälper oss att snabbt identifiera problem med tjänster eller maskinvarukonfigurationer. Microsofts förmåga att få dessa data från kunder och driva fram förbättringar i ekosystemet hjälper höjer ribban inom kvaliteten på våra integrerade Azure Stack-lösningar.

Telemetri hjälper också till Microsoft för att bättre förstå hur kunder distribuera komponenter, använda funktioner och använder tjänster för att uppnå sina affärsmål. Få insikter från dessa data prioritera hjälper dig att teknikinvesteringar inom områden som kan direkt påverka kundernas upplevelse och arbetsbelastningar.

Några exempel är kundens användning av behållare, lagring och nätverkskonfigurationer som är associerade med Azure Stack-roller. Vi använder också insikter för att driva fram förbättringar och insikt några av våra hantering och övervakning av lösningar. Detta hjälper kunder att diagnostisera kvalitetsproblem och spara pengar genom att göra färre support-anrop till Microsoft.

## <a name="manage-telemetry-collection"></a>Hantera telemetriinsamling
Vi rekommenderar inte att inaktivera telemetri i din organisation som telemetri tillhandahåller data som driver förbättrad produktens funktioner och stabilitet. Vi känner dock att i vissa situationer kan det vara nödvändigt.

I så fall kan du konfigurera telemetrinivån som skickas till Microsoft genom att använda registret före distribution eller med hjälp av telemetri slutpunkter efter distribution.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Ange telemetrinivån i Windows-registret
Windows-Registereditorn används för att manuellt ange hur telemetri på den fysiska värddatorn innan du distribuerar Azure Stack. Om en princip för hantering redan finns, till exempel en grupprincip åsidosätter den här registerinställningen.

Innan du distribuerar Azure Stack på development kit värden, startar i CloudBuilder.vhdx och kör följande skript i ett upphöjt PowerShell-fönster:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

Telemetri-nivåerna är kumulativa och kategoriserade i fyra nivåer (0 – 3):

**0 (säkerhet)**. Säkerhetsdata. Information som krävs för att hålla operativsystemet säker, inklusive data om anslutna användarupplevelse och telemetri inställningar och Windows Defender. Inga specifika Azure Stack-telemetri genereras på den här nivån.

**1 (grundläggande)**. Säkerhetsdata och grundläggande hälso-och kvalitet. Grundläggande information, inklusive: kvalitet-relaterad data, app-kompatibilitet, appanvändningsdata och data från säkerhetsnivån. Om du anger din telemetrinivån till grundläggande aktiverar Azure Stack-telemetri. Information som samlats in på den här nivån omfattar:

- **Grundläggande enhetsinformation** som ger en förståelse om typer och konfigurationer av interna och virtualiserade Windows Server 2016-instanser i ekosystemet, inklusive:
  - Datorn attribut, till exempel OEM-tillverkare, modell
  - Nätverk attribut, till exempel antal och hastighet för nätverkskort,
  - Processor och minne attribut, till exempel hur många kärnor, minnesstorlek,
  - Storage-attribut, till exempel hur många enheter, typ och storlek.
- **Telemetri funktioner**, inklusive procent av överförda händelser, avbrutna händelser och senaste Uppladdningstid.
- **Kvalitet-relaterad information** som hjälper Microsoft att utveckla en grundläggande förståelse för hur Azure Stack fungerar. Ett exempel är antalet kritiska aviseringar på en viss maskinvarukonfiguration.
- **Data om programkompatibilitet**, som hjälper dig att ge insikt om vilka Resursproviders som är installerade på en dator och virtuell dator och identifierar potentiella kompatibilitetsproblem.

**2 (utökad)**. Ytterligare information, inklusive: hur operativsystemet och andra Azure Stack-tjänster används, hur de fungerar, avancerade tillförlitlighetsdata och data från både Basic-och säkerhet.

**3 (fullständig)**. Alla data som behövs för att identifiera och bidra till att lösa problem, plus data från den **Security**, **grundläggande**, och **utökad** nivåer.

> [!NOTE]
> Telemetri på standardvärdet är 2 (utökad).

Om du inaktiverar Windows-och Azure Stack inaktiverar SQL telemetri. Mer information om effekterna av Windows Server-telemetriinställningar refererar till den [Windows-telemetri White Paper](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Dessa nivåer för telemetri gäller endast för Microsoft Azure Stack-komponenter. Icke-Microsoft-programvarukomponenter och tjänster som körs i maskinvara livscykel värden från Azure Stack-maskinvarupartner kan kommunicera med sina molntjänster utanför dessa telemetri-nivåer. Du bör fungera med Azure Stack-lösningen maskinvaruleverantören att förstå deras telemetri-principen och hur du kan anmäla eller avanmäla dig.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Aktivera eller inaktivera telemetri efter distributionen

Om du vill aktivera eller inaktivera telemetri efter distributionen, som du behöver ha åtkomst till detta privilegierad slutpunkt (program) som är exponerad på ERCS virtuella datorer.
1.  För att aktivera: `Set-Telemetry -Enable`
2.  Inaktivera: `Set-Telemetry -Disable`

PARAMETERN detalj:
> . PARAMETERN Enable - aktivera telemetri ladda upp data
> 
> . PARAMETERN Disable - inaktivera telemetri ladda upp data  

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
[Starta och stoppa ASDK](asdk-start-stop.md)
