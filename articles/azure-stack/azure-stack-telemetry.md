---
title: Azure Stack telemetri | Microsoft Docs
description: Beskriver hur du konfigurerar Azure Stack telemetriinställningar med hjälp av PowerShell.
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248205"
---
# <a name="azure-stack-telemetry"></a>Azure Stack-telemetri

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure Stack telemetri Överför automatiskt systemdata till Microsoft via användarupplevelsen ansluten. Microsoft-teamen använda data som Azure Stack telemetri samlar in för att förbättra kundupplevelser. Dessa data används också för säkerhet, hälsa, kvaliteten och analys av minnesprestanda.

För en Azure-Stack-operator telemetri kan ge värdefulla insikter om enterprise-distributioner och ger dig en röst som hjälper till att formen framtida versioner av Azure-stacken.

> [!NOTE]
> Du kan också konfigurera Azure-Stack för att vidarebefordra information om användning till Azure för fakturering. Detta krävs för flera noder Azure Stack-kunder som väljer lön-som-du-Använd fakturering. Användningsrapport styrs oberoende från telemetri och krävs inte för flera noder kunder som väljer modellen kapacitet eller för Azure-stacken Development Kit användare. För dessa scenarier användningsrapport kan stängas av [med hjälp av skriptet registrering](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting).

Azure Stack telemetri baseras på komponenten anslutna användarupplevelsen för Windows Server 2016 och telemetri som använder den [ETW Event Tracing for Windows ()](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging teknik för att samla in och lagra händelser och data. Azure Stack-komponenter använder samma teknik för att publicera händelser och data som samlas in med hjälp av offentliga operativsystemet loggning och spårning API: er. Exempel på dessa Azure Stack-komponenter är dessa providers: nätverksresurs, lagring, övervakning av resursen och Update-resurs. Komponenten anslutna användarupplevelse och telemetri krypterar data med hjälp av SSL och använder certifikat fästning kan överföra data via HTTPS till Microsoft Data Management-tjänsten.

> [!IMPORTANT]
> Om du vill aktivera telemetri dataflöde öppnas port 443 (HTTPS) i nätverket. Komponenten anslutna användarupplevelse och telemetri ansluter till Microsoft Data Management-tjänsten på https://v10.vortex-win.data.microsoft.com. Komponenten anslutna användarupplevelse och telemetri också ansluter till https://settings-win.data.microsoft.com att hämta konfigurationsinformation.

## <a name="privacy-considerations"></a>Överväganden för sekretess

ETW-service skickar telemetridata tillbaka till skyddade molnlagring. Principen om minsta behörighet får åtkomst till telemetridata. Endast Microsoft-personal med en giltig affärsbehov får åtkomst till telemetridata. Microsoft inte dela personlig kundinformation med tredje part, utom kundens gottfinnande och i begränsade syfte som beskrivs i den [sekretesspolicy för Microsoft](https://privacy.microsoft.com/PrivacyStatement). Företag rapporter som delas med OEM-tillverkare och partners omfattar aggregerade, anonymiserade data. Datadelning beslut görs av en intern Microsoft-teamet inklusive sekretess och juridiska data management intressenter.

Microsoft anser i och praxis minimering av information. Vi strävar efter att samla den information som behövs och spara den för endast så länge som behövs för att tillhandahålla en tjänst eller för analys. Information om hur Azure-stacken system och Azure-tjänster fungerar tas bort i sex månader. Sammanfattas eller sammanställda data sparas för en längre period.

Vi förstår att sekretess och säkerhet kundinformation är viktig.  Microsoft använder en fina och omfattande metod till kundens integritet och skydd av kundinformation i Azure-stacken. IT-administratörer har kontroller för att anpassa funktioner och inställningar när som helst. Vårt arbete för genomskinlighet och förtroende är tydligt:

- Vi kan öppna med kunder om vilka typer av data som vi samlar in.
- Vi har gjort företagskunder kontrollen – de kan anpassa sina egna sekretessinställningar.
- Vi placera först kundens integritet och säkerhet.
- Vi kan transparent om hur du hämtar för telemetridata.
- Vi använder telemetridata för att förbättra kundupplevelser.

Microsoft inte har för avsikt att samla in känsliga data, till exempel kreditkortsnummer, användarnamn och lösenord, e-postadresser eller liknande känslig information. Om vi upptäcker att känslig information har mottagits av misstag, vi ta bort den.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exempel på hur Microsoft använder telemetridata

Telemetri spelar en viktig roll i vilket hjälper dig att snabbt identifiera och åtgärda kritiska tillförlitlighetsproblem i kunddistributioner och konfigurationer. Insikter från telemetridata kan hjälpa dig att identifiera problem med tjänsterna eller maskinvarukonfigurationer. Microsofts möjlighet att hämta informationen från kunder och förbättringar av enheten till ekosystemet, genererar i fältet för kvaliteten på integrerad Azure Stack-lösningar.

Telemetri hjälper också till Microsoft för att bättre förstå hur kunder distribuera komponenter, använda funktioner och använda tjänster för att uppnå affärsmål. Dessa insikter att prioritera tekniska investeringar i områden som kan direkt påverka kundupplevelser och arbetsbelastningar.

Några exempel är kundens användning av behållare, lagring och nätverkskonfigurationer som är associerade med Azure Stack-roller. Vi använder också insikter om enheten förbättringar och information om Azure-stacken hantering och övervakning av lösningar. Dessa förbättringar gör det enklare att diagnostisera problem och spara pengar genom att göra färre stöd anrop till Microsoft.

## <a name="manage-telemetry-collection"></a>Hantera telemetri samling

Vi inte rekommenderade inaktivera telemetri i din organisation. I vissa fall kan det dock vara nödvändigt.

I så fall kan konfigurera du nivån telemetri som skickas till Microsoft med registerinställningar innan du distribuerar Azure Stack eller genom att använda telemetri slutpunkter när du har distribuerat Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Telemetri nivåer och datainsamling

Innan du ändrar telemetri, bör du förstå nivåerna telemetri och vilka data som samlas in på varje nivå.

Telemetriinställningar för grupperas i fyra nivåer (0-3) som är kumulativa och kategoriserade som du följande:

**0 (security)**</br>
Säkerhetsdata. Information som krävs för att skydda operativsystemet. Detta omfattar information om den anslutna användarupplevelse och telemetri inställningar och Windows Defender. Inga telemetri som är specifika för Azure-stacken genereras på den här nivån.

**1 (grundläggande)**</br>
Säkerhetsdata och grundläggande hälso-och kvalitet. Grundläggande enhetsinformation, inklusive: quality-relaterad data, app-kompatibilitet, appanvändningsdata och data från den **säkerhet** nivå. Anger din telemetri nivån till grundläggande aktiverar Azure Stack telemetri. Information som samlats in på den här nivån omfattar:

- *Grundläggande enhetsinformation* som ger en förståelse om typer och konfigurationer av inbyggd och virtuella Windows Server 2016-instanser i ekosystemet. Det här omfattar:

  - Datorn attribut, till exempel OEM-tillverkare och modell.
  - Nätverk attribut, till exempel antalet nätverkskort och deras hastighet.
  - Processor och minne attribut, till exempel antalet kärnor och mängden minne som är installerade.
  - Lagringsattribut, till exempel hur många enheter, typ av enhet och enhetsstorlek.

- *Telemetri funktioner*, inklusive procentandelen av överförda händelser, ignorerade händelser och de senaste data överför tid.
- *Quality-relaterad information* som hjälper Microsoft att utveckla en grundläggande förståelse av hur Azure-stacken utförs. Till exempel antal kritiska aviseringar på en viss maskinvarukonfiguration.
- *Kompatibilitetsinformation* att ger insikt om vilka Resursproviders är installerade på en dator och en virtuell dator. Detta identifierar potentiella problem med programkompatibilitet.

**2 (Avancerat)**</br>
Ytterligare information, inklusive: hur operativsystemet och Azure-stacken tjänster används, hur utför dessa tjänster, avancerad tillförlitlighet data och data från den **säkerhet** och **grundläggande** nivåer.

> [!NOTE]
> Det här är standardinställningen för telemetri.

**3 (fullständig)**</br>
Alla data som behövs för att identifiera och hjälpa till att lösa problem, plus data från den **säkerhet**, **grundläggande**, och **utökad** nivåer.

> [!IMPORTANT]
> Dessa nivåer telemetri tillämpas endast på Microsoft Azure-stacken komponenter. Icke-Microsoft-programvarukomponenter och tjänster som körs i maskinvara livscykel värden från Azure-stacken maskinvarupartners kan kommunicera med sina molntjänster utanför dessa telemetri nivåer. Du ska arbeta med din Azure-stacken maskinvaruleverantör att förstå sin princip telemetri och hur du kan anmäla eller avanmäla.

Inaktivera telemetri för Windows och Azure-stacken inaktiverar även SQL telemetri. Mer information om effekterna av telemetriinställningar för Windows Server finns i [Windows telemetri Whitepaper](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: ställa in telemetri i Windows-registret

Du kan använda Registereditorn för att manuellt ange nivån telemetri för den fysiska värddatorn innan du distribuerar Azure Stack. Om en princip för hantering redan finns, till exempel en grupprincip åsidosätter den här registerinställningen.

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

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK och flera noder: aktivera eller inaktivera telemetri efter distribution

Om du vill aktivera eller inaktivera telemetri efter distributionen, som du behöver ha åtkomst till detta Privilegierade slutpunkt (program) som är exponerad på ERCS virtuella datorer.

1. Så här aktiverar du: `Set-Telemetry -Enable`
2. Inaktivera: `Set-Telemetry -Disable`

Parameterinformation:
> . PARAMETERN aktivera – aktivera telemetri dataöverföringen</br>
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

- [Hämta Azure Stack development kit distributionspaketet](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [Distribuera Azure-stacken development kit](azure-stack-run-powershell-script.md)
