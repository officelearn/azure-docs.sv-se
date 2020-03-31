---
title: Skyddsrekommendationer för slutpunkter i Azure Security Centers
description: Hur slutpunktsskyddslösningarna upptäcks och identifieras som felfria.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208550"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Utvärdering och rekommendationer för slutpunktsskydd i Azure Security Center

Azure Security Center tillhandahåller hälsobedömningar av versioner av slutpunktsskydd som [stöds.](security-center-services.md#endpoint-supported) I den här artikeln beskrivs de scenarier som leder Security Center för att generera följande två rekommendationer:

* **Installera slutpunktsskyddslösningar på din virtuella dator**
* **Lösa hälsoproblem för slutpunktsskydd på dina datorer**

## <a name="windows-defender"></a>Windows Defender

* Security Center rekommenderar att du **"Installera slutpunktsskyddslösningar på virtuell dator"** när [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) körs och resultatet är **AMServiceEnabled: False**

* Security Center rekommenderar att du **"Lös hälsoproblem för slutpunktsskydd på dina datorer"** när [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) körs och något av följande inträffar:

  * Någon av följande egenskaper är falska:

    **AMServiceEnabled**

    **AntispywareEnabled**

    **RealTimeProtectionEnabled**

    **BeteendeÖvervakadEnbar**

    **IoavProtectionEnabled**

    **OnAccessProtectionEnabled**

  * Om en eller båda av följande egenskaper är 7 eller fler.

    **AntispywareSignatureAge**

    **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Slutpunktsskydd för Microsoft System Center

* Security Center rekommenderar att du **"Installera slutpunktsskyddslösningar på virtuell dator"** när du importerar **SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** och kör **Get-MProtComputerStatus-resultat** med **AMServiceEnabled = false**

* Security Center rekommenderar att du **"Lös hälsoproblem för slutpunktsskydd på dina datorer"** när **Get-MprotComputerStatus** körs och något av följande inträffar:

    * Minst en av följande egenskaper är falsk:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Om en eller båda av följande signaturuppdateringar är större eller lika med 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center rekommenderar att du **"Installera slutpunktsskyddslösningar på den virtuella datorn"** när någon av följande kontroller inte uppfylls:
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** finns
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** finns
    * **Filen dsa_query.cmd** finns i installationsmappen
    * Kör **dsa_query.cmd-resultat** med **Component.AM.mode: på - Trend Micro Deep Security Agent har upptäckts**

## <a name="symantec-endpoint-protection"></a>Symantec slutpunktsskydd
Security Center rekommenderar att du **"Installera slutpunktsskyddslösningar på den virtuella datorn"** när någon av följande kontroller inte uppfylls:

* **HKLM:\Software\Symantec\Symantec Slutpunktsskydd\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Slutpunktsskydd\CurrentVersion\public-opstate\ASRunningStatus = 1**

Eller

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Slutpunktsskydd\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Slutpunktsskydd\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center rekommenderar att du **"Löser hälsoproblem för slutpunktsskydd på dina datorer"** när någon av följande kontroller inte uppfylls:

* Kontrollera Symantec Version >= 12: Registerplats: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Värde "PRODUCTVERSION"**

* Kontrollera realtidsskyddsstatus: **HKLM:\Software\Wow6432Node\Symantec\Symantec Slutpunktsskydd\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Kontrollera status för signaturuppdatering: STATUS FÖR **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dagar**

* Kontrollera fullständig genomsökningsstatus: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dagar**

* Sök efter signaturversionsnummer Sökväg till signaturversion för Symantec 12: **Registry Paths+ "CurrentVersion\SharedDefs" -Värde "SRTSP"** 

* Sökväg till signaturversion för Symantec 14: **Registersökvägar+ "CurrentVersion\SharedDefs\SDSDefs" -Värde "SRTSP"**

Registersökvägar:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee-slutpunktsskydd för Windows

Security Center rekommenderar att du **"Installera slutpunktsskyddslösningar på den virtuella datorn"** när någon av följande kontroller inte uppfylls:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** finns

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center rekommenderar att du **"Löser hälsoproblem för slutpunktsskydd på dina datorer"** när någon av följande kontroller inte uppfylls:

* McAfee-version: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Hitta signaturversion: **HKLM:\Software\McAfee\AVSolution\DS\DS -Värde "dwContentMajorVersion"**

* Hitta signaturdatum: **HKLM:\Software\McAfee\AVSolution\DS\DS -Värde "szContentCreationDate" >= 7 dagar**

* Sök söksdatum: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Värde "LastFullScanOdsRunTime" >= 7 dagar**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Slutpunkt Säkerhet för Linux Hot Prevention 

Security Center rekommenderar att du **"Installera slutpunktsskyddslösningar på den virtuella datorn"** när någon av följande kontroller inte uppfylls:

- Fil **/opt/isec/ens/threatprevention/bin/isecav** avslutas 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"-utdata** är: **McAfee-namn = McAfee Endpoint Security for Linux Threat Prevention and McAfee version >= 10**

Security Center rekommenderar att du **"Löser hälsoproblem för slutpunktsskydd på dina datorer"** när någon av följande kontroller inte uppfylls:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** returnerar **Snabbsökning, Fullständig genomsökning** och båda skanningarna <= 7 dagar

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** returnerar **DAT och motor Uppdateringstid** och båda <= 7 dagar

- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary"** returnerar **åtkomstskanningsstatus**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus för Linux 

Security Center rekommenderar att du **"Installera slutpunktsskyddslösningar på den virtuella datorn"** när någon av följande kontroller inte uppfylls:

- Fil **/opt/sophos-av/bin/savdstatus** avslutas eller sök efter anpassad plats **"readlink $(which savscan)"**

- **"/opt/sophos-av/bin/savdstatus --version"** returnerar Sophos namn = **Sophos Anti-Virus och Sophos version >= 9**

Security Center rekommenderar att du **"Löser hälsoproblem för slutpunktsskydd på dina datorer"** när någon av följande kontroller inte uppfylls:

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Schemalagd genomsökning . \* avslutad" | svans -1"**, returnerar ett värde

- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan finished"** | svans -1", returnerar ett värde

- **"/opt/sophos-av/bin/savdstatus --lastupdate"** returnerar sistUppdate, vilket bör vara <= 7 dagar 

- **"/opt/sophos-av/bin/savdstatus -v"** är lika med **"On-access scanning is running"** 

- **"/opt/sophos-av/bin/savconfig get LiveProtection"** returnerar aktiverat

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Microsoft Antimalware-tilläggsloggar finns på: **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Eller PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Support

Om du vill ha mer hjälp kontaktar du Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Eller lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).