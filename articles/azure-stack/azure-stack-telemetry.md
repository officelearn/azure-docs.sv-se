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
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: 471232fee5245426afce89993d9faebccc6a8ea8
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389385"
---
# <a name="azure-stack-telemetry"></a>Azure Stack-telemetri

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Azure Stack-telemetri överför systemdata automatiskt till Microsoft via användarupplevelsen är ansluten. Microsoft teams använda de data som Azure Stack-telemetri som samlar in för att förbättra kundupplevelsen. Dessa data används också för säkerhet, hälsa, kvalitet och prestandaanalys.

För Azure Stack-operatör, telemetri kan ge värdefulla insikter om enterprise-distributioner och ger dig en röst som hjälper till att forma framtida versioner av Azure Stack.

> [!NOTE]
> Du kan också konfigurera Azure Stack för att vidarebefordra användningsinformation till Azure för fakturering. Detta krävs för flera noder Azure Stack-kunder som väljer att betala som du-användning fakturering. Användningsrapport styrs oberoende från telemetri och krävs inte för flera noder kunder som väljer att modellen kapacitet eller Azure Stack Development Kit användare. Dessa scenarier kan användningsrapportering kan stängas av [med hjälp av registrering skriptet](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure Stack-telemetri baseras på den anslutna användarupplevelsen för Windows Server 2016 och telemetri komponenten, som använder den [för Windows ETW (Event Tracing)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging-teknik för att samla in och lagra händelser och data. Azure Stack-komponenter använder samma teknik för att publicera händelser och data som samlas in genom att använda offentliga operativsystemet händelseloggning och spårning av API: er. Exempel på de här Azure Stack-komponenterna är dessa providers: Nätverks-resurs, lagringsresurs, övervakning av resursen och att uppdatera resursen. Komponenten ansluten användarupplevelse och telemetri krypterar data med hjälp av SSL och använder certifikat fästa kan överföra data via HTTPS till Microsoft Data Management-tjänsten.

> [!IMPORTANT]
> Om du vill aktivera telemetridataflöde måste port 443 (HTTPS) vara öppna i nätverket. Komponenten ansluten användarupplevelse och telemetri som ansluter till Microsoft Data Management-tjänsten på https://v10.vortex-win.data.microsoft.com. Komponenten ansluten användarupplevelse och telemetri som också ansluter till https://settings-win.data.microsoft.com att hämta konfigurationsinformation.

## <a name="privacy-considerations"></a>Överväganden för sekretess

ETW-tjänsten skickar telemetridata tillbaka till skyddade molnlagring. Huvudnamn om lägsta behörighet får åtkomst till dessa data. Endast Microsoft-personal med ett giltigt affärsbehov får åtkomst till dessa data. Microsoft inte dela personliga kundinformation till tredje part, förutom kundens eget godtycke eller för de begränsa syften som beskrivs i den [Microsoft Privacy Statement](https://privacy.microsoft.com/PrivacyStatement). Företag rapporter som delas med OEM-tillverkare och partners omfatta aggregerade, avidentifierade data. Datadelning beslut görs av en intern Microsoft-teamet, inklusive sekretess, juridik och hantering av intressenter.

Microsoft arbetar för och praxis minimering av information. Vi strävar efter att samla in den information som behövs och lagra den för endast så länge som behövs för att tillhandahålla en tjänst eller för analys. Information om hur Azure Stack-system och Azure-tjänster fungerar tas bort inom sex månader. Sammanfattas eller sammanställda data sparas för en längre period.

Vi förstår att sekretess och säkerhet med kundinformation är viktig.  Microsoft använder en genomtänkt och omfattande metod för kundens integritet och skyddet av kunddata i Azure Stack. IT-administratörer har kontroller för att anpassa funktionerna och sekretessinställningar när som helst. Vårt åtagande att transparens och förtroende är tydligt:

- Vi kör öppet med kunder om vilka typer av data som vi samlar in.
- Låter vi företagskunder kontroll – de kan anpassa sina egna sekretessinställningar.
- Vi placera först kundens integritet och säkerhet.
- Vi transparent om hur dessa data används.
- Vi använder dessa data för att förbättra kundupplevelsen.

Microsoft avser inte att samla in känsliga data, till exempel kreditkortsnummer, användarnamn och lösenord, e-postadresser eller liknande känslig information. Om vi fastställer att känslig information har mottagits av misstag, vi ta bort den.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exempel på hur Microsoft använder dessa data

Telemetri spelar en viktig roll i att hjälpa att snabbt identifiera och lösa viktiga tillförlitlighetsproblem i kunddistributioner och konfigurationer. Insikter från dessa data kan hjälpa dig att identifiera problem med tjänster eller maskinvarukonfigurationer. Microsofts förmåga att få dessa data från kunder och förbättringar av enheten för ekosystemet höjer ribban inom kvaliteten på integrerade Azure Stack-lösningar.

Telemetri hjälper också till Microsoft för att bättre förstå hur kunder distribuera komponenter, använda funktioner och använder tjänster för att uppnå sina affärsmål. Dessa insikter hjälper att prioritera teknikinvesteringar inom områden som kan direkt påverka kundupplevelse och arbetsbelastningar.

Några exempel är kundens användning av behållare, lagring och nätverkskonfigurationer som är associerade med Azure Stack-roller. Vi använder också insikter för att driva fram förbättringar och information i Azure Stack-hantering och övervakning av lösningar. Dessa förbättringar gör det enklare för kunderna att diagnostisera problem och spara pengar genom att göra färre support-anrop till Microsoft.

## <a name="manage-telemetry-collection"></a>Hantera telemetriinsamling

Vi inte rekommenderade inaktivera telemetri i din organisation. I vissa situationer kan det dock vara nödvändigt.

I dessa scenarier kan konfigurera du telemetrinivån som skickas till Microsoft med registerinställningar innan du distribuerar Azure Stack eller med hjälp av telemetri-slutpunkter när du har distribuerat Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Telemetri nivåer och datainsamling

Innan du ändrar telemetriinställningar, bör du förstå telemetri-nivåer och vilka data som samlas in på varje nivå.

Telemetriinställningar för är grupperade i fyra nivåer (0 – 3) som är kumulativa och kategoriserade som som följer:

**0 (säkerhet)**</br>
Säkerhetsdata. Information som krävs för att skydda operativsystemet. Detta inkluderar data om anslutna användarupplevelse och telemetri inställningar och Windows Defender. Ingen telemetri som är specifika för Azure Stack genereras på den här nivån.

**1 (grundläggande)**</br>
Säkerhetsdata och grundläggande hälso-och kvalitet. Grundläggande information, inklusive: kvalitet-relaterad data, app-kompatibilitet, appanvändningsdata, och data från den **Security** nivå. Om du anger din telemetrinivån till grundläggande aktiverar Azure Stack-telemetri. Information som samlats in på den här nivån omfattar:

- *Grundläggande enhetsinformation* som ger en förståelse om typer och konfigurationer av inbyggda och virtuella Windows Server 2016-instanser i ekosystemet. Det här omfattar:

  - Dator-attribut, till exempel OEM-tillverkare och modell.
  - Nätverk-attribut, till exempel antalet nätverkskort och deras hastighet.
  - Processor och minne attribut, till exempel antalet kärnor och mängden minne som är installerade.
  - Storage-attribut, till exempel hur många enheter, typ av enhet och diskens storlek.

- *Telemetri funktioner*, inklusive procentandelen överförda händelser, avbrutna händelser och de senaste data Uppladdningstid.
- *Kvalitet-relaterad information* som hjälper Microsoft att utveckla en grundläggande förståelse för hur Azure Stack fungerar. Till exempel: antal kritiska aviseringar på en viss maskinvarukonfiguration.
- *Data om programkompatibilitet* som ger insikt om vilka Resursproviders som är installerade på ett system och en virtuell dator. Detta identifierar potentiella kompatibilitetsproblem.

**2 (utökat)**</br>
Ytterligare information, inklusive: hur operativsystemet och Azure Stack-tjänster används, hur du utför de här tjänsterna, avancerade tillförlitlighet data och data från den **Security** och **grundläggande** nivåer.

> [!NOTE]
> Det här är standardinställningen för telemetri.

**3 (fullständig)**</br>
Alla data som behövs för att identifiera och bidra till att lösa problem, plus data från den **Security**, **grundläggande**, och **utökad** nivåer.

> [!IMPORTANT]
> Dessa nivåer för telemetri gäller endast för Microsoft Azure Stack-komponenter. Icke-Microsoft-programvarukomponenter och tjänster som körs i maskinvara livscykel värden från Azure Stack-maskinvarupartner kan kommunicera med sina molntjänster utanför dessa telemetri-nivåer. Du bör fungera med Azure Stack-lösningen maskinvaruleverantören att förstå deras telemetri-principen och hur du kan anmäla eller avanmäla dig.

Om du inaktiverar Windows-och Azure Stack inaktiverar även SQL-telemetri. Mer information om konsekvenserna av att telemetriinställningar för Windows Server finns i den [Windows-telemetri White Paper](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: ställa in telemetri i Windows-registret

Du kan använda Windows-Registereditorn för att manuellt ange telemetrinivån på den fysiska värddatorn innan du distribuerar Azure Stack. Om en princip för hantering redan finns, till exempel en grupprincip åsidosätter den här registerinställningen.

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

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK och flera noder: aktivera eller inaktivera telemetri efter distributionen

Om du vill aktivera eller inaktivera telemetri efter distributionen, som du behöver ha åtkomst till detta privilegierad slutpunkt (program) som är exponerad på ERCS virtuella datorer.

1. För att aktivera: `Set-Telemetry -Enable`
2. Inaktivera: `Set-Telemetry -Disable`

Parameterinformation:
> . PARAMETERN Enable - aktivera telemetri ladda upp data</br>
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

[Registrera Azure Stack med Azure](azure-stack-registration.md)