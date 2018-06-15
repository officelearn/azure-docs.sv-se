---
title: Verifieringsrapport för Azure-Stack | Microsoft Docs
description: Rapporten Azure Stack beredskap layout och granska verifieringsresultatet.
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
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937822"
---
# <a name="azure-stack-validation-report"></a>Azure Stack verifieringsrapport
Verktyget Azure Stack beredskap layout körs verifieringar som har stöd för distribution och underhåll av en Azure-Stack-miljö. Verktyget skriver valideringsresultat till en JSON-fil i rapporten. Rapporten visar detaljerad och sammanfattade data på tillståndet för krav för distribution av Azure-stacken och hemligheter Rotation för befintliga distributioner i Azure-stacken.  

 ## <a name="where-to-find-the-report"></a>Här hittar du i rapporten
När verktyget körs loggas resultaten till **AzsReadinessCheckerReport.json**. Verktyget skapar även en logg med namnet **AzsReadinessChecker.log**. Platsen för filerna visas med verifieringsresultat i PowerShell.

![Kör-validering](./media/azure-stack-validation-report/validation.png)

Båda filerna sparas resultaten av efterföljande kontroller när det körs på samma dator.  Exempelvis kan du köra verktyget för att verifiera certifikat, kör igen för att bekräfta Azure identitet och sedan en tredje tid för att verifiera registrering. Resultatet av alla tre verifieringar är tillgängliga i den resulterande JSON-rapporten.  

Som standard skrivs båda filerna till *C:\Users\<användarnamn > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Använd den **- OutputPath** ***&lt;sökväg&gt;*** parameter i slutet av kör kommandoraden för att ange en annan rapport-plats.   
- Använd den **- CleanReport** parameter i slutet av kommandot Kör för att ta bort information från *AzsReadinessCheckerReport.json*. Om föregående körningar av verktyget.

## <a name="view-the-report"></a>Läs rapporten
Om du vill visa rapporten i PowerShell, ange sökvägen till rapporten som ett värde för **- ReportPath**. Det här kommandot visar innehållet i rapporten och identifierar också verifieringar som ännu inte har resultat.

Till exempel om du vill visa rapporten i ett PowerShell-Kommandotolken öppen till platsen där rapporten finns, kör du: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

Utdata liknar följande bild:

![Visa rapport](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Visa rapporten Sammanfattning
Om du vill visa en sammanfattning av rapporten, kan du lägga till den **-sammanfattning** växla till slutet av PowerShell-kommandoraden. Exempel: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Sammanfattningen visar verifieringar som inte har resultat och anger lyckat eller misslyckat för verifieringar har slutförts. Utdata liknar följande bild:

![rapport – sammanfattning](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Visa en filtrerad rapport
Om du vill visa en rapport filtreras i en enda typ av verifiering av **- ReportSections** parametern och ange något av följande värden som motsvarar den verifieringstypen som du vill visa:
- Certifikat
- AzureRegistration
- AzureIdentity
- Jobb   
- Alla  

Till exempel om du vill visa rapporten Sammanfattning för certifikat, Använd följande PowerShell-kommandorad: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Se också
[Start-AzsReadinessChecker cmdlet-referens](azure-stack-azsreadiness-cmdlet.md)
