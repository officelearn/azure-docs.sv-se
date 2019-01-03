---
title: Använd PowerShell för att publicera Azure Security Center och skydda ditt nätverk | Microsoft Docs
description: Det här dokumentet vägleder dig genom processen att onboarding Azure Security Center med hjälp av PowerShell-cmdletar.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2018
ms.author: rkarlin
ms.openlocfilehash: 69310e51495cbb91303c3e8837ad42f6a4ac3374
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972916"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatisera onboarding av Azure Security Center med hjälp av PowerShell

Du kan skydda dina Azure-arbetsbelastningar programmässigt, med hjälp av Azure Security Center PowerShell-modulen.
Med hjälp av PowerShell kan du automatisera uppgifter och undvika mänskliga fel i manuella uppgifter. Detta är särskilt användbart i storskaliga distributioner som omfattar flera prenumerationer med hundratals och tusentals resurser – som måste skyddas från början.

Registrering Azure Security Center med hjälp av PowerShell kan du automatisera onboarding och hanteringen av dina Azure-resurser och lägga till de nödvändiga säkerhetskontrollerna programmässigt.

Den här artikeln innehåller ett PowerShell-skript som kan ändras och används i din miljö för att lansera Security Center för dina prenumerationer. 

I det här exemplet ska vi aktivera Security Center på en prenumeration med ID: d07c0080-170c-4c24-861d-9c817742786c och tillämpar de rekommenderade inställningarna som ger en hög nivå av skydd, genom att implementera standardnivån i Security Center, som tillhandahåller Avancerat skydd och identifiering av funktioner:

1. Ange den [ASC standard skyddsnivå](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Ställa in Log Analytics-arbetsytan till vilken Microsoft Monitoring Agent skickar data som samlas in på de virtuella datorerna som är associerade med prenumerationen – i det här exemplet är en befintlig användardefinierade arbetsyta (myWorkspace).

3. Aktivera Security Center agenten för automatisk etablering som [distribuerar Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Ange organisationens [IT-chef som security-kontakt för ASC aviseringar och viktiga händelser](security-center-provide-security-contact-details.md).

6. Tilldela Security Center [standard säkerhetsprinciper](tutorial-security-policy.md).

## <a name="prerequisites"></a>Förutsättningar

De här stegen bör utföras innan du kör cmdlet: ar för Security Center:

1.  Kör PowerShell som administratör.
2.  Kör följande kommandon i PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Integrera Security Center med hjälp av PowerShell

1.  Registrera dina prenumerationer till Security Center Resource Provider:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Valfritt: Ställa in täckning (prisnivå) prenumerationer (om inte har definierats, prisnivån har angetts till ledig):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Konfigurera en Log Analytics-arbetsyta som agenterna rapporterar. Du måste ha en Log Analytics-arbetsyta som du redan skapat att prenumerationens virtuella datorer rapporterar till. Du kan definiera flera prenumerationer för att rapportera till samma arbetsyta. Om inte har definierats, används standardarbetsytan.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Automatiskt etablera installationen av Microsoft Monitoring Agent på virtuella datorer i Azure:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Vi rekommenderar att aktivera automatisk etablering att se till att virtuella datorer i Azure skyddas automatiskt av Azure Security Center.
    >

5.  Valfritt: Vi rekommenderar starkt att du definierar säkerhetskontaktuppgifter för prenumerationer som du publicerar, som används som mottagare av meddelanden och meddelanden som genereras av Security Center:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertsAdmin -NotifyOnAlert 

6.  Tilldela standard Security Center policy initiativ:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Du nu har integrerats Azure Security Center med PowerShell!

Du kan nu använda dessa PowerShell-cmdletar med automatiserade skript för att programmässigt iterera över prenumerationerna och resurserna. Detta sparar tid och minskar risken för mänskliga fel. Du kan använda det här [exempel på skript](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) som referens.






## <a name="see-also"></a>Se också
Mer information om hur du kan använda PowerShell för att automatisera Kom igång med Security Center finns i följande artikel:

* [Az.Security](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Security/Commands.Security/help/Az.Security.md).

Mer information om Security Center finns i följande artikel:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
