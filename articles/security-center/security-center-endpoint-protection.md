---
title: Slut punkts skydds lösningar identifiering och hälso utvärdering i Azure Security Center | Microsoft Docs
description: Hur lösningar för slut punkts skydd identifieras och identifieras som felfria.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: 4d3fc90a722b9f4043e891a14b542e6b90c94c55
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881039"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint Protection-utvärdering och rekommendationer i Azure Security Center

Endpoint Protection-utvärdering och rekommendationer i Azure Security Center identifierar och tillhandahåller hälso utvärdering av versioner av slut punkts skydds lösningar som [stöds](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) . I det här avsnittet beskrivs scenarier som genererar följande två rekommendationer för Endpoint Protection-lösningar med Azure Security Center.

* **Installera Endpoint Protection-lösningar på den virtuella datorn**
* **Lös problem med hälso tillstånd för slut punkts skydd på dina datorer**

## <a name="windows-defender"></a>Windows Defender

* Rekommendationen **"Installera Endpoint Protection-lösningar på virtuell dator"** genereras när [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) körs och resultatet är **AMServiceEnabled: !**

* Rekommendationen **"Lös problem med Endpoint Protection-hälso problemen på dina datorer"** genereras när [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) körs, eller båda av följande inträffar:

  * Minst en av följande egenskaper är falsk:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Om en eller båda av följande egenskaper är större än eller lika med 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Rekommendationen **"Installera Endpoint Protection-lösningar på virtuell dator"** skapas när du importerar **SCEPMpModule ("$env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** och kör  **Get-MProtComputerStatus-** resultat med **AMServiceEnabled = false**

* Rekommendationen **"Lös problem med Endpoint Protection-hälso problemen på dina datorer"** genereras när **Get-MprotComputerStatus** körs, eller båda av följande inträffar:

    * Minst en av följande egenskaper är falsk:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Om en eller båda av följande uppdateringar av signaturen är större än eller lika med 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Rekommendationen **"Installera Endpoint Protection-lösningar på virtuell dator"** skapas om en eller flera av följande kontroller inte är uppfyllda:
    * **HKLM: \ SOFTWARE\TrendMicro\Deep säkerhets agent** finns
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** finns
    * Filen **dsq_query. cmd** finns i installationsmappen
    * Kör **dsa_query. cmd** -resultat med **komponent. am. mode: analys av Micro-djup säkerhets agent** har identifierats

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Rekommendationen **"Installera Endpoint Protection-lösningar på virtuell dator"** skapas om någon av följande kontroller inte är uppfyllda:

* **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Eller

* **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Rekommendationen **"Lös problem med slut punkts skydd på dator"** skapas om någon av följande kontroller inte är uppfyllda:  

* Kontrol lera Symantec-versionen > = 12:  Register plats: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Kontrol lera status för real tids skydd: **HKLM: \ Software\Wow6432Node\Symantec\Symantec-slutpunkt Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**

* Kontrol lera status för uppdatering av signatur: **HKLM\Software\Symantec\Symantec-slutpunkt Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 dagar**

* Kontrol lera status för fullständig sökning: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Sök efter signatur version nummer sökväg till signatur version för Symantec 12: **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Sökväg till Signature version för Symantec 14: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Register Sök vägar:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection för Windows

Rekommendationen **"Installera Endpoint Protection-lösningar på virtuell dator"** skapas om följande kontroller inte är uppfyllda:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** finns

* **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Rekommendationen **"Lös problem med slut punkts skydd på** datorerna" skapas om följande kontroller inte är uppfyllda:

* McAfee-version: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Sök efter signatur version: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Sök efter datum för signatur: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" > = 7 dagar**

* Sök efter genomsöknings datum: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" > = 7 dagar**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Säkerhet för McAfee-slutpunkten för Linux hot förebyggande 

**Installations slut punkts skydds lösningarna på den virtuella datorns** rekommendation skapas om en eller båda av följande kontroller inte är uppfyllda:  

- Filen **/opt/ISEC/ens/threatprevention/bin/isecav** avslutas 

- **"/opt/ISEC/ens/threatprevention/bin/isecav--version"-** utdata är: **McAfee-namn = McAfees slut punkts säkerhet för Linux Threat förebygga och McAfee-version > = 10**

**Lösnings problemen för slut punkts skydd på din dators** rekommendation skapas om en eller flera av följande kontroller inte är uppfyllda:

- **"/opt/ISEC/ens/threatprevention/bin/isecav--listtask"** returnerar **snabb sökning, fullständig genomsökning** och båda genomsökningarna < = 7 dagar

- **"/opt/ISEC/ens/threatprevention/bin/isecav--listtask"** returnerar **dat-och motor uppdaterings tid** och båda < = 7 dagar

- **"/opt/ISEC/ens/threatprevention/bin/isecav--getoasconfig--Summary"** returnerar status **för åtkomst skanning**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus för Linux 

**Installations slut punkts skydds lösningarna på den virtuella datorns** rekommendation skapas om en eller båda av följande kontroller inte är uppfyllda:

- Filen **/opt/Sophos-av/bin/savdstatus** avslutas eller söker efter den anpassade platsen **"Readlink $ (som savscan)"**

- **"/opt/Sophos-av/bin/savdstatus--version"** returnerar Sophos-namn = **Sophos Anti-virus och sophos-version > = 9**

**Lösnings problemen för slut punkts skydd på din dators** rekommendation skapas om en eller flera av följande kontroller inte är uppfyllda:

- **"/opt/Sophos-av/bin/savlog--MaxAge = 7 | grep-i "schemalagd sökning. \* slutfört "| svan-1"** , returnerar ett värde   

- **"/opt/Sophos-av/bin/savlog--MaxAge = 7 | grep "skanningen är färdig"** | pilslut-1 ", returnerar ett värde   

- **"/opt/Sophos-av/bin/savdstatus--lastupdate"** returnerar lastupdate som ska vara < = 7 dagar 

- **"/opt/Sophos-av/bin/savdstatus-v"** är lika med **"Genomsökning vid åtkomst körs"** 

- **"/opt/Sophos-av/bin/savconfig get LiveProtection"** returnerar aktiverat  

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Microsofts tilläggs loggar för program mot skadlig kod finns på:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (eller PaaSAntimalware) \1.5.5.x (version #) \CommandExecution.log**

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
