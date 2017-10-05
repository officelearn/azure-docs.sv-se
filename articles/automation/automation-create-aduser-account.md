---
title: "Skapa ett Azure AD-användarkonto | Microsoft Docs"
description: "I den här artikeln beskrivs hur du skapar autentiseringsuppgifter för ett Azure AD-användarkonto för runbooks i Azure Automation, för autentisering i Azure och klassiska Azure."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
keywords: azure active directory user, azure service management, azure ad user account
ms.assetid: fcfe266d-b22e-4dfb-8272-adcab09fc0cf
ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 4eaa3e36ededddeb5268ec4f49b9daee2f824cee
ms.contentlocale: sv-se
ms.lasthandoff: 08/15/2017

---
# <a name="authenticate-runbooks-with-azure-classic-deployment-and-resource-manager"></a>Autentisera runbooks med den klassiska Azure-distributionen och Resource Manager
I den här artikeln beskrivs hur du konfigurerar ett Azure AD-användarkonto för Azure Automation-runbooks som körs mot den klassiska Azure-distributionsmodellen eller Azure Resource Manager-resurser.  Även om du fortfarande kan använda den här autentiseringsidentiteten för Azure Resource Manager-baserade runbooks är den rekommenderade metoden att använda ett Kör som-konto i Azure.       

## <a name="create-a-new-azure-active-directory-user"></a>Skapa en ny Azure Active Directory-användare
1. Logga in på den klassiska Azure-portalen som tjänstadministratör för den Azure-prenumeration du vill hantera.
2. Välj **Active Directory** och välj sedan namnet på din organisationskatalog.
3. Välj fliken **Användare** och välj sedan **Lägg till användare** i kommandofältet.
4. På sidan **Berätta om den här användaren** under **Typ av användare** väljer du **Ny användare i din organisation**.
5. Ange ett användarnamn.  
6. Välj katalognamnet som associeras med din Azure-prenumeration på sidan Active Directory.
7. På användarens **profilsida** anger du ett för- och efternamn, ett användarvänligt namn och en användare från listan **Roller**.  **Aktivera inte Multi-Factor Authentication**.
8. Observera användarens fullständiga namn och tillfälliga lösenord.
9. Välj **Inställningar > Administratörer > Lägg till**.
10. Ange det fullständiga användarnamnet för den användare som du skapade.
11. Välj den prenumeration som du vill att användaren ska hantera.
12. Logga ut från Azure och logga sedan in igen med det konto som du nyss skapat. Du uppmanas att ändra användarens lösenord.

## <a name="create-an-automation-account-in-azure-classic-portal"></a>Skapa ett Automation-konto i den klassiska Azure-portalen
I det här avsnittet utför du följande steg för att skapa ett Azure Automation-konto på Azure Portal för användning med dina runbooks som hanterar resurser på den klassiska Azure-portalen.  

> [!NOTE]
> Automation-konton som skapas med den klassiska Azure-portalen kan hanteras både på den klassiska Azure-portalen och på Azure Portal samt med vardera uppsättning cmdlets. När kontot har skapats spelar det ingen roll hur du skapar och hanterar resurser i kontot. Om du vill fortsätta att använda den klassiska Azure-portalen använder du den i stället för Azure Portal när du skapar Automation-konton.
> 
> 

1. Logga in på den klassiska Azure-portalen som tjänstadministratör för den Azure-prenumeration du vill hantera.
2. Välj **Automation**.
3. På sidan **Automation** väljer du **Skapa ett Automation-konto**.
4. I rutan **Skapa ett Automation-konto** skriver du ett namn för ditt nya Automation-konto och väljer en **region** från listrutan.  
5. Acceptera inställningarna och skapa kontot genom att klicka på **OK**.
6. När det har skapats visas det på sidan **Automation**.
7. Klicka på kontot så kommer du till sidan Instrumentpanel.  
8. Välj **Tillgångar** på Automation-instrumentpanelssidan.
9. På sidan **Tillgångar** väljer du **Lägg till inställningar** längst ned på sidan.
10. På sidan **Lägg till inställningar** väljer du **Lägg till autentiseringsuppgift**.
11. På sidan **Definiera autentiseringsuppgift** väljer du **Windows PowerShell-autentiseringsuppgift** från listrutan **Typ av autentiseringsuppgift** och anger ett namn för autentiseringsuppgiften.
12. På nästa sida av typen **Definiera autentiseringsuppgift** anger du användarnamnet för AD-användarkontot som du skapade tidigare i fältet **Användarnamn** och lösenordet i fälten **Lösenord** och **Bekräfta lösenord**. Spara ändringarna genom att klicka på **OK**.

## <a name="create-an-automation-account-in-the-azure-portal"></a>Skapa ett Automation-konto på Azure Portal
I det här avsnittet utför du följande steg för att skapa ett Azure Automation-konto på Azure Portal för användning med dina runbooks som hanterar resurser i Azure Resource Manager-läget.  

1. Logga in på Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Automation-konton**.
3. Klicka på **Lägg till** på bladet Automation-konton.<br><br>![Lägga till ett Automation-konto](media/automation-create-aduser-account/add-automation-acct-properties.png)
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


