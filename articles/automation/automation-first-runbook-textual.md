---
title: Min första PowerShell Workflow-Runbook i Azure Automation
description: Självstudiekurs som steg för steg beskriver hur du skapar, testar och publicerar en enkel text-runbook med hjälp av PowerShell Workflow.
keywords: powershell-arbetsflöde, powershell-arbetsflödesexempel, arbetsflöde powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: 4c816ade5ae6b416ba716433f385b75e1b32eb92
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226512"
---
# <a name="my-first-powershell-workflow-runbook"></a>Min första PowerShell Workflow-runbook

> [!div class="op_single_selector"]
> * [Grafisk](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Den här självstudien beskriver steg för steg hur du skapar en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks) i Azure Automation. Börja med en enkel Runbook som du testar och publicerar och lär dig hur du spårar statusen för Runbook-jobbet. Ändra sedan runbooken så att den hanterar Azure-resurser i praktiken genom att starta en virtuell Azure-dator. För det sista gör du runbooken mer robust genom att lägga till Runbook-parametrar.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* En virtuell dator i Azure. Eftersom du stoppar och startar den här datorn bör den inte vara en virtuell produktions dator.

## <a name="step-1---create-new-runbook"></a>Steg 1 – Skapa en ny runbook

Börja med att skapa en enkel runbook som visar texten **Hello World**.

1. Öppna ditt Automation-konto på Azure Portal.

   Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa till gångar är de moduler som automatiskt ingår i ett nytt Automation-konto. Du bör också ha den autentiseringsuppgifter som är kopplad till din prenumeration.
 
1. Öppna listan över runbooks genom att välja **Runbooks** under **process automatisering** .
1. Skapa en ny Runbook genom att välja **skapa en Runbook**.
1. Ge runbooken namnet **MyFirstRunbook-Workflow**.
1. I det här fallet ska du skapa en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Välj sedan **PowerShell-arbetsflöde** för **Runbook-typ**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="step-2---add-code-to-the-runbook"></a>Steg 2 – Lägga till kod i runbooken

Du kan antingen skriva kod direkt i runbooken eller välja cmdlets, Runbooks och till gångar från biblioteks kontrollen och lägga till dem i runbooken med relaterade parametrar. I den här självstudien skriver du kod direkt i runbooken.

1. Din Runbook är för närvarande Tom med endast det obligatoriska **arbets flödes** nyckelordet, namnet på runbooken och de klamrar som är i hela arbets flödet.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Skriv `Write-Output "Hello World"` mellan klammerparenteserna.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Spara runbooken genom att klicka på **Spara**.

## <a name="step-3---test-the-runbook"></a>Steg 3 – Testa runbooken

Innan du publicerar runbooken för att göra den tillgänglig i produktion bör du testa den och kontrol lera att den fungerar som den ska. Om du testar en runbook körs dess utkast version och du kan visa dess utdata interaktivt.

1. Öppna test fönstret genom att välja **test fönster** .
1. Klicka på **Starta** för att starta testet, och testa det enda aktiverade alternativet.
1. Observera att ett [Runbook-jobb](automation-runbook-execution.md) skapas och att dess status visas i fönstret.

   Jobbets status börjar i **kö**, vilket anger att jobbet väntar på att en Runbook Worker i molnet ska bli tillgänglig. Statusen ändras till att **börja** när en arbets uppgift anlitar jobbet. Slutligen blir statusen **igång** när runbooken faktiskt börjar köras.

1. När Runbook-jobbet har slutförts visas utdata i test fönstret. I det här fallet visas **Hello World**.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="step-4---publish-and-start-the-runbook"></a>Steg 4 – Publicera och starta runbooken

Den Runbook som du har skapat är fortfarande i utkast läge. Du måste publicera den innan du kan köra den i produktion. När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen. I det här fallet har du ingen publicerad version ännu eftersom du precis har skapat runbook-jobbet.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Rulla åt vänster för att Visa runbooken på sidan **Runbooks** och Observera att fältet **redigerings status** är inställt på **publicerat**.
1. Rulla tillbaka till höger för att visa sidan för **MyFirstRunbook-Workflow**.

   Med alternativen över överst kan du starta runbooken nu, schemalägga en framtida start tid eller skapa en [webhook](automation-webhooks.md) så att runbooken kan startas via ett HTTP-anrop.

1. Välj **Start** och sedan **Ja** när du uppmanas att starta runbooken.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Ett jobb fönster öppnas för det Runbook-jobb som har skapats. I det här fallet låter du fönstret vara öppet så att du kan se jobbets förlopp.

1. Observera att jobb statusen visas i **jobb Sammanfattning**. Den här statusen matchar de status värden som du såg när du testade runbooken.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. När runbookens status visas som **Slutförd** klickar du på **Utdata**. Sidan utdata öppnas, där du kan se **Hello World** -meddelandet.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Stäng sidan utdata.

1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör bara se **Hello World** i utdataströmmen. Observera att fönstret strömmar kan visa andra strömmar för ett Runbook-jobb, till exempel utförliga data strömmar och fel strömmar, om Runbook skriver till dem.

   ![Jobbsammanfattning](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Stäng fönstret strömmar och fönstret jobb för att återgå till sidan **MyFirstRunbook** .
1. Klicka på **jobb** under **resurser** för att öppna sidan jobb för denna Runbook. Den här sidan visar alla jobb som har skapats av din Runbook. Du bör bara se ett jobb i listan eftersom du bara har kört jobbet en gång.

   ![Jobb](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Klicka på jobb namnet för att öppna samma jobb fönster som du visade när du startade runbooken. Använd det här fönstret om du vill visa information om alla jobb som har skapats för runbooken.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Steg 5 – Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser. Det kan inte göra det om det inte autentiserar med hjälp av prenumerationens autentiseringsuppgifter. Autentiseringen använder cmdleten **Connect-AzAccount** .

>[!NOTE]
>För PowerShell-Runbooks är **Add-AzAccount** och **Add-AzureRMAccount** alias för **Connect-AzAccount**. Du kan använda dessa cmdletar, eller så kan du [Uppdatera dina moduler](automation-update-azure-modules.md) i ditt Automation-konto till de senaste versionerna. Du kan behöva uppdatera dina moduler även om du precis har skapat ett nytt Automation-konto.

1. Gå till sidan **MyFirstRunbook-Workflow** och öppna text redigeraren genom att klicka på **Redigera**.
2. Ta bort `Write-Output` raden.
3. Placera markören på en tom rad mellan klammerparenteserna.
4. Skriv eller kopiera och klistra in följande kod som hanterar autentiseringen med ditt kör som-konto för Automation.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Klicka på **test fönster** så att du kan testa runbooken.
1. Starta testet genom att klicka på **Starta**. När den är klar bör du se utdata som liknar följande och visa grundläggande information från ditt konto. Den här åtgärden bekräftar att autentiseringsuppgiften är giltig.

   ![Autentisera](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Steg 6 – Lägga till kod för att starta en virtuell dator

Nu när din Runbook autentiseras för Azure-prenumerationen kan du hantera resurser. Nu ska vi lägga till ett kommando för att starta en virtuell dator. Du kan välja vilken virtuell dator som helst i din Azure-prenumeration och nu hårdkoda du namnet i runbooken. Om du hanterar resurser över flera prenumerationer måste du använda parametern *AzContext* med cmdleten [Get-AzContext](/powershell/module/az.accounts/get-azcontext) .

1. Ange namn och resurs grupps namn för den virtuella dator som ska startas genom att ange ett anrop till cmdleten [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) enligt nedan. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Spara runbooken och klicka sedan på **test fönster** så att du kan testa den.
1. Starta testet genom att klicka på **Starta**. När den är klar kontrollerar du att den virtuella datorn har startats.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Steg 7 – Lägga till en indataparameter i runbooken

Din Runbook startar för närvarande den virtuella datorn som du har hårdkodad i runbooken. Det blir mer användbart om du kan ange den virtuella datorn när runbooken startas. Nu ska vi lägga till indataparametrar till Runbook för att tillhandahålla den funktionen.

1. Lägg till värden för *VMName* och *ResourceGroupName* i runbooken och Använd de associerade variablerna med cmdleten **Start-AzVM** enligt nedan.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Spara runbooken och öppna Testfönster. Nu kan du ange värden för de två variablerna som finns i testet.
3. Stäng Testfönster.
4. Klicka på **Publicera** för att publicera den nya versionen av runbooken.
5. Stoppa den virtuella datorn som du har startat.
6. Starta runbooken genom att klicka på **Starta**. 
7. Skriv värdena för **VMNAME** och **RESOURCEGROUPNAME** för den virtuella dator som du ska starta.

   ![Starta runbooken](media/automation-first-runbook-textual/automation-pass-params.png)

8. När Runbook-flödet har slutförts kontrollerar du att den virtuella datorn har startats.

## <a name="next-steps"></a>Nästa steg

* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).
* För att komma igång med grafiska runbooks, se [min första grafiska Runbook](automation-first-runbook-graphical.md).
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Mer information om Runbook-typer och deras fördelar och begränsningar finns i [Azure Automation Runbook-typer](automation-runbook-types.md).
* Mer information om stöd funktioner för PowerShell-skript finns [i stöd för interna PowerShell-skript i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
