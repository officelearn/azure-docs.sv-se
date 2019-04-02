---
title: Källkontrollsintegration i Azure Automation - äldre
description: Den här artikeln beskriver källkontrollsintegrering med GitHub i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c95af40c3fa3f9dad2bfb5ea4a1b9f585c636928
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807067"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Källkontrollintegrering i Azure Automation - äldre

> [!NOTE]
> Det finns en ny upplevelse för källkontroll. Läs mer om den nya upplevelsen i [källkontroll (förhandsversion)](source-control-integration.md).

Integrering av källkontroll kan du associera runbooks i ditt Automation-konto till en GitHub-källkontroll. Källkontroll kan du enkelt kan samarbeta med ditt team, spåra ändringar och återställa tidigare versioner av dina runbooks. Till exempel kan källkontroll du synkronisera olika grenar i källkontrollen till din utveckling, testning eller produktion Automation-konton, vilket gör det enkelt att flytta upp kod som har testats i din utvecklingsmiljö till din Automation i produktionsmiljön konto.

Källkontroll kan du skicka koden från Azure Automation till källkontroll eller för att hämta dina runbooks från källkontroll till Azure Automation. Den här artikeln beskriver hur du konfigurerar källkontroll i Azure Automation-miljön. Vi börjar genom att konfigurera Azure Automation för att få åtkomst till din GitHub-lagringsplats och gå igenom olika åtgärder som kan genomföras med hjälp av integrering av källkontroll. 

> [!NOTE]
> Källkontrollen har stöd för hämta och skicka [PowerShell Workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks) samt [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks). [Grafiska runbooks](automation-runbook-types.md#graphical-runbooks) stöds inte ännu.

Det finns två enkla steg som krävs för att konfigurera källkontroll för ditt Automation-konto, och bara ett om du redan har ett GitHub-konto. De är:

## <a name="step-1--create-a-github-repository"></a>Steg 1 – skapa en GitHub-lagringsplats

Om du redan har ett GitHub-konto och en databas som du vill länka till Azure Automation kan sedan logga in på ditt befintliga konto och starta från steg 2 nedan. Annars navigerar du till [GitHub](https://github.com/), registrera dig för ett nytt konto och [skapar en ny lagringsplats](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Steg 2 – konfigurera källkontroll i Azure Automation

1. Automation-kontot sida i Azure-portalen under **kontoinställningar**, klickar du på **källkontroll.**

2. Den **källkontroll** öppnas, där du kan konfigurera din GitHub-kontoinformation. Nedan visas en lista över de parametrar som du kan konfigurera:  

   | **Parametern** | **Beskrivning** |
   |:--- |:--- |
   | Välj källa |Välj källa. För närvarande endast **GitHub** stöds. |
   | Auktorisering |Klicka på den **auktorisera** knappen för att bevilja Azure Automation-åtkomst till din GitHub-lagringsplats. Om du redan är inloggad på ditt GitHub-konto i ett annat fönster, används autentiseringsuppgifterna för kontot. När auktoriseringen är klar visas sidan för ditt GitHub-användarnamn under **auktorisering egenskapen**. |
   | Välj centrallager |Välj en GitHub-lagringsplats i listan över tillgängliga databaser. |
   | Välj en gren |Välj en gren i listan över tillgängliga grenar. Endast den **master** grenen visas om du inte har skapat några grenar. |
   | Runbookmapp |Sökvägen till runbook-mappen anger sökvägen i GitHub-lagringsplats från vilken du vill skicka eller hämta din kod. Det måste anges i formatet **/mappnamn/undermappsnamn**. Endast runbookar i sökvägen till runbook-mappen kommer att synkroniseras till din Automation-konto. Runbooks i undermappar i sökvägen till runbook-mappen kommer **inte** synkroniseras. Använd **/** att synkroniseras alla runbookar under databasen. |
3. Exempel: Om du har en databas med namnet **PowerShellScripts** som innehåller en mapp med namnet **RootFolder**, som innehåller en mapp med namnet **undermapp**. Du kan använda följande strängar för att synkronisera varje mappnivå:

   1. Att synkronisera runbooks från **databasen**, runbookmapp är */*
   2. Att synkronisera runbooks från **RootFolder**, runbookmapp är */RootFolder*
   3. Att synkronisera runbooks från **undermapp**, runbookmapp är */RootFolder/undermapp*.
4. När du har konfigurerat parametrarna som de visas på den **Ange källkontroll** sidan.  

    ![Sidan källa kontroll som visar inställningarna](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. När du klickar på **OK**, integrering av källkontroll har nu konfigurerats för ditt Automation-konto och ska uppdateras med din GitHub-information. Nu kan du klicka på den här delen att visa alla källa för control sync jobbhistorik.  

    ![Värden för den aktuella konfigurerade konfigurationen av källkontroll](media/source-control-integration-legacy/automation-RepoValues.png)
6. När du har konfigurerat källkontroll, två [variabler för tillgångar](automation-variables.md) skapas i ditt Automation-konto. Dessutom läggs ett auktoriserat program till i ditt GitHub-konto.

   * Variabeln **Microsoft.Azure.Automation.SourceControl.Connection** innehåller värdena i anslutningssträngen, enligt nedan.  

     | **Parametern** | **Värde** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |String |
     | `Value` |{”Gren”:\<*din grennamnet*>, ”RunbookFolderPath”:\<*runbookmapp*>, ”providertyp”:\<*har värdet 1 för GitHub*>, ”databas”:\<*namnet på databasen*>, ”användarnamn”:\<*Your GitHub-användarnamn*>} |

   * Variabeln **Microsoft.Azure.Automation.SourceControl.OAuthToken**, innehåller din OAuthToken säker krypterade värdet.  

     |**Parametern**            |**Värde** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
     | `Type`  | Unknown(Encrypted) |
     | `Value` | <*Krypterade OAuthToken*> |  

     ![Ett fönster som visar källan kontroll variabler](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automation-källkontroll** läggs till som ett auktoriserat program till ditt GitHub-konto. Visa programmet: Från startsidan GitHub navigerar du till din **profil** > **inställningar** > **program**. Det här programmet kan Azure Automation att synkronisera dina GitHub-lagringsplatsen till ett Automation-konto.  

     ![Programinställningar i GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Med hjälp av källkontroll i Automation

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Checka in en runbook från Azure Automation till källkontroll

Runbook-kontroll i kan du skicka ändringar som du har gjort i en runbook i Azure Automation till centrallagret för källkontroll. Nedan visas stegen för att kontrollera i en runbook:

1. Från ditt Automation-konto [skapa en ny textbaserade runbook](automation-first-runbook-textual.md), eller [redigera en befintlig, text-runbook](automation-edit-textual-runbook.md). Denna runbook kan vara antingen ett PowerShell-arbetsflöde eller en runbook för PowerShell-skript.  
2. När du har redigerat en runbook, spara den och klicka på **checka in** från den **redigera** sidan.  

    ![Ett fönster som visar incheckning till GitHub-knapp](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Skriver över den kod som finns för närvarande i din källkontroll checka in från Azure Automation. I Git motsvarande kommandoradsanvisningen att checka in är **git Lägg till + git-incheckning + git push**  

3. När du klickar på **checka in**, ges du ett bekräftelsemeddelande visas, klicka på **Ja** att fortsätta.  

    ![En dialogruta som bekräftar att checka in källkontroll](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Startar källa kontroll runbook checkar in: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Denna runbook ansluter till GitHub och skickar ändringar från Azure Automation till databasen. Om du vill visa den kontrollerade i jobbhistoriken, gå tillbaka till den **integrering av källkontroll** fliken och klicka för att öppna sidan för synkronisering av centrallager. Den här sidan visas alla dina källkontrolljobb.  Välj det jobb som du vill visa och klicka om du vill visa information.  

    ![Ett fönster som visar resultatet av ett synkroniseringsjobb](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Källa kontroll runbooks är särskilda Automation-runbooks som du inte kan visa eller redigera. Även om de inte visas på din runbook-lista, kan du se synkroniseringsjobb som visar i din lista över jobb.

5. Namnet på den ändrade runbooken skickas som indataparameter för markerad i runbook. Du kan [visa Jobbinformationen](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) genom att expandera runbook i **synkronisering av centrallager** sidan.  

    ![Ett fönster som visar indata för en-synkroniseringsjobb](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Uppdatera din GitHub-lagringsplatsen när jobbet har slutförts för att visa ändringarna.  Det bör finnas en allokering i din lagringsplats med ett meddelande: **Uppdatera *Runbooknamn* i Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synkronisera runbooks från källkontroll till Azure Automation

Knappen Synkronisera på sidan för synkronisering av centrallager kan du hämta alla runbooks i runbookmapp av lagringsplatsen till ditt Automation-konto. Samma databas kan synkroniseras till mer än en Automation-konto. Nedan visas stegen för att synkronisera en runbook:

1. Automation-konto där du konfigurerar källkontroll, öppna den **Integration/lagringsplats för Källkontrollssynkronisering** och klicka på **synkronisering**.  Du uppmanas via ett bekräftelsemeddelande klickar du på **Ja** att fortsätta.  

    ![Knappen för att synkronisera med meddelande som bekräftar att alla runbooks kommer att synkroniseras](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Synkronisering startar runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Denna runbook ansluter till GitHub och hämtar ändringarna från databasen till Azure Automation. Du bör se ett nytt jobb på den **synkronisering av centrallager** för den här åtgärden. Klicka för att öppna informationssidan jobbet om du vill visa information om synkroniseringsjobb.  

    ![Ett fönster som visar resultatet för synkronisering av ett synkroniseringsjobb på en GitHub-lagringsplats](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > En synkronisering från källkontroll skriver över Utkastversionen av de runbooks som för närvarande finns i ditt Automation-konto för **alla** runbooks som för närvarande finns som källkontroll. Git motsvarande kommandoradsverktyget anvisningarna för att synkronisera är **git pull**

![Ett fönster som visar alla loggar från ett pausat källa kontroll-synkroniseringsjobb](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Kopplar från källkontrollen

Du kopplar från ditt GitHub-konto genom att öppna sidan synkronisering av centrallager och klicka på **Disconnect**. När du kopplar från källkontrollen runbooks som har synkroniserats tidigare kvar i ditt Automation-konto men kommer inte att aktivera synkronisering av centrallager-sidan.  

  ![Ett fönster som visar knappen Koppla från att koppla från källkontrollen](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Nästa steg

Mer information om integrering av källkontroll, finns i följande resurser:  

* [Azure Automation: Källkontrollintegrering i Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Rösta på din favorit källkontrollsystem](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Azure Automation: Integrera källkontroll för Runbook med hjälp av Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
