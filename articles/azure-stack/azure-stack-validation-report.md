---
title: Verifieringsrapport för Azure Stack | Microsoft Docs
description: Använda rapporten Azure Stack-beredskap för installation och granska verifieringsresultatet från.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/23/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 1b2b06c02dc54c4369dd8490b714d1444d4b3b01
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986193"
---
# <a name="azure-stack-validation-report"></a>Azure Stack-verifieringsrapport
Verktyget Azure Stack-beredskap för installation används för att köra verifieringar som har stöd för distribution och underhåll av Azure Stack-miljön. Verktyget skriver resultatet till en JSON-fil för rapporten. Rapporten visar detaljerad och sammanfattade data om tillståndet för krav för distribution av Azure Stack. Rapporten visar även information om hemligheter Rotation för befintliga Azure Stack-distributioner.  

 ## <a name="where-to-find-the-report"></a>Var du hittar i rapporten
När verktyget körs loggas resultaten till **AzsReadinessCheckerReport.json**. Verktyget skapar även en logg med namnet **AzsReadinessChecker.log**. Platsen för filerna visas med verifieringsresultat i PowerShell.

![Kör-verifiering](./media/azure-stack-validation-report/validation.png)

Båda filerna sparas resultaten av efterföljande verifieringskontroller när det körs på samma dator.  Exempelvis kan du köra verktyget för att verifiera certifikat, kör igen för att verifiera Azure identity och sedan en tredje tid för att verifiera registrering. Resultatet av alla tre verifieringar är tillgängliga i den resulterande .json-rapporten.  

Som standard båda filerna skrivs till *C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan plats.   
- Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information från *AzsReadinessCheckerReport.json*. Om tidigare körningar av verktyget.

## <a name="view-the-report"></a>Läs rapporten
Om du vill visa rapporten i PowerShell, ange sökvägen till rapporten som ett värde för **- ReportPath**. Det här kommandot visar innehållet i rapporten och identifierar valideringar som ännu inte har resultat.

Till exempel om du vill visa rapporten i en PowerShell-Kommandotolken som är öppen för den plats där rapporten finns, kör du: 
   > `Read-AzsReadinessReport -ReportPath .\AzsReadinessReport.json` 

Utdata liknar följande:

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

Certificate Validation results not available.

############### Registration Validation Summary ###############

Azure Registration Validation results not available.

############### Azure Identity Results ###############

Test                          : ServiceAdministrator
Result                        : OK
AAD Service Admin             : admin@contoso.onmicrosoft.com
Azure Environment             : AzureCloud
Azure Active Directory Tenant : contoso.onmicrosoft.com
Error Details                 : 

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.

############### AzsReadiness Job Summary ###############

Index             : 0
Operations        : 
StartTime         : 2018/10/22 14:24:16
EndTime           : 2018/10/22 14:24:19
Duration          : 3
PSBoundParameters : 
````

## <a name="view-the-report-summary"></a>Visa sammanfattning av rapporten
Om du vill visa en sammanfattning av rapporten, kan du lägga till den **-sammanfattning av** växla till slutet av PowerShell-kommandoraden. Exempel: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -summary`  

Sammanfattningen visar verifieringar som inte har resultat och anger lyckat eller misslyckat för verifieringar har slutförts. Utdata liknar följande:

````PowerShell
Reading All Validation(s) from Report C:\Contoso-AzsReadinessCheckerReport.json

############### Certificate Validation Summary ###############

    Certificate Validation found no errors or warnings.
    
############### Registration Validation Summary ###############

    Registration Validation found no errors or warnings.

############### Azure Identity Validation Summary ###############

    Azure Identity Validation found no errors or warnings.

############### Azure Stack Graph Validation Summary ###############

Azure Stack Graph Validation results not available.

############### Azure Stack ADFS Validation Summary ###############

Azure Stack ADFS Validation results not available.
````


## <a name="view-a-filtered-report"></a>Visa en filtrerad rapport
Du kan visa en rapport som har filtrerats på en enda typ av verifiering den **- ReportSections** parameter med något av följande värden:
- Certifikat
- AzureRegistration
- AzureIdentity
- Graph
- ADFS
- Jobb   
- Alla  

Till exempel om du vill visa rapporten Sammanfattning för certifikat, Använd följande PowerShell-kommandorad: 
 > `Read-AzsReadinessReport -ReportPath .\Contoso-AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Se också
