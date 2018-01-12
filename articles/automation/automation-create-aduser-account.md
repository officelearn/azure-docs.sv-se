---
title: "Skapa ett Azure AD-användarkonto | Microsoft Docs"
description: "Den här artikeln beskriver hur du skapar en Azure AD-användare kontoautentisering för runbooks i Azure Automation för att autentisera i Azure."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: azure active directory user, azure service management, azure ad user account
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: f0a9664898cd27529daf73d130dd25fd296a9b48
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Autentisera runbooks med den klassiska Azure-distributionen och Resource Manager
I den här artikeln beskrivs hur du konfigurerar ett Azure AD-användarkonto för Azure Automation-runbooks som körs mot den klassiska Azure-distributionsmodellen eller Azure Resource Manager-resurser.  Medan det fortsätter att vara en identitet för autentiseringsmetoder som stöds för dina Azure Resource Manager-baserat runbooks, är den rekommenderade metoden att använda en Azure kör som-konto.       

## <a name="create-a-new-azure-active-directory-user"></a>Skapa en ny Azure Active Directory-användare
1. Logga in på Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Azure Active Directory** > **användare och grupper** > **alla användare** > **ny användare**.
3. Ange information om användaren vill **namn** och **användarnamn**.  
4. Observera användarens fullständiga namn och tillfälliga lösenord.
5. Välj **Directory rollen**.
6. Tilldela rollen Global eller begränsad administratör.
7. Logga ut från Azure och logga sedan in igen med det konto som du nyss skapat. Du uppmanas att ändra användarens lösenord.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Skapa ett Automation-konto på Azure Portal
I det här avsnittet utför du följande steg för att skapa ett Azure Automation-konto på Azure Portal för användning med dina runbooks som hanterar resurser i Azure Resource Manager-läget.  

1. Logga in på Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Automation-konton**.
3. Välj **Lägg till**.<br><br>![Lägga till ett Automation-konto](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.
5. Om du har mer än en prenumeration anger du den som du vill använda för det nya kontot, samt en ny eller befintlig **resursgrupp** och en **plats** för Azure-datacentret.
6. Välj värdet **Ja** för alternativet **Skapa Kör som-konto i Azure** och klicka på knappen **Skapa**.  
   
    > [!NOTE]
    > Om du väljer att inte skapa ”Kör som”-kontot genom att välja alternativet **Nej** visas ett varningsmeddelande på bladet **Lägg till Automation-konto**.  När kontot skapas och tilldelas till rollen **Deltagare** i prenumerationen har den ingen tillhörande autentiseringsidentitet i din prenumerationskatalogtjänst och har därför ingen åtkomst till resurser i din prenumeration.  Det förhindrar att runbooks som refererar till det här kontot kan autentisera och utföra åtgärder mot Azure Resource Manager-resurser.
    > 
    >

    <br>![Varningsmeddelande för Lägg till Automation-konto](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

När du har skapat autentiseringsuppgifterna måste du skapa en autentiseringstillgång för att associera Automation-kontot med det AD-användarkonto du skapade tidigare.  Kom ihåg att vi endast skapade Automation-kontot och att det inte är associerat med en autentiseringsidentitet.  Följ stegen i artikeln [Autentiseringstillgångar i Azure Automation](automation-credentials.md#creating-a-new-credential-asset) och ange värdet för **användarnamn** i formatet **domän\användare**.

## <a name="use-the-credential-in-a-runbook"></a>Använda autentiseringsuppgifter i en runbook
Du kan hämta autentiseringsuppgifterna i en runbook med hjälp av [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) och sedan använda dem med [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) för att ansluta till din Azure-prenumeration. Om autentiseringsuppgifterna är en administratör för fler Azure-prenumerationer bör du även använda [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) för att ange rätt prenumeration. Detta illustreras i Windows PowerShell-exemplet nedan som du ofta ser överst i de flesta Azure Automation-runbooks.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Upprepa dessa rader efter eventuella [kontrollpunkter](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) i din runbook. Om runbooken har pausats och sedan fortsätter i en annan arbetsprocess måste autentiseringen göras igen.

## <a name="next-steps"></a>Nästa steg
* Gå igenom de olika runbook-typerna och stegen för att skapa egna runbooks genom att läsa artikeln [Typer av Azure Automation-runbooks](automation-runbook-types.md)

