<properties
   pageTitle="Konfigurera Azure Automation"
   description="Beskriver de steg som du måste utföra för att konfigurera Azure Automation för den första användningen."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="magoedte;bwren" />

# Konfigurera Azure Automation

Den här artikeln beskriver de åtgärder som du måste utföra för att börja använda Azure Automation.

## Automation-konton

När du startar Azure Automation för första gången måste du skapa minst ett Automation-konto. Automation-konton gör att du kan isolera dina Automation-resurser (runbooks, tillgångar, konfigurationer) från Automation-resurserna som finns i andra Automation-konton. Du kan använda Automation-konton för att hålla Automation- resurser åtskilda i separata logiska miljöer. Du kan exempelvis använda ett konto för utveckling och ett annat för produktion.

Automation-resurserna för varje Automation-konto associeras med en enda Azure-region, men Automation-konton kan hantera Azure-tjänster i alla regioner. Huvudskälet till att skapa Automation-konton i olika regioner är om du har principer som kräver att data och resurser är isolerade i en specifik region.

>[AZURE.NOTE] Automation-konton, och de resurser som de innehåller, som skapats med Azure-portalen kan inte nås på den klassiska Azure-portalen. Om du vill hantera dessa konton eller deras resurser med Windows PowerShell måste du använda Azure Resource Manager-modulerna. 
>
>Automation-konton som skapats med den klassiska Azure-portalen kan hanteras på båda portalerna och med båda cmdlet-uppsättningarna. När kontot har skapats spelar det ingen roll hur du skapar och hanterar resurser i kontot. Om du vill fortsätta att använda den klassiska Azure-portalen använder du den i stället för Azure-portalen för att skapa Automation-konton.


Ett Automation-konto kan inaktiveras om det uppstår problem med ditt Azure-konto, till exempel på grund av en försenad betalning. I så fall kan du inte komma åt kontot, och eventuella jobb som körs avbryts tillfälligt och alla scheman inaktiveras. Du kan visa kontot men du kan inte se några resurser i det. När du har korrigerat problemet och Automation-kontot har aktiverats måste du aktivera dina scheman och starta om alla runbooks som har pausats.


## Konfigurera autentisering för Azure-resurser

När du kommer åt Azure-resurser med hjälp av [Azure-cmdlets](http://msdn.microsoft.com/library/azure/jj554330.aspx) krävs autentisering mot din Azure-prenumeration. Detta görs i Azure Automation med ett organisationskonto i Azure Active Directory som du konfigurerar som administratör för din prenumeration. Därefter kan du skapa [autentiseringsuppgifter](http://msdn.microsoft.com/library/dn940015.aspx) för det här användarkontot och använda det med [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) i din runbook.

>[AZURE.NOTE] Microsoft-konton, tidigare kallade LiveID:n, kan inte användas med Azure Automation.

## Skapa en ny Azure Active Directory-användare för att hantera en Azure-prenumeration

1. Logga in på den klassiska Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Active Directory**
3. Välj katalognamnet som associeras med Azure-prenumerationen. Om det behövs kan du ändra den här associationen från **Inställningar > Prenumerationer > Redigera katalog**.
4. [Skapa en ny Active Directory-användare](http://msdn.microsoft.com/library/azure/hh967632.aspx).  Välj **Ny användare i organisationen** för **Typ av användare** och låt bli att **Aktivera Multi-Factor Authentication**.
5. Observera användarens fullständiga namn och tillfälliga lösenord.
7. Välj **Inställningar > Administratörer > Lägg till**.
8. Ange det fullständiga användarnamnet för den användare som du skapade.
9. Välj den prenumeration som du vill att användaren ska hantera.
10. Logga ut från Azure och logga sedan in igen med det konto som du nyss skapat. Du uppmanas att ändra användarens lösenord.
11. Skapa en ny [Azure Automation-autentiseringstillgång](automation-credentials.md) för det användarkonto som du nyss skapade. **Typ av autentiseringsuppgift** ska vara **Windows PowerShell-autentiseringsuppgift**.

## Skapa ett Automation-konto

Ett Automation-konto är en behållare för dina Azure Automation-resurser. Det är ett sätt att avgränsa dina miljöer eller att ytterligare ordna dina arbetsflöden. Om du redan har skapat ett Automation-konto kan du hoppa över det här steget.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Klicka på **Nytt** > **Hantering** > **Automation-konto**

3. I bladet **Lägg till Automation-konto** konfigurerar du Automation-kontoinformationen. 

>[AZURE.NOTE] När ett Automation-kontot skapas på Azure-portalen återförs inte kontot och de resurser som är associerade med det till den klassiska Azure-portalen. 

Nedan visas en lista över de parametrar som du kan konfigurera:

|Parameter            |Beskrivning |
|:---|:---|
| Namn | Namnet på Automation-kontot. Namnet måste vara ett unikt värde. |
| Resursgrupp | Resursgrupper gör det enkelt att se och hantera relaterade Azure-resurser. På Azure-portalen kan du välja en befintlig resursgrupp eller skapa en ny för ditt Automation-konto. På den klassiska Azure-portalen placeras däremot alla Automation-konton i en standardresursgrupp. |
| Prenumeration | Välj en prenumeration i listan med tillgängliga prenumerationer. |
| Region | Regionen anger var Automation-resurser i kontot lagras. Du kan välja en region i listan. Regionsvalet påverkar inte funktionerna i ditt konto, men runbooks kan köras snabbare om din kontoregion finns nära den plats där dina andra Azure-resurser lagras. |
| Kontoalternativ | Det här alternativet gör att du kan välja vilka resurser som skapas i ditt Automation-konto. Om du väljer **Ja** skapas en självstudie-runbook. |

![Skapa ett konto](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE] När ett Automation-konto som har skapats med den klassiska Azure-portalen [flyttas till en annan resursgrupp](../resource-group-move-resources.md) med hjälp av Azure-portalen är Automation-kontot inte längre tillgängligt på den klassiska Azure-portalen.



## Använda autentiseringsuppgifter i en runbook

Du kan hämta autentiseringsuppgifterna i en runbook med hjälp av [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) och sedan använda dem med [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) för att ansluta till din Azure-prenumeration. Om autentiseringsuppgifterna är en administratör för fler Azure-prenumerationer bör du även använda [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) för att ange rätt prenumeration. Detta illustreras i Windows PowerShell-exemplet nedan som du ofta ser överst i de flesta Azure Automation-runbooks.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

Upprepa dessa rader efter eventuella [kontrollpunkter](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) i din runbook. Om runbooken har pausats och sedan fortsätter i en annan arbetsprocess måste autentiseringen göras igen.

## Relaterade artiklar
- [Azure Automation: Autentisera mot Azure med hjälp av Azure Active Directory](https://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 



<!--HONumber=jun16_HO2-->


