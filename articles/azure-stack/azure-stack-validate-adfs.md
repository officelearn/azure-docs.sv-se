---
title: Verifiera AD FS-integrering för Azure Stack
description: Använda Azure Stack-beredskap layout för att verifiera AD FS-integrering för Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: patricka
ms.reviewer: jerskine
ms.openlocfilehash: 0aa13f2fced9122163d5278b5bb50cc1e11a0379
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947348"
---
# <a name="validate-adfs-integration-for-azure-stack"></a>Verifiera AD FS-integrering för Azure Stack

Använd verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) för att verifiera att din miljö är redo för AD FS-integrering med Azure Stack. Innan du börjar center dataintegrering eller före ett Azure Stack-distributionen bör du kontrollera ADFS-integrering.

Validerar beredskap för installation:

* Den *federationsmetadata* innehåller giltig XML-elementen för federation.
* Den *ADFS SSL-certifikat* kan hämtas och förtroendekedja kan skapas. AD FS måste lita kedja för SSL-certifikat på stämpel. Certifikatet måste signeras av samma *certifikatutfärdare* som Azure Stack-distributionscertifikat eller av en partner för betrodda utfärdare. En fullständig lista över betrodda utfärdare partner finns: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca
* *AD FS-certifikat för tokensignering* är betrodda och inte snart upphör att gälla.

Läs mer om integrering av Azure Stack data center [Azure Stack datacenter-integrering - identitet](azure-stack-integrate-identity.md)

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget för diagnostisk för installation

Ladda ned den senaste versionen av verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) från den [PSGallery](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara på plats.

**Den dator där verktyget körs:**

* Windows 10 eller Windows Server 2016 med domänanslutning.
* PowerShell 5.1 eller senare. Om du vill kontrollera vilken version du kör följande PowerShell-cmd och granska de *större* version och *mindre* versioner:  
   > `$PSVersionTable.PSVersion`
* Den senaste versionen av den [Microsoft Azure Stack-beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.

**Active Directory Federation Services-miljö:**

Du behöver minst en av följande typer av metadata:

* URL: en för AD FS-federationsmetadata. Till exempel, `https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`
* Federation metadata XML-fil. Till exempel FederationMetadata.xml

## <a name="validate-adfs-integration"></a>Verifiera AD FS-integrering

1. Öppna en administrativ PowerShell-kommandotolk och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven.

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Från PowerShell-Kommandotolken kör du följande om du vill starta verifieringen. Ange värdet för **- CustomADFSFederationMetadataEndpointUri** som URI för federationsmetadata:

     `Invoke-AzsADFSValidation -CustomADFSFederationMetadataEndpointUri https://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

1. Granska utdata när verktyget körs. Bekräfta att statusen är OK för integreringskraven för AD FS. En lyckad validering visas liknar följande.

    ```
    Invoke-AzsADFSValidation v1.1809.1001.1 started.

    Testing ADFS Endpoint https://sts.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml

            Read Metadata:                         OK
            Test Metadata Elements:                OK
            Test SSL ADFS Certificate:             OK
            Test Certificate Chain:                OK
            Test Certificate Expiry:               OK

    Details:
    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.
    Additional help URL: https://aka.ms/AzsADFSIntegration

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsADFSValidation Completed
    ```

I produktionsmiljöer, testa certifikatkedjor förtroendekedja från en arbetsstation för operatörer kan inte betraktas som fullständigt indikation på efterlevnadsstatus för PKI-förtroende i Azure Stack-infrastruktur. Azure Stack-stämpel offentliga VIP-nätverk ha anslutning till listan över återkallade certifikat för PKI-infrastruktur.

## <a name="report-and-log-file"></a>Rapporten och loggfilen

Varje tidsverifiering körs, loggas resultaten till **AzsReadinessChecker.log** och **AzsReadinessCheckerReport.json**. Platsen för filerna visas med verifieringsresultat i PowerShell.

Verifiering-filer kan hjälpa dig att dela status innan du distribuerar Azure Stack eller undersöka problem med verifieringen. Båda filerna spara resultatet av varje efterföljande valideringskontrollen. Rapporten innehåller din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem.

Som standard skrivs båda filerna till `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Användning:

* **-OutputPath** *sökväg* parameter i slutet av kör kommandoraden för att ange en annan plats.
* **-CleanReport** parameter i slutet av kommandot Kör för att rensa *AzsReadinessCheckerReport.json* av föregående rapportinformation. Mer information finns i [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel

Om en verifieringskontroll misslyckas, visas information om felet i PowerShell-fönstret. Verktyget också loggar information till den *AzsReadinessChecker.log*.

I följande exempel ger vägledning om vanliga verifieringsfel.

### <a name="command-not-found"></a>Det gick inte att hitta kommandot

`Invoke-AzsADFSValidation : The term 'Invoke-AzsADFSValidation' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.`

**Orsak** -PowerShell Autoload kunde inte läsa in modulen beredskap för installation på rätt sätt.

**Lösning** – importera beredskap för installation modulen uttryckligen. Kopiera och klistra in koden nedan i PowerShell och uppdatera den \<version\> med versionsnumret för den installerade versionen.

`Import-Module "c:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\<version>\Microsoft.AzureStack.ReadinessChecker.psd1" -Force`

## <a name="next-steps"></a>Nästa steg

[Visa rapport om distributionsberedskap](azure-stack-validation-report.md)  
[Överväganden för allmänna Azure Stack-integrering](azure-stack-datacenter-integration.md)  
