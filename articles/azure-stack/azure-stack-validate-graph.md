---
title: Verifiera Azure Graph-integrering för Azure Stack
description: Använda Azure Stack-beredskap layout för att verifiera graph-integrering för Azure Stack.
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
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 0755f9d60bee8a57f9259a51cf54e8cda566175e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247025"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Verifiera graph-integrering för Azure Stack

Använd verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) för att verifiera att din miljö är redo för graph-integrering med Azure Stack. Verifiera graph-integrering innan du börjar datacenter-integrering eller innan en Azure Stack-distribution.

Validerar beredskap för installation:

* Autentiseringsuppgifterna för tjänstkontot som skapats för graph-integrering har rätt behörigheter att fråga Active Directory.
* Den *global katalog* kan lösas och är beröras.
* KDC kan lösas och är beröras.
* Nödvändiga nätverksanslutningen är på plats.

Läs mer om Azure Stack datacenter-integrering, [Azure Stack datacenter-integrering - identitet](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Hämta verktyget för diagnostisk för installation

Ladda ned den senaste versionen av verktyget Azure Stack-beredskap för installation (AzsReadinessChecker) från den [PowerShell-galleriet](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara på plats.

**Den dator där verktyget körs:**

* Windows 10 eller Windows Server 2016 med domänanslutning.
* PowerShell 5.1 eller senare. Kör följande PowerShell-kommando för att kontrollera vilken version och granska de *större* version och *mindre* versioner:  
   > `$PSVersionTable.PSVersion`
* Active Directory PowerShell-modulen.
* Senaste versionen av den [Microsoft Azure Stack-beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.

**Active Directory-miljö:**

* Identifiera användarnamnet och lösenordet för ett konto för graph-tjänsten i den befintliga Active Directory-instansen.
* Identifiera Active Directory-skogens rot FQDN.

## <a name="validate-the-graph-service"></a>Verifiera graph-tjänsten

1. Öppna en administrativ PowerShell-kommandotolk och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Från PowerShell-Kommandotolken kör du följande kommando för att ange den *$graphCredential* variabeln till graph-kontot. Ersätt `contoso\graphservice` med ditt konto med hjälp av den `domain\username` format.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. Kör följande kommando för att starta valideringen för graph-tjänsten från PowerShell-Kommandotolken. Ange värdet för **- ForestFQDN** som det fullständiga Domännamnet för skogsroten.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Granska utdata när verktyget körs. Kontrollera att statusen är OK för integreringskraven för diagrammet. En lyckad validering liknar följande exempel:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

I produktionsmiljöer, Testa nätverksanslutningen från en operatör arbetsstation är inte fullständigt indikation på anslutningen som är tillgängliga för Azure Stack. Azure Stack-stämpel offentliga VIP-nätverk ha anslutning för LDAP-trafik att utföra identitetsintegrering.

## <a name="report-and-log-file"></a>Rapporten och loggfilen

Varje tidsverifiering körs, loggas resultaten till **AzsReadinessChecker.log** och **AzsReadinessCheckerReport.json**. Platsen för filerna visas med verifieringsresultat i PowerShell.

Verifiering-filer kan hjälpa dig att dela status innan du distribuerar Azure Stack eller undersöka problem med verifieringen. Båda filerna spara resultatet av varje efterföljande valideringskontrollen. Rapporten ger din distribution team bekräftelse av identity-konfigurationen. Loggfilen kan hjälpa din distribution eller support-teamet undersöka verifieringsproblem.

Som standard skrivs båda filerna till `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Användning:

* **-OutputPath**: Den *sökväg* parameter i slutet av kommandot Kör för att ange en annan plats.
* **-CleanReport**: Parametern i slutet av kommandot Kör för att rensa *AzsReadinessCheckerReport.json* av föregående rapportinformation. Mer information finns i [Azure Stack verifieringsrapport](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Verifieringsfel

Om en verifieringskontroll misslyckas, visas information om felet i PowerShell-fönstret. Verktyget också loggar information till *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Nästa steg

[Visa rapport om distributionsberedskap](azure-stack-validation-report.md)  
[Överväganden för allmänna Azure Stack-integrering](azure-stack-datacenter-integration.md)  
