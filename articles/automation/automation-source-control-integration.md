---
title: Datakällan kontrollen integrering i Azure Automation
description: Den här artikeln beskriver källkontrollintegrering med GitHub i Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 29ec32c933f3dbe07a844bd99a1f5aa3fa57d61b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="source-control-integration-in-azure-automation"></a>Källkontrollintegrering i Azure Automation
Källkontrollintegrering kan du associera runbooks i ditt Automation-konto till en GitHub-källkontroll. Källkontrollen kan du enkelt samarbeta med din grupp, spåra ändringar och återställa tidigare versioner av dina runbooks. Till exempel kan källkontrollen du synkronisera olika filialer i källkontrollen till din utvecklings-, test- eller produktion Automation-konton, vilket gör det enkelt att befordra kod som har testats i din utvecklingsmiljö produktionsmiljön Automation-konto.

Källkontrollen kan du skicka koden från Azure Automation till källkontroll eller hämtar runbooks från källkontroll till Azure Automation. Den här artikeln beskriver hur du ställer in källkontrollen i Azure Automation-miljö. Vi börjar med att konfigurera Azure Automation för att få åtkomst till dina GitHub-lagringsplatsen och gå igenom olika åtgärder som kan utföras med hjälp av källkontrollintegrering. 

> [!NOTE]
> Källkontrollen har stöd för dra och push-överföring [PowerShell-arbetsflöde runbooks](automation-runbook-types.md#powershell-workflow-runbooks) samt [PowerShell runbooks](automation-runbook-types.md#powershell-runbooks). [Grafiska runbook-flöden](automation-runbook-types.md#graphical-runbooks) stöds inte ännu.<br><br>
> 
> 

Det finns två enkla steg som krävs för att konfigurera källkontroll för Automation-konto och bara om du redan har en GitHub-konto. De är:

## <a name="step-1--create-a-github-repository"></a>Steg 1 – skapa en GitHub-databas
Om du redan har en GitHub-konto och en databas som du vill länka till Azure Automation sedan logga in på ditt befintliga konto och starta från steg 2 nedan. Annars navigerar du till [GitHub](https://github.com/), registrera dig för ett nytt konto och [skapar en ny lagringsplats](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Steg 2 – konfigurera källkontroll i Azure Automation
1. Från Automation-kontot sidan i Azure-portalen under **kontoinställningar**, klickar du på **källkontroll.** 
   
1. Den **källkontrollen** öppnas, där du kan konfigurera din kontoinformation för GitHub. Nedan visas en lista över de parametrar som du kan konfigurera:  
   
   | **Parametern** | **Beskrivning** |
   |:--- |:--- |
   | Välj källa |Välj källa. För närvarande endast **GitHub** stöds. |
   | Auktorisering |Klicka på den **auktorisera** för att bevilja Azure Automation-åtkomst till dina GitHub-lagringsplatsen. Om du redan är inloggad på ditt GitHub-konto i ett annat fönster som autentiseringsuppgifterna för kontot används. När tillståndet är klar visas sidan GitHub-användarnamn under **auktorisering egenskapen**. |
   | Välj databasen |Välj en GitHub-databas i listan över tillgängliga databaser. |
   | Välj en gren |Välj en gren från listan över tillgängliga filialer. Endast den **master** grenen visas om du inte har skapat några filialer. |
   | Runbook-sökvägen för mappen |Sökvägen till runbook anger sökvägen i GitHub-databas som du vill push och pull-koden. Det måste anges i formatet **/mappnamn/undermappsnamn**. Endast runbookar i sökvägen till runbook-mappen kommer att synkroniseras till ditt Automation-konto. Runbooks i undermappar i sökvägen till runbook-mappen kommer **inte** synkroniseras. Använd **/** att synkronisera alla runbooks under databasen. |
3. Till exempel om du har en databas med namnet **PowerShellScripts** som innehåller en mapp med namnet **RootFolder så**, som innehåller en mapp med namnet **undermapp**. Du kan använda följande strängar för att synkronisera varje mappnivå:
   
   1. Att synkronisera runbooks från **databasen**, mappsökväg för runbook */*
   2. Att synkronisera runbooks från **RootFolder så**, runbook mappsökväg */RootFolder*
   3. Att synkronisera runbooks från **undermapp**, runbook mappsökväg */RootFolder så/undermapp*.
4. När du har konfigurerat parametrarna som de visas på den **ange källkontrollen** sidan.  
   
    ![Konfigurera datakälla kontroll på sidan](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. När du klickar på **OK**, källkontrollintegrering har nu konfigurerats för ditt Automation-konto och ska uppdateras med din GitHub-information. Nu kan du klicka på den här delen att visa alla källa för kontrollen sync jobbhistorik.  
   
    ![Värden för databasen](media/automation-source-control-integration/automation_03_RepoValues.png)
6. När du har skapat källkontrollen skapas följande Automation resurser i ditt Automation-konto:  
   Två [variabeln tillgångar](automation-variables.md) skapas.  
   
   * Variabeln **Microsoft.Azure.Automation.SourceControl.Connection** innehåller värdena i anslutningssträngen, enligt nedan.  
     
     | **Parametern** | **Värde** |
     |:--- |:--- |
     | Namn |Microsoft.Azure.Automation.SourceControl.Connection |
     | Typ |Sträng |
     | Värde |{”Gren”:\<*namnet på din gren*>, ”RunbookFolderPath”:\<*Runbook mappsökväg*>, ”providertyp”:\<*har värdet 1 för GitHub*>, ”databas”:\<*namnet på databasen*>, ”användarnamn”:\<*din GitHub-användarnamn*>} |

    * Variabeln **Microsoft.Azure.Automation.SourceControl.OAuthToken**, innehåller säker krypterade värdet för din OAuthToken.  

    |**Parametern**            |**Värde** |
    |:---|:---|
    | Namn  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Typ | Unknown(Encrypted) |
    | Värde | <*Krypterade OAuthToken*> |  

    ![Variabler](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Automation källkontrollen** läggs till som ett auktoriserade program till dina GitHub-konto. Så här visar du programmet: från startsidan GitHub navigerar du till din **profil** > **inställningar** > **program**. Det här programmet kan Azure Automation att synkronisera dina GitHub-lagringsplatsen till ett Automation-konto.  

    ![Git-program](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Använda källkontrollen i Automation
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Checka in en runbook från Azure Automation till källkontroll
Runbook-kontroll i kan du skicka ändringar som du har gjort i en runbook i Azure Automation i din källkontroll. Nedan följer stegen för att söka i en runbook:

1. Från ditt Automation-konto [skapa en ny textrepresentation runbook](automation-first-runbook-textual.md), eller [redigera en befintlig, textrepresentation runbook](automation-edit-textual-runbook.md). Denna runbook kan vara ett PowerShell-arbetsflöde eller en runbook för PowerShell-skript.  
2. När du redigerar en runbook, spara den och klickar på **checka in** från den **redigera** sidan.  
   
    ![Knappen incheckning](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Skriver över den kod som för närvarande finns i din källkontrollen checka in från Azure Automation. Git motsvarande kommandoradsverktyget anvisningarna för att checka in är **git Lägg till + git-incheckning + git push**  

1. När du klickar på **checka in**, uppmanas du ett bekräftelsemeddelande klickar du på **Ja** att fortsätta.  
   
    ![Incheckningsmeddelande](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. Checka in startar runbook för käll-kontroll: **Sync MicrosoftAzureAutomationAccountToGitHubV1**. Denna runbook ansluter till GitHub och skickar ändringarna från Azure Automation till databasen. Om du vill visa den markerade jobbhistorik gå tillbaka till den **Källkontrollintegrering** och klicka på för att öppna sidan databasen. Den här sidan visas alla dina källkontrolljobb.  Välj det jobb som du vill visa och klicka om du vill visa information.  
   
    ![Checka in Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Källan kontrollen runbooks är särskilda Automation-runbooks som du inte kan visa eller redigera. Även om de inte visas i listan runbook, kan du se synkroniseringsjobb visas i listan över jobb.
   > 
   > 
3. Namnet på den ändrade runbooken skickas som parameter för markerad i runbook. Du kan [visa Jobbinformationen](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) genom att expandera runbook i **databasen synkronisering** sidan.  
   
    ![Checka in indata](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. Uppdatera dina GitHub-lagringsplatsen när jobbet har slutförts för att visa ändringarna.  Det bör finnas ett genomförande i databasen med ett genomförande meddelande: **uppdaterade *Runbooknamnet* i Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synkronisering av runbooks från källkontroll Azure Automation
Knappen Synkronisera på sidan databasen kan du dra alla runbooks i runbook-sökvägen för mappen för databasen för att ditt Automation-konto. Samma databas kan synkroniseras till mer än en Automation-konto. Nedan visas stegen för att synkronisera en runbook:

1. Automation-konto där du ställa in källkontroll, öppna den **källa kontrollen Integration/databasen synkronisering** och klickar på **Sync**.  Du uppmanas ett bekräftelsemeddelande klickar du på **Ja** att fortsätta.  
   
    ![Synkronisera knappen](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. Synkronisering startar runbook: **Sync MicrosoftAzureAutomationAccountFromGitHubV1**. Denna runbook ansluter till GitHub och tar emot ändringarna från databasen till Azure Automation. Du bör se ett nytt jobb på den **databasen synkronisering** sidan för den här åtgärden. Klicka för att öppna sidan jobbet om du vill visa information om synkroniseringsjobb.  
   
    ![Synkronisera Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > En Synkronisera från källkontroll skriver över utkastversionen för runbooks som för närvarande finns i ditt Automation-konto för **alla** datakälla runbooks som för närvarande. Git motsvarande kommandoradsverktyget anvisningarna för att synkronisera är **git, pull**


## <a name="troubleshooting-source-control-problems"></a>Felsökning för käll-kontroll
Om det finns några fel med en kontroll i eller synkroniseringsjobb, jobbstatusen ska göra uppehåll och du kan visa mer information om felet i sidan jobb.  Den **alla loggar** del visar alla PowerShell strömmar som associeras med jobbet. Detta ger dig de information som behövs för att hjälpa dig att lösa eventuella problem med din incheckning eller synkronisering. Där visas också sekvens med åtgärder som uppstod när synkroniseringen eller kontroll i en runbook.  

![AllLogs bild](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Kopplar från källkontrollen
Om du vill koppla från GitHub-konto, öppna sidan databasen och klicka på **frånkoppling**. När du kopplar från källkontrollen runbooks som har synkroniserats tidigare kvar i ditt Automation-konto men sidan databasen aktiveras inte.  

  ![Koppla från](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>Nästa steg
Mer information om källkontrollintegrering finns i följande resurser:  

* [Azure Automation: Källkontrollintegrering i Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Rösta på din favorit källkontrollsystem](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Azure Automation: Integrera Runbook källkontrollen med hjälp av Visual Studio Team Services](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

