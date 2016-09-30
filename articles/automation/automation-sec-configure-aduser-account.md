<properties
   pageTitle="Konfigurera Azure AD-användarkonto | Microsoft Azure"
   description="Den här artikeln beskriver hur du konfigurerar autentiseringsuppgifter för ett Azure AD-användarkonto för runbooks i Azure Automation för att autentisera mot ARM och ASM."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="azure active directory-användare, azure service management, azure ad-användarkonto" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="magoedte" />


# Autentisera runbooks med Azure Service Management och Resource Manager

Den här artikeln beskriver de steg du utför när du konfigurerar ett Azure AD-användarkonto för Azure Automation-runbooks som körs mot Azure Service Management- (ASM) eller Azure Resource Manager-resurser (ARM).  Även om detta fortfarande är en autentiseringsidentitet som stöds för dina ARM-baserade runbooks är den rekommenderade metoden att använda det nya ”Kör som”-kontot i Azure.       

## Skapa en ny Azure Active Directory-användare

1. Logga in på den klassiska Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
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


## Skapa ett Automation-konto på den klassiska Azure-portalen
I det här avsnittet ska du utföra följande steg för att skapa ett nytt Azure Automation-konto på Azure-portalen som ska användas med dina runbooks för att hantera resurser i ASM- och ARM-läge.  

>[AZURE.NOTE] Automation-konton som skapats med den klassiska Azure-portalen kan hanteras av både den klassiska Azure-portalen och Azure-portalen och endera uppsättning cmdlets. När kontot har skapats spelar det ingen roll hur du skapar och hanterar resurser i kontot. Om du vill fortsätta att använda den klassiska Azure-portalen använder du den i stället för Azure-portalen för att skapa Automation-konton.


1. Logga in på den klassiska Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
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

## Skapa ett Automation-konto på Azure-portalen

I det här avsnittet ska du utföra följande steg för att skapa ett nytt Azure Automation-konto på Azure-portalen som ska användas med dina runbooks för att hantera resurser i ARM-läge.  

1. Logga in på Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Automation-konton**.
3. Klicka på **Lägg till** på bladet Automation-konton.<br>![Lägga till ett Automation-konto](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
2. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.
5. Om du har mer än en prenumeration anger du den som du vill använda för det nya kontot, samt en ny eller befintlig **resursgrupp** och en **plats** för Azure-datacentret.
3. Välj värdet **Nej** för alternativet **Skapa Kör som-konto i Azure** och klicka på knappen **Skapa**.  

    >[AZURE.NOTE] Om du väljer att inte skapa ”Kör som”-kontot genom att välja alternativet **Nej** visas ett varningsmeddelande på bladet **Lägg till Automation-konto**.  När kontot skapas och tilldelas till rollen **Deltagare** i prenumerationen har den ingen tillhörande autentiseringsidentitet i din prenumerationskatalogtjänst och har därför ingen åtkomst till resurser i din prenumeration.  Detta förhindrar att runbooks som refererar till det här kontot kan autentisera och utföra åtgärder mot ARM-resurser.

    ![Varningsmeddelande för Lägg till Automation-konto](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

4. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

När du har skapat autentiseringsuppgiften måste du skapa en autentiseringstillgång för att associera Automation-kontot med AD-användarkontot som du skapade tidigare.  Kom ihåg att vi endast skapade Automation-kontot och att det inte är associerat med en autentiseringsidentitet.  Följ stegen i artikeln [Autentiseringstillgångar i Azure Automation](../automation/automation-credentials.md#creating-a-new-credential) och ange värdet för **användarnamn** i formatet **domän\användare**.

## Använda autentiseringsuppgifter i en runbook

Du kan hämta autentiseringsuppgifterna i en runbook med hjälp av [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) och sedan använda dem med [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) för att ansluta till din Azure-prenumeration. Om autentiseringsuppgifterna är en administratör för fler Azure-prenumerationer bör du även använda [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) för att ange rätt prenumeration. Detta illustreras i Windows PowerShell-exemplet nedan som du ofta ser överst i de flesta Azure Automation-runbooks.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Upprepa dessa rader efter eventuella [kontrollpunkter](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) i din runbook. Om runbooken har pausats och sedan fortsätter i en annan arbetsprocess måste autentiseringen göras igen.

## Nästa steg
* Gå igenom de olika runbook-typerna och stegen för att skapa egna runbooks genom att läsa artikeln [Typer av Azure Automation-runbooks](../automation/automation-runbook-types.md)



<!--HONumber=Sep16_HO3-->


