---
title: Inbyggd i Azure Security Center med PowerShell
description: Det här dokumentet går igenom processen för introduktion av Azure Security Center med PowerShell-cmdletar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: 5aaaf539c07a7ba2c2463d5bfd1f452853f52379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603683"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Automatisera introduktion av Azure Security Center med PowerShell

Du kan skydda dina Azure-arbetsbelastningar programmässigt med hjälp av Azure Security Center PowerShell-modulen.
Med PowerShell kan du automatisera uppgifter och undvika det mänskliga felet i manuella uppgifter. Detta är särskilt användbart i storskaliga distributioner som involverar dussintals prenumerationer med hundratals och tusentals resurser – som alla måste säkras från början.

Med Introduktion till Azure Security Center med PowerShell kan du programmera inbyggt på- och medföljande hantering av dina Azure-resurser och lägga till nödvändiga säkerhetskontroller.

Den här artikeln innehåller ett exempel på PowerShell-skript som kan ändras och användas i din miljö för att distribuera Security Center över dina prenumerationer. 

I det här exemplet aktiverar vi Security Center på en prenumeration med ID: d07c0080-170c-4c24-861d-9c817742786c och tillämpar de rekommenderade inställningarna som ger en hög skyddsnivå genom att implementera standardnivån för Security Center, som tillhandahåller avancerade funktioner för skydd och upptäckt av hot:

1. Ange [standardskyddsnivån för Säkerhetscenter](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Ange arbetsytan Log Analytics som Microsoft Monitoring Agent ska skicka de data som samlas in på de virtuella datorer som är associerade med prenumerationen – i det här exemplet en befintlig användardefinierad arbetsyta (myWorkspace).

3. Aktivera Security Centers automatiska agentetablering som [distribuerar Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Ange organisationens [CISO som säkerhetskontakt för Säkerhetscentervarningar och anmärkningsvärda händelser](security-center-provide-security-contact-details.md).

6. Tilldela Säkerhetscenters [standardsäkerhetsprinciper](tutorial-security-policy.md).

## <a name="prerequisites"></a>Krav

De här stegen bör utföras innan du kör cmdlets för Säkerhetscenter:

1.  Kör PowerShell som administratör.
2.  Kör följande kommandon i PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Inbyggd security center med PowerShell

1.  Registrera dina prenumerationer på Security Center Resource Provider:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Valfritt: Ange täckningsnivån (prisnivån) för prenumerationerna (Om den inte har definierats är prisnivån ledig):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Konfigurera en Log Analytics-arbetsyta som agenterna ska rapportera till. Du måste ha en Log Analytics-arbetsyta som du redan har skapat, som prenumerationens virtuella datorer rapporterar till. Du kan definiera flera prenumerationer som ska rapporteras till samma arbetsyta. Om den inte har definierats används standardarbetsytan.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Installation av Microsoft Monitoring Agent automatiskt på dina virtuella Azure-datorer:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Vi rekommenderar att du aktiverar automatisk etablering för att se till att dina virtuella Azure-datorer skyddas automatiskt av Azure Security Center.
    >

5.  Valfritt: Vi rekommenderar starkt att du definierar säkerhetskontaktinformation för de prenumerationer du har ombord, som ska användas som mottagare av aviseringar och meddelanden som genereras av Security Center:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Tilldela standardinitiativet Security Center-princip:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Nu har du installerat Azure Security Center med PowerShell!

Du kan nu använda dessa PowerShell-cmdlets med automatiseringsskript för att programmässigt iterera över prenumerationer och resurser. Detta sparar tid och minskar risken för mänskliga fel. Du kan använda det här [exempelskriptet](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) som referens.






## <a name="see-also"></a>Se även
Mer information om hur du kan använda PowerShell för att automatisera introduktionen till Security Center finns i följande artikel:

* [Az.Security](https://docs.microsoft.com/powershell/module/az.security).

Mer information om Security Center finns i följande artikel:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Läs om hur du hanterar och svarar på säkerhetsaviseringar.