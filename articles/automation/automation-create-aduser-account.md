---
title: Skapa Azure AD-användarkonto
description: Den här artikeln beskriver hur du skapar en Azure AD-användare kontoautentisering för runbooks i Azure Automation för att autentisera i Azure.
keywords: azure active directory user, azure service management, azure ad user account
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 105dcf5564ec5c1d0b3528e9b5667d89d98c6cab
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Autentisera runbooks med den klassiska Azure-distributionen och Resource Manager
I den här artikeln beskrivs hur du konfigurerar ett Azure AD-användarkonto för Azure Automation-runbooks som körs mot den klassiska Azure-distributionsmodellen eller Azure Resource Manager-resurser. Medan det fortsätter att vara en identitet för autentiseringsmetoder som stöds för dina Azure Resource Manager-baserat runbooks, är den rekommenderade metoden att använda en Azure kör som-konto.       

## <a name="create-a-new-azure-active-directory-user"></a>Skapa en ny Azure Active Directory-användare
1. Logga in på Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Azure Active Directory** > **användare och grupper** > **alla användare** > **ny användare**.
3. Ange information om användaren vill **namn** och **användarnamn**.  
4. Observera användarens fullständiga namn och tillfälliga lösenord.
5. Välj **Directory rollen**.
6. Tilldela rollen Global eller begränsad administratör.
7. Logga ut från Azure och sedan logga in igen med det konto som du nyss skapade. Du uppmanas att ändra användarens lösenord.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Skapa ett Automation-konto på Azure Portal
I det här avsnittet utför du följande steg för att skapa ett Azure Automation-konto på Azure Portal för användning med dina runbooks som hanterar resurser i Azure Resource Manager-läget.  

1. Logga in på Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Automation-konton**.
3. Välj **Lägg till**.<br><br>![Lägga till ett Automation-konto](media/automation-create-aduser-account/add-automation-acct-properties.png)
4. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.
5. Om du har mer än en prenumeration anger du den som du vill använda för det nya kontot, samt en ny eller befintlig **resursgrupp** och en **plats** för Azure-datacentret.
6. Välj värdet **Ja** för alternativet **Skapa Kör som-konto i Azure** och klicka på knappen **Skapa**.  
   
    > [!NOTE]
    > Om du väljer att inte skapa kör som-kontot genom att välja alternativet **nr**, visas ett varningsmeddelande i den **lägga till Automation-konto** bladet. När kontot skapas och tilldelas till den **deltagare** roll i prenumerationen har inte en motsvarande autentiseringsidentitet i katalogtjänsten prenumerationer och därför inte komma åt resurser i din prenumeration. Detta förhindrar alla runbooks som refererar till det här kontot från att kunna autentisera och utföra åtgärder mot resurser i Azure Resource Manager.
    > 
    >

    <br>![Varningsmeddelande för Lägg till Automation-konto](media/automation-create-aduser-account/add-automation-acct-properties-error.png)<br>  
7. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

När du har skapat autentiseringsuppgifterna måste du skapa en autentiseringstillgång för att associera Automation-kontot med det AD-användarkonto du skapade tidigare. Kom ihåg att du bara skapa Automation-kontot och är inte associerad med en autentiseringsidentitet. Följ stegen i artikeln [Autentiseringstillgångar i Azure Automation](automation-credentials.md#creating-a-new-credential-asset) och ange värdet för **användarnamn** i formatet **domän\användare**.

## <a name="use-the-credential-in-a-runbook"></a>Använda autentiseringsuppgifter i en runbook
Du kan hämta autentiseringsuppgifterna i en runbook med hjälp av [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) och sedan använda dem med [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) för att ansluta till din Azure-prenumeration. Om autentiseringsuppgifterna är en administratör för fler Azure-prenumerationer bör du även använda [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) för att ange rätt prenumeration. Detta framgår av följande PowerShell-exempel som normalt visas överst i de flesta Azure Automation-runbook.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Upprepa dessa rader om någon [kontrollpunkter](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) i din runbook. Om runbooken pausas och återupptas på en annan worker, måste den autentisera igen.

## <a name="next-steps"></a>Nästa steg
* Gå igenom de olika runbook-typerna och stegen för att skapa egna runbooks genom att läsa artikeln [Typer av Azure Automation-runbooks](automation-runbook-types.md)

