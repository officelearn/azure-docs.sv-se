<properties 
   pageTitle="Rollbaserad åtkomstkontroll i Azure Automation | Microsoft Azure"
   description="Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Den här artikeln beskriver hur du konfigurerar RBAC i Azure Automation."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="automation rbac, rollbaserad åtkomstkontroll, azure rbac" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/20/2016"
   ms.author="magoedte;sngun"/>

# Rollbaserad åtkomstkontroll i Azure Automation

## Rollbaserad åtkomstkontroll

Med rollbaserad åtkomstkontroll (RBAC) kan du hantera åtkomsten till Azure-resurser. Med hjälp av [RBAC](../active-directory/role-based-access-control-configure.md) kan du hålla isär uppgifter i ditt team och bevilja endast den mängd åtkomst till användare, grupper och program som de behöver för att utföra sitt arbete. Rollbaserad åtkomst kan beviljas till användare som använder Azure-portalen, Azure-kommandoradsverktygen eller Azure Management-API:er.

## RBAC i Automation-konton

I Azure Automation beviljas åtkomst genom att lämplig RBAC-roll tilldelas till användare, grupper och program i Automation-kontoomfånget. Följande är de inbyggda roller som stöds av ett Automation-konto:  

|**Roll** | **Beskrivning** |
|:--- |:---|
| Ägare | Ägarrollen ger tillgång till alla resurser och åtgärder i ett Automation-konto, inklusive möjligheten att ge andra användare, grupper och program hanteringsåtkomst till Automation-kontot. |
| Deltagare | Med deltagarrollen kan du hantera allt, men du kan inte ändra andra användares åtkomstbehörighet till ett Automation-konto. |
| Läsare | Med läsarrollen kan du visa alla resurser i ett Automation-konto men inte göra några ändringar.|
| Automation-operatör | Med rollen som Automation-operatör kan du utföra driftåtgärder, till exempel starta, stoppa, pausa, återuppta och schemalägga jobb. Den här rollen är användbar om du vill skydda dina Automation-kontoresurser, t.ex. autentiseringstillgångar och runbooks, så att de inte kan visas eller ändras men fortfarande vill att medlemmar i din organisation ska kunna köra dessa runbooks. |
| Administratör för användaråtkomst | Med rollen Administratör för användaråtkomst kan du hantera användaråtkomsten till Azure Automation-konton. |

>[AZURE.NOTE] Du kan inte bevilja åtkomst till en eller flera specifika runbooks, endast för resurserna och åtgärderna på Automation-kontot.  

I den här artikeln beskriver vi steg för steg hur du konfigurerar RBAC i Azure Automation. Men låt oss först ta en närmare titt på de enskilda behörigheter som beviljas deltagare, läsare, Automation-operatör och administratör för användaråtkomst så att vi får en god översikt innan vi beviljar någon behörighet till Automation-kontot.  Annars kan beviljandet
resultera i oväntade eller oönskade konsekvenser.     

## Deltagarbehörigheter

I följande tabell visas vilka åtgärder som kan utföras av deltagarrollen i Automation.

| **Resurstyp** | **Läsa** | **Skriva** | **Ta bort** | **Andra åtgärder** |
|:--- |:---|:--- |:---|:--- |
| Azure Automation – konto | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | 
| Automation – certifikattillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation – anslutningstillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | 
| Automation – anslutningstypstillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | 
| Automation – autentiseringstillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation – schematillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation – variabeltillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation – önskad tillståndskonfiguration | | | | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) |
| Hybrid Runbook Worker – resurstyp | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | 
| Azure Automation – jobb | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | 
| Automation – jobbström | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Automation – jobbschema | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | |
| Automation – modul | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure Automation – Runbook | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) |
| Automation – Runbook-utkast | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) |
| Automation – testjobb för Runbook-utkast | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | 
| Automation – webhook | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) |

## Behörighet för läsare

I följande tabell visas vilka åtgärder som kan utföras av rollen Läsare i Automation.

| **Resurstyp** | **Läsa** | **Skriva** | **Ta bort** | **Andra åtgärder** |
|:--- |:---|:--- |:---|:--- |
| Klassisk prenumerationsadministratör | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Hanteringslås | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Behörighet | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | |
| Leverantörsåtgärder | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Rolltilldelning | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Rolldefinition | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | 

## Behörigheter för Automation-operatör

I följande tabell visas vilka åtgärder som kan utföras av rollen Automation-operatör i Automation.

| **Resurstyp** | **Läsa** | **Skriva** | **Ta bort** | **Andra åtgärder** |
|:--- |:---|:--- |:---|:--- |
| Azure Automation – konto | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Automation – certifikattillgång | | | |
| Automation – anslutningstillgång | | | |
| Automation – anslutningstypstillgång | | | |
| Automation – autentiseringstillgång | | | |
| Automation – schematillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | |
| Automation – variabeltillgång | | | |
| Automation – önskad tillståndskonfiguration | | | | |
| Hybrid Runbook Worker – resurstyp | | | | | 
| Azure Automation – jobb | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | 
| Automation – jobbström | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | |  
| Automation – jobbschema | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | |
| Automation – modul | | | |
| Azure Automation – Runbook | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – Runbook-utkast | | | |
| Automation – testjobb för Runbook-utkast | | | |  
| Automation – webhook | | | |

Mer information om de åtgärder som stöds av rollen Automation-operatör i Automation-kontot och dess resurser finns i [Automation-operatörsåtgärder](../active-directory/role-based-access-built-in-roles.md#automation-operator).

## Behörigheter för Administratör för användaråtkomst

I följande tabell visas vilka åtgärder som kan utföras av rollen Administratör för användaråtkomst i Automation.

| **Resurstyp** | **Läsa** | **Skriva** | **Ta bort** | **Andra åtgärder** |
|:--- |:---|:--- |:---|:--- |
| Azure Automation – konto | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – certifikattillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – anslutningstillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – anslutningstypstillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – autentiseringstillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – schematillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – variabeltillgång | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – önskad tillståndskonfiguration | | | | |
| Hybrid Runbook Worker – resurstyp | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Azure Automation – jobb | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Automation – jobbström | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Automation – jobbschema | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – modul | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure Automation – Runbook | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – Runbook-utkast | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Automation – testjobb för Runbook-utkast | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Automation – webhook | ![Grön status](media/automation-role-based-access-control/green-checkmark.png) | | |

## Konfigurera RBAC för ditt Automation-konto med hjälp av Azure-portalen

1.  Logga in på [Azure-portalen](https://portal.azure.com/) och öppna Automation-kontot från bladet Automation-konton.  

2.  Klicka på kontrollen **Åtkomst** längst uppe till höger. Bladet **Användare** öppnas där du kan lägga till nya användare, grupper och program för att hantera Automation-kontot och visa befintliga roller som kan konfigureras för Automation-kontot.  

    ![Knappen Åtkomst](media/automation-role-based-access-control/automation-01-access-button.png)  

>[AZURE.NOTE] **Prenumerationsadministratörer** är redan konfigurerade som standardanvändaren. Prenumerationsadministratörernas Active Directory-grupp innehåller tjänstadministratörerna och medadministratörerna för din Azure-prenumeration. Tjänstadministratören är ägaren av din Azure-prenumeration och dess resurser och ärver även ägarrollen från Automation-kontona. Det innebär att **tjänstadministratörer och medadministratörer** för en prenumeration **ärver** åtkomsten och den **tilldelas** för alla andra användare. Klicka på **Prenumerationsadministratörer** om du vill visa mer information om deras behörigheter.  

### Lägga till en ny användare och tilldela en roll

1.  Klicka på bladet **Lägg till** från bladet Användare för att öppna bladet **Lägg till åtkomst** där du kan lägga till en användare, grupp eller ett program och tilldela dem en roll.  

    ![Lägga till användare](media/automation-role-based-access-control/automation-02-add-user.png)  

2.  Välj en roll i listan över tillgängliga roller. Vi ska välja rollen **Läsare**, men du kan välja någon av de tillgängliga inbyggda rollerna som ett Automation-konto har stöd för eller en anpassad roll som du har definierat.  

    ![Välja en roll](media/automation-role-based-access-control/automation-03-select-role.png)  

3.  Öppna bladet **Lägg till användare** genom att klicka på **Lägg till användare**. Om du har lagt till användare, grupper eller program för att hantera din prenumeration så visas dessa användare och du kan välja dem för att lägga till åtkomst. Om det inte finns några användare i listan, eller om användaren som du vill lägga till inte visas, klickar du på **Bjud in** för att öppna bladet **Bjud in gäst** där du kan bjuda in användare med en giltig e-postadress till ett Microsoft-konto, till exempel Outlook.com-, OneDrive- eller Xbox Live-ID:n. När du har angett e-postadressen för användaren klickar du på **Välj** för att lägga till användaren och klickar sedan på **OK**. 

    ![Lägga till användare](media/automation-role-based-access-control/automation-04-add-users.png)  
 
    Nu bör du se användaren på bladet **Användare** med rollen **Läsare**.  

    ![Visa användare](media/automation-role-based-access-control/automation-05-list-users.png)  

    Du kan också tilldela en roll till användaren från bladet **Roller**. 

1. Klicka på **Roller** från bladet Användare för att öppna bladet **Roller**. Från det här bladet kan du visa namnet på rollen och antalet användare och grupper som har tilldelats till rollen.

    ![Tilldela en roll från bladet Användare](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
    >[AZURE.NOTE] Rollbaserad åtkomstkontroll kan bara anges på Automation-kontonivån och inte för en resurs under Automation-kontot.

    Du kan tilldela mer än en roll för en användare, grupp eller ett program. Om vi till exempel lägger till rollen **Automation-operatör** tillsammans med **läsarrollen** för användaren så kan de visa alla Automation-resurser samt köra runbook-jobben. Du kan expandera listrutan om du vill visa en lista över roller som användaren har tilldelats.  

    ![Visa flera roller](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  
 
### Ta bort en användare

Du kan ta bort åtkomstbehörigheten för en användare som inte hanterar Automation-kontot eller som inte längre arbetar i organisationen. Nedan följer stegen för att ta bort en användare: 

1.  Från bladet **Användare** väljer du den rolltilldelning som du vill ta bort.

2.  Klicka på knappen **Ta bort** på bladet med tilldelningsinformation.

3.  Bekräfta borttagningen genom att klicka på **Ja**. 

    ![Ta bort användare](media/automation-role-based-access-control/automation-08-remove-users.png)  

## Roll tilldelad till användare

Om användare som tilldelats en roll loggar in på sina Automation-konton kan de nu se ägarens konto i listan med **standardkataloger**. För att visa det Automation-konto som de har lagts till i måste de byta standardkatalogen till ägarens standardkatalog.  

![Standardkatalog](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### Användarupplevelsen för Automation-operatörsrollen

Om en användare som har tilldelats Automation-operatörsrollen visar det Automation-konto som han har tilldelats kan han bara visa listan med runbooks, runbook-jobb och scheman som skapats i Automation-kontot, men inte deras definition. Användaren kan starta, stoppa, pausa, återuppta eller schemalägga runbook-jobbet. Användaren har inte åtkomst till andra Automation-resurser, till exempel konfigurationer, Hybrid Worker-grupper eller DSC-noder.  

![Ingen åtkomst till resurser](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

När användaren klickar på runbooken är inte kommandona för att visa källan eller redigera runbooken tillgängliga eftersom Automation-operatörsrollen inte tillåter åtkomst till dem.  

![Ingen åtkomst för att redigera runbooks](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

Användaren kan visa och skapa scheman, men har inte åtkomst till någon annan typ av tillgång.  

![Ingen åtkomst till tillgångar](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Den här användaren kan inte heller visa webhooks som är associerade med en runbook.

![Ingen åtkomst till webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## Konfigurera RBAC för ditt Automation-konto med hjälp av Azure PowerShell

Du kan också konfigurera rollbaserad åtkomst till ett Automation-konto med hjälp av följande [Azure PowerShell-cmdlets](../active-directory/role-based-access-control-manage-access-powershell.md).

• [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) visar alla RBAC-roller som är tillgängliga i Azure Active Directory. Du kan använda det här kommandot tillsammans med egenskapen **Namn** för att visa en lista över alla de åtgärder som kan vidtas av en viss roll.  
    **Exempel:**  
    ![Hämta rolldefinition](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) visar Azure AD RBAC-rolltilldelningar i det angivna omfånget. Utan parametrar returnerar detta kommando alla rolltilldelningar som skapats under prenumerationen. Använd parametern **ExpandPrincipalGroups** om du vill visa en lista med alla åtkomsttilldelningar för den angivna användaren och för de grupper som användaren är medlem i.  
    **Exempel:** Använd följande kommando om du vill visa alla användare och deras roller i ett Automation-konto.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Hämta rolltilldelning](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) om du vill ge åtkomst till användare, grupper och program i ett visst omfång.  
    **Exempel:** Använd följande kommando om du vill tilldela rollen ”Automation-operatör” för en användare i Automation-kontoomfånget.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Ny rolltilldelning](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Använd [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) om du vill ta bort åtkomsten till den angivna användaren, gruppen eller programmet i ett visst omfång.  
    **Exempel:** Använd följande kommando för att avlägsna användaren från rollen ”Automation-operatör” i Automation-kontoomfånget.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

I cmdletarna ovan ersätter du **inloggnings-ID**, **prenumerations-ID**, **resursgruppsnamn** och **Automation-kontonamn** med din kontoinformation. Välj **Ja** när du uppmanas att bekräfta innan du fortsätter att ta bort rolltilldelningen för användaren.   


## Nästa steg
-  Information om hur du kan konfigurera RBAC på olika sätt med Azure Automation finns i [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Mer information om hur du kan starta en runbook på olika sätt finns i [Starta en runbook](automation-starting-a-runbook.md)
- Information om olika runbook-typer finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)




<!--HONumber=sep16_HO1-->


