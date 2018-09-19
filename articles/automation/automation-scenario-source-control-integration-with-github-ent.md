---
title: Azure Automation källkontrollsintegrering med GitHub Enterprise
description: Ger information om hur du konfigurerar integrering med GitHub Enterprise för källkontroll för Automation-runbooks.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8c7dc256b92252793545336ffc45a987054a5509
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "35648783"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Azure Automation-scenario – Automation källkontrollsintegrering med GitHub Enterprise

Automation stöder för närvarande integrering av källkontroll, där du kan associera runbooks i ditt Automation-konto till en GitHub-källkontroll. Men kunder som har distribuerat [GitHub Enterprise](https://enterprise.github.com/home) för att uppfylla sina DevOps-metoder, även vill använda den för att hantera livscykeln för runbooks som har utvecklats för att automatisera affärsprocesser och service management-åtgärder.

I det här scenariot har du en Windows-dator i ditt datacenter som konfigurerats som en Hybrid Runbook Worker med Azure Resource Manager-modulerna och Git-verktyg som installerats. Hybrid worker-datorn har en klon av den lokala Git-lagringsplatsen. När runbook körs på den hybrid worker, Git-katalogen är synkroniserad och filinnehållet runbook importeras till Automation-kontot.

Den här artikeln beskriver hur du ställer in den här konfigurationen i din miljö för Azure Automation. Du startar genom att konfigurera Automation med säkerhetsreferenser runbooks som krävs för att stödja det här scenariot och distribution av en Hybrid Runbook Worker i ditt datacenter för att köra runbooks och få åtkomst till din GitHub Enterprise-lagringsplats för att synkronisera runbooks med ditt Automation-konto.

## <a name="getting-the-scenario"></a>Hämta scenariot

Det här scenariot består av två PowerShell-runbooks som du kan importera direkt från den [Runbook-galleriet](automation-runbook-gallery.md) på Azure portal eller nedladdning från den [PowerShell-galleriet](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Beskrivning|
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook exporterar ett RunAs-certifikat från en Automation-konto till en hybrid worker, så att runbooks på worker kan autentisera med Azure för att importera runbooks till Automation-kontot.|
Sync-LocalGitFolderToAutomationAccount | Runbook synkroniserar den lokala Git-mappen på den hybrid-datorn och sedan importera runbook-filer (*.ps1) till Automation-kontot.|

### <a name="credentials"></a>Autentiseringsuppgifter

Autentiseringsuppgift | Beskrivning|
-----------|------------|
GitHRWCredential | Autentiseringstillgång som du skapar för att innehålla användarnamnet och lösenordet för en användare med behörighet till hybrid worker.|

## <a name="installing-and-configuring-this-scenario"></a>Installera och konfigurera det här scenariot

### <a name="prerequisites"></a>Förutsättningar

1. Synkronisera LocalGitFolderToAutomationAccount runbook autentiserar med hjälp av den [kör som-konto](automation-sec-configure-azure-runas-account.md).

2. Det krävs också en Log Analytics-arbetsyta med Azure Automation-lösningen aktiveras och konfigureras. Om du inte har en som är associerad med Automation-konto som används för att installera och konfigurera det här scenariot kan det skapas och konfigurerat åt dig när du kör den **New OnPremiseHybridWorker.ps1** skriptet från hybrid runbook arbetaren.

    > [!NOTE]
    > För närvarande följande regioner stöder Automation-integrering med Log Analytics - enbart **Australien, sydöstra**, **östra USA 2**, **Sydostasien**, och  **Västeuropa**.

3. En dator som kan fungera som en dedikerad Hybrid Runbook Worker som även är värd för GitHub-programvara och underhålla runbook-filer (*runbook*.ps1) i en källkatalog i filsystemet synkroniseras mellan GitHub och Automation konto.

### <a name="import-and-publish-the-runbooks"></a>Importera och publicera runbooks

Importera den *Export RunAsCertificateToHybridWorker* och *synkronisering LocalGitFolderToAutomationAccount* runbooks från Runbook-galleriet från ditt Automation-konto i Azure-portalen följer den Procedurerna i [importera Runbook från Runbook-galleriet](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publicera runbooks när de har importerats till ditt Automation-konto.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Distribuera och konfigurera Hybrid Runbook Worker

Om du inte har en Hybrid Runbook Worker som redan har distribuerats i ditt datacenter, bör du granska kraven och gör automatisk installation med hjälp av proceduren i Azure Automation Hybrid Runbook Worker - automatisera installera och konfiguration för [Windows](automation-windows-hrw-install.md#automated-deployment) eller [Linux](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker). När du har installerat hybrid worker på en dator, utför du följande steg för att slutföra konfigurationen för att stödja det här scenariot.

1. Logga in på värddatorn för Hybrid Runbook Worker-roll med ett konto som har lokala administratörsrättigheter och skapa en katalog för att lagra filer för Git-runbook. Klona den interna Git-databasen till katalogen.
1. Om du inte redan har ett RunAs-konto som har skapats eller om du vill skapa en ny en dedikerad för detta ändamål, från Azure-portalen navigerar du till Automation-konton, väljer du ditt Automation-konto och skapa en [autentiseringstillgång](automation-credentials.md) som innehåller användarnamn och lösenord för en användare med behörighet till hybrid worker.
1. Från ditt Automation-konto [redigera runbooken](automation-edit-textual-runbook.md)**Export RunAsCertificateToHybridWorker** och ändra värdet för variabeln *$Password* med ett starkt lösenord.    När du har ändrat värdet klickar du på **publicera** ha Utkastversionen av runbooken publiceras.
1. Starta runbooken **Export RunAsCertificateToHybridWorker**, och i den **starta Runbook** bladet, under alternativet **körningsinställningar** väljer alternativet  **Hybrid Worker** och välj Hybrid worker-grupp som du skapade tidigare i det här scenariot i den nedrullningsbara listan.

    Det här exporterar ett certifikat till hybrid worker så att runbooks på worker kan autentisera med Azure med hjälp av kör som-anslutningen för att hantera Azure-resurser (särskilt för det här scenariot, importera runbooks till Automation-konto).

1. Från ditt Automation-konto väljer du Hybrid worker-gruppen som skapades tidigare och [ange ett RunAs-konto](automation-hrw-run-runbooks.md#runas-account) för Hybrid worker-gruppen och välj autentiseringstillgång som du precis eller om du redan har skapat. Detta säkerställer att synkronisera runbook kan köra Git-kommandon. 
1. Starta runbooken **synkronisering LocalGitFolderToAutomationAccount**, ange följande obligatoriska indataparameter värden och i den **starta Runbook** bladet, under alternativet **kör inställningar**  väljer alternativet **Hybrid Worker** och i den nedrullningsbara listrutan väljer du Hybrid worker-grupp som du skapade tidigare i det här scenariot:

   * *ResourceGroup* -namnet på resursgruppen som är associerade med ditt Automation-konto
   * *AutomationAccountName* -namnet på ditt Automation-konto
   * *GitPath* -lokal mapp eller fil på den Hybrid Runbook Worker där Git ställs in för att hämta senaste ändringarna till

    Detta synkroniserar den lokala Git-mappen på den hybrid worker-datorn och sedan importerar .ps1-filer från källkatalogen till Automation-kontot.

1. Visa sammanfattningsinformation för jobb för runbook genom att välja den från den **Runbooks** blad i ditt Automation-konto och välj sedan den **jobb** panelen. Bekräfta att processen har slutförts genom att välja den **alla loggar** panel och granska detaljerad logg-dataströmmen.

## <a name="next-steps"></a>Nästa steg

* Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
* Mer information om PowerShell-skriptstöd finns i [Inbyggt PowerShell-skriptstöd i Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
