---
title: Source Control-integrering i Azure Automation - Legacy
description: I den här artikeln beskrivs källkontrollintegrering med GitHub i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 651b97dabfd3cce858ea1f905a39c10bd7d81c41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75417443"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integrering av källkontroll i Azure Automation - Legacy

> [!NOTE]
> Det finns en ny upplevelse för källkontroll. Mer information om den nya upplevelsen finns i [Källkontroll (förhandsgranskning)](source-control-integration.md).

Med källkontrollintegrering kan du associera runbooks i ditt Automation-konto till en GitHub-källkontrolldatabas. Med källkontrollen kan du enkelt samarbeta med ditt team, spåra ändringar och återställa till tidigare versioner av dina runbooks. Med källkontroll kan du till exempel synkronisera olika grenar i källkontroll med dina automationskonton för utveckling, test eller produktion, vilket gör det enkelt att marknadsföra kod som har testats i din utvecklingsmiljö till din produktion Automation Konto.

Med källkontrollen kan du skicka kod från Azure Automation till källkontroll eller hämta dina runbooks från källkontrollen till Azure Automation. I den här artikeln beskrivs hur du konfigurerar källkontroll i din Azure Automation-miljö. Vi börjar med att konfigurera Azure Automation för att komma åt din GitHub-databas och gå igenom olika åtgärder som kan göras med hjälp av källkontrollintegration. 

> [!NOTE]
> Källkontrollen stöder att dra och trycka [på PowerShell Workflow runbooks](automation-runbook-types.md#powershell-workflow-runbooks) samt [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks). [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) stöds ännu inte.

Det finns två enkla steg som krävs för att konfigurera källkontroll för ditt Automation-konto, och bara ett om du redan har ett GitHub-konto. De är:

## <a name="step-1--create-a-github-repository"></a>Steg 1 – Skapa en GitHub-databas

Om du redan har ett GitHub-konto och en databas som du vill länka till Azure Automation loggar du in på ditt befintliga konto och börjar från steg 2 nedan. Annars navigerar du till [GitHub,](https://github.com/)registrerar dig för ett nytt konto och [skapar en ny databas](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Steg 2 – Konfigurera källkontroll i Azure Automation

1. Klicka på **Källkontroll** under Kontoinställningar på sidan Automation-konto i **Azure-portalen.**

2. Sidan **Källkontroll** öppnas, där du kan konfigurera dina GitHub-kontouppgifter. Nedan visas en lista över de parametrar som du kan konfigurera:  

   | **Parametern** | **Beskrivning** |
   |:--- |:--- |
   | Välj Källa |Välj källa. För närvarande stöds endast **GitHub.** |
   | Auktorisering |Klicka på knappen **Auktorisera** om du vill ge Azure Automation åtkomst till din GitHub-databas. Om du redan är inloggad på ditt GitHub-konto i ett annat fönster används autentiseringsuppgifterna för det kontot. När auktoriseringen har slutförts visas ditt GitHub-användarnamn under **Auktoriseringsegenskap**. |
   | Välj databas |Välj en GitHub-databas i listan över tillgängliga databaser. |
   | Välj gren |Välj en gren i listan över tillgängliga grenar. Endast **huvudgrenen** visas om du inte har skapat några grenar. |
   | Sökväg till Runbook-mapp |Sökvägen till runbook-mappen anger sökvägen i GitHub-databasen som du vill skicka eller hämta koden från. Den måste anges i formatet **/foldername/subfoldername**. Endast runbooks i sökvägen till runbook-mappen synkroniseras med ditt Automation-konto. Runbooks i undermapparna i sökvägen till runbook-mappen synkroniseras **INTE.** Används **/** för att synkronisera alla runbooks under databasen. |
3. Om du till exempel har en databas med namnet **PowerShellScripts** som innehåller en mapp med namnet **RootFolder**, som innehåller en mapp med namnet **SubFolder**. Du kan använda följande strängar för att synkronisera varje mappnivå:

   1. Om du vill synkronisera runbooks från **databasen**är sökvägen till runbook-mapp*/*
   2. Om du vill synkronisera runbooks från **RootFolder**är sökvägen till runbook-mapp */RootFolder*
   3. Om du vill synkronisera runbooks från **Undermapps**är sökvägen till runbook-mapp */RootFolder/SubFolder*.
4. När du har konfigurerat parametrarna visas de på sidan **Konfigurera källkontroll.**  

    ![Kontrollsidan För källa som visar inställningarna](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. När du klickar på **OK**är källkontrollintegrering nu konfigurerad för ditt Automation-konto och bör uppdateras med din GitHub-information. Du kan nu klicka på den här delen för att visa alla dina källkontrollsynkroniseringsjobbhistorik.  

    ![Värden för den aktuella konfigurerade källkontrollkonfigurationen](media/source-control-integration-legacy/automation-RepoValues.png)
6. När du har konfigurerat källkontroll skapas två [variabla tillgångar](automation-variables.md) i ditt Automation-konto. Dessutom läggs ett auktoriserat program till i ditt GitHub-konto.

   * Variabeln **Microsoft.Azure.Automation.SourceControl.Connection** innehåller värdena för anslutningssträngen, som visas nedan.  

     | **Parametern** | **Värde** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |String |
     | `Value` |{"Branch":\<*Ditt grennamn*>"RunbookFolderPath":\<*Sökväg till runbookmapp*>"ProviderType":\<har värdet*1 för GitHub->"Repository":* \<Namnet på*databasen*>"Användarnamn":\<Ditt*GitHub-användarnamn*>} |

   * Variabeln **Microsoft.Azure.Automation.SourceControl.OAuthToken**innehåller det säkra krypterade värdet för din OAuthToken.  

     |**Parametern**            |**Värde** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.oauthToken |
     | `Type`  | Okänd (krypterad) |
     | `Value` | <*Krypterad OAuthToken*> |  

     ![Ett fönster som visar källkontrollvariabler](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automation Source Control** läggs till som ett auktoriserat program i ditt GitHub-konto. Så här visar du programmet: Från startsidan för GitHub navigerar du till dina > **profilinställningarsprogram** > **Applications**. **profile** Med det här programmet kan Azure Automation synkronisera din GitHub-databas med ett Automation-konto.  

     ![Programinställningar i GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Använda källkontroll i automation

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Checka in en runbook från Azure Automation till källkontroll

Med runbook-incheckning kan du skicka de ändringar du har gjort i en runbook i Azure Automation till källkontrolldatabasen. Nedan följer stegen för att checka in en runbook:

1. Skapa [en ny textkörning från](automation-first-runbook-textual.md)ditt Automation-konto eller redigera en befintlig [textkörningsbok](automation-edit-textual-runbook.md). Den här runbooken kan vara antingen ett PowerShell-arbetsflöde eller en PowerShell-skriptkörningsbok.  
2. När du har redigerat runbooken sparar du den och klickar på **incheckningen** från sidan **Redigera.**  

    ![Ett fönster som visar incheckningen till GitHub-knappen](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Incheckning från Azure Automation skriver över koden som för närvarande finns i källkontrollen. Git motsvarande kommandoradsinstruktion för incheckning är **git add + git commit + git push**  

3. När du klickar på **incheckningen**uppmanas du att göra ett bekräftelsemeddelande genom att klicka på **Ja** för att fortsätta.  

    ![En dialogruta som bekräftar incheckningen till källkontrollen](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Incheckning startar källkontrollkörningen: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Den här runbooken ansluter till GitHub och skickar ändringar från Azure Automation till din databas. Om du vill visa den incheckade jobbhistoriken går du tillbaka till fliken **Källkontrollintegrering** och klickar för att öppna sidan Databassynkronisering. På den här sidan visas alla dina källkontrolljobb.  Markera det jobb du vill visa och klicka för att visa informationen.  

    ![Ett fönster som visar resultatet av ett synkroniseringsjobb](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Källkontroll runbooks är speciella Automation runbooks som du inte kan visa eller redigera. Även om de inte visas på din runbook lista, ser du synkronisering jobb som visas i din jobblista.

5. Namnet på den ändrade runbooken skickas som en indataparameter för den incheckade runbooken. Du kan [visa jobbinformationen](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) genom att expandera runbook på sidan **Databassynkronisering.**  

    ![Ett fönster som visar indata för ett synkroniseringsjobb](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Uppdatera GitHub-databasen när jobbet är klart för att visa ändringarna.  Det bör finnas ett åtagande i databasen med ett commit-meddelande: **Uppdaterat *runbooknamn* i Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synkronisera runbooks från källkontroll till Azure Automation

Med synkroniseringsknappen på sidan Databassynkronisering kan du dra alla runbooks i sökvägen till din databas till ditt Automation-konto. Samma databas kan synkroniseras till mer än ett Automation-konto. Nedan följer stegen för att synkronisera en runbook:

1. Öppna sidan **Källkontrollintegrering/databassynkronisering** från det Automation-konto där du konfigurerar källkontrollintegrering/databassynkronisering och klicka på **Synkronisera**.  Du uppmanas att göra ett bekräftelsemeddelande och klicka på **Ja** för att fortsätta.  

    ![Knappen Synkronisera med meddelande som bekräftar att alla runbooks synkroniseras](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Synkronisering startar runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Den här runbooken ansluter till GitHub och hämtar ändringarna från din databas till Azure Automation. Du bör se ett nytt jobb på sidan **Databassynkronisering** för den här åtgärden. Om du vill visa information om synkroniseringsjobbet klickar du för att öppna sidan med jobbinformation.  

    ![Ett fönster som visar synkroniseringsresultaten för ett synkroniseringsjobb i en GitHub-databas](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > En synkronisering från källkontrollen skriver över utkastversionen av runbooks som för närvarande finns i ditt Automation-konto för **alla** runbooks som för närvarande finns i källkontrollen. Git-motsvarande kommandoradsinstruktion för synkronisering är **git pull**

![Ett fönster som visar alla loggar från ett pausat källkontrollsynkroniseringsjobb](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Koppla från källkontroll

Om du vill koppla från ditt GitHub-konto öppnar du sidan Databassynkronisering och klickar på **Koppla från**. När du har kopplat från källkontrollen finns runbooks som synkroniserades tidigare kvar i ditt Automation-konto, men sidan Databassynkronisering är inte aktiverad.  

  ![Ett fönster som visar knappen Koppla från för att koppla från källkontrollen](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Nästa steg

Mer information om källkontrollintegrering finns i följande resurser:  

* [Azure Automation: Source Control-integrering i Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Azure Automation: Integrera Runbook Source Control med Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
