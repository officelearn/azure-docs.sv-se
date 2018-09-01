---
title: Verifieringsrapport för Azure Stack | Microsoft Docs
description: Använda rapporten Azure Stack-beredskap för installation och granska verifieringsresultatet från.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 06b5660a9428e98d2e99b5d447a05700968ec884
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381921"
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
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

Utdata liknar följande bild:

![Visa rapport](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Visa sammanfattning av rapporten
Om du vill visa en sammanfattning av rapporten, kan du lägga till den **-sammanfattning av** växla till slutet av PowerShell-kommandoraden. Exempel: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Sammanfattningen visar verifieringar som inte har resultat och anger lyckat eller misslyckat för verifieringar har slutförts. Utdata liknar följande bild:

![rapport-sammanfattning](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Visa en filtrerad rapport
Du kan visa en rapport som har filtrerats på en enda typ av verifiering den **- ReportSections** parameter med något av följande värden:
- Certifikat
- AzureRegistration
- AzureIdentity
- Jobb   
- Alla  

Till exempel om du vill visa rapporten Sammanfattning för certifikat, Använd följande PowerShell-kommandorad: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Se också
[Start-AzsReadinessChecker cmdlet-referens](azure-stack-azsreadiness-cmdlet.md)
