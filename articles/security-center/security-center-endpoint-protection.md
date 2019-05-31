---
title: Endpoint protection lösningar identifierings- och bedömning i Azure Security Center | Microsoft Docs
description: Så identifieras och har identifierat som felfria endpoint protection-lösningar.
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
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247971"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint protection-utvärdering och rekommendationer i Azure Security Center

Endpoint protection-utvärdering och rekommendationer i Azure Security Center identifierar och tillhandahåller hälsotillstånd bedömning av [stöds](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) versioner av slutpunktsskyddslösningar. Det här avsnittet beskrivs de scenarier som genererar följande två rekommendationer för slutpunktsskyddslösningar av Azure Security Center.

* **Installera endpoint protection-lösningar på den virtuella datorn**
* **Lösa hälsoproblem för endpoint protection på datorer**

## <a name="windows-defender"></a>Windows Defender

* Den **”installera endpoint protection-lösningar på den virtuella datorn”** rekommendation skapas när [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) körs och resultatet är **AMServiceEnabled: False**

* Den **”Lös hälsoproblem för endpoint protection på dina datorer”** rekommendation skapas när [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) körs och en eller båda av följande inträffar:

  * Minst en av följande egenskaper är false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Om en eller båda av följande egenskaper är större eller lika med 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center endpoint protection

* Den **”installera endpoint protection-lösningar på den virtuella datorn”** rekommendation skapas när du importerar **SCEPMpModule (”$env: Program\microsoft Security Client\MpProvider\MpProvider.psd1”)** och köra **Get-MProtComputerStatus** resulterar med **AMServiceEnabled = false**

* Den **”Lös hälsoproblem för endpoint protection på dina datorer”** rekommendation skapas när **Get-MprotComputerStatus** körs och en eller båda av följande inträffar:

    * Minst en av följande egenskaper är false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Om en eller båda av följande Signaturuppdateringar är större eller lika med 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Den **”installera endpoint protection-lösningar på den virtuella datorn”** rekommendation genereras om en eller flera av följande kontroller inte uppfylls:
    * **HKLM:\SOFTWARE\TrendMicro\Deep säkerhetsagenten** finns
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** finns
    * Den **dsq_query.cmd** filen finns i installationsmappen
    * Kör **dsa_query.cmd** resulterar med **Component.AM.mode: på - Trend Micro Deep Security Agent upptäckte**

## <a name="symantec-endpoint-protection"></a>Symantec endpoint protection
Den **”installera endpoint protection-lösningar på den virtuella datorn”** rekommendation genereras om någon av följande kontroller inte uppfylls:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Eller

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = ”Symantec Endpoint Protection”**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Den **”Lös hälsoproblem för endpoint protection på dina datorer”** rekommendation genereras om någon av följande kontroller inte uppfylls:  

* Kontrollera Symantec Version > = 12:  Registernyckel: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Kontrollera status för realtidsskydd: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Kontrollera status för signaturuppdatering: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 days**

* Kontrollera status för fullständig sökning: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Hitta signaturen versionsnumret sökväg till signaturversion för Symantec-12: **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Sökväg till signaturversion för Symantec 14: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Registry Paths:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection för Windows

Den **”installera endpoint protection-lösningar på den virtuella datorn”** rekommendation genereras om följande kontroller inte uppfylls:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** finns

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Den **”Lös hälsoproblem för endpoint protection på dina datorer”** rekommendation genereras om följande kontroller inte uppfylls:

* McAfee Version: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion > = 10**

* Hitta Signaturversion: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Hitta signaturen datum: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Hitta datum för Maskinvarugenomsökning: **HKLM:\Software\McAfee\Endpoint\AV\ODS-värdet ”LastFullScanOdsRunTime” > = 7 dagar**

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Microsoft Antimalware loggar finns på:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (eller PaaSAntimalware)\1.5.5.x (version #) \CommandExecution.log**

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Eller du kan arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
