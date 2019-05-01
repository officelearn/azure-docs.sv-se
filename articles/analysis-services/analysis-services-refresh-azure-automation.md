---
title: Uppdatera Azure Analysis Services-modeller med Azure Automation | Microsoft Docs
description: Lär dig mer om att skriva kod modellen uppdateras med hjälp av Azure Automation.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 1897193f0ae781029a7303c42ca8eeaa51389892
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64920581"
---
# <a name="refresh-with-azure-automation"></a>Uppdatera med Azure Automation

Med Azure Automation och PowerShell-Runbooks kan utföra du automatiserade datauppdateringar på din Azure Analysis-tabellmodeller.  

I exemplet i den här artikeln används den [PowerShell SqlServer-moduler](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Ett exempel PowerShell-Runbook som visar att uppdatera en modell finns senare i den här artikeln.  

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig token för Azure Active Directory (OAuth 2).  I exemplet i den här artikeln använder tjänstens huvudnamn (SPN) för att autentisera till Azure Analysis Services.

Mer information om hur du skapar ett huvudnamn för tjänsten finns]

## <a name="prerequisites"></a>Nödvändiga komponenter

> [!IMPORTANT]
> I följande exempel förutsätter att Azure Analysis Services-brandväggen är inaktiverad. Om en brandvägg har aktiverats behöver offentliga IP-adressen för begäran initieraren tillåtas i brandväggen.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installera SqlServer-moduler från PowerShell-galleriet.

1. I Azure Automation-konto klickar du på **moduler**, sedan **bläddringsgalleriegenskapen**.

2. I sökfältet söker du efter **SqlServer**.

    ![Sök moduler](./media/analysis-services-refresh-azure-automation/1.png)

3. SqlServer och sedan klicka på **Import**.
 
    ![Importera modul](./media/analysis-services-refresh-azure-automation/2.png)

4. Klicka på **OK**.
 
### <a name="create-a-service-principal-spn"></a>Skapa tjänstens huvudnamn (SPN)

Läs om hur du skapar ett huvudnamn för tjänsten i [skapa ett huvudnamn för tjänsten med hjälp av Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurera behörigheter i Azure Analysis Services
 
Tjänstens huvudnamn som du skapar måste ha administratörsbehörighet för servern på servern. Mer information finns i [lägga till tjänstens huvudnamn till administratörsrollen för servern](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Utforma Azure Automation-Runbook

1. I Automation-konto, skapar en **autentiseringsuppgifter** resurs som ska användas för att på ett säkert sätt lagra tjänstens huvudnamn.

    ![Skapa autentiseringsuppgifter](./media/analysis-services-refresh-azure-automation/6.png)

2. Ange information för autentiseringsuppgifter.  För den **användarnamn**, ange den **SPN ClientId**, för den **lösenord**, ange den **SPN hemlighet**.

    ![Skapa autentiseringsuppgifter](./media/analysis-services-refresh-azure-automation/7.png)

3. Importera Automation-Runbook

    ![Importera Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Bläddra efter den **uppdatering Model.ps1** fil, ange en **namn** och **beskrivning**, och klicka sedan på **skapa**.

    ![Importera Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. När Runbooken har skapats, hamnar den automatiskt i redigeringsläge.  Välj **Publicera**.

    ![Publicera Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Autentiseringsuppgifter resursen som skapades tidigare hämtas av runbook med hjälp av den **Get-AutomationPSCredential** kommando.  Det här kommandot skickas sedan till den **Invoke-ProcessASADatabase** PowerShell-kommando för att utföra autentiseringen till Azure Analysis Services.

6. Testa runbooken genom att klicka på **starta**.

    ![Starta Runbooken](./media/analysis-services-refresh-azure-automation/11.png)

7. Fyll i **DATABASENAME**, **ANALYSISSERVER**, och **REFRESHTYPE** parametrar och klicka sedan på **OK**. Den **WEBHOOKDATA** parametern krävs inte när denna Runbook körs manuellt.

    ![Starta Runbooken](./media/analysis-services-refresh-azure-automation/12.png)

Om Runbook har körts visas utdata som liknar följande:

![Lyckad körning](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Använda en fristående Azure Automation-Runbook

Runbook kan konfigureras för att utlösa Azure Analysis Services-modellen uppdatera enligt ett schema.

Detta kan konfigureras på följande sätt:

1. I Automation-Runbook klickar du på **scheman**, sedan **lägga till ett schema**.
 
    ![Skapa ett schema](./media/analysis-services-refresh-azure-automation/14.png)

2. Klicka på **schema** > **skapa ett nytt schema**, och sedan fylla i information.

    ![Konfigurera schema](./media/analysis-services-refresh-azure-automation/15.png)

3. Klicka på **Skapa**.

4. Fyll i parametrar för schemat. Dessa kommer att användas varje gång som utlöser Runbooken. Den **WEBHOOKDATA** parameter ska vara tomt när du kör via ett schema.

    ![Konfigurera parametrar](./media/analysis-services-refresh-azure-automation/16.png)

5. Klicka på **OK**.

## <a name="consume-with-data-factory"></a>Använd med Data Factory

Om du vill använda en runbook med hjälp av Azure Data Factory, först skapa en **Webhook** för runbook. Den **Webhook** ger en URL som kan anropas via en webbaktivitet för Azure Data Factory.

> [!IMPORTANT]
> Skapa en **Webhook**, status på Runbook måste vara **publicerad**.

1. I ditt Automation-Runbook, klickar du på **Webhooks**, och klicka sedan på **Lägg till Webhook**.

   ![Lägg till Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Ge Webhooken ett namn och ett förfallodatum.  Namnet identifierar bara Webhook i Automation-Runbook, den utgör inte en del av URL: en.

   >[!CAUTION]
   >Se till att du kopiera URL: en innan du stänger guiden som du inte få tillbaka en gång stängd.
    
   ![Konfigurera Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Parametrarna för webhooken kan vara tomt.  När du konfigurerar Azure Data Factory-webbaktiviteten, kan parametrarna skickas i brödtexten i web-anropet.

3. I Data Factory, konfigurerar en **webb-aktivitet**

### <a name="example"></a>Exempel

   ![Exempel Web-aktivitet](./media/analysis-services-refresh-azure-automation/19.png)

Den **URL** är den URL som skapats från Webhooken.

Den **brödtext** är ett JSON-dokument som ska innehålla följande egenskaper:


|Egenskap   |Värde  |
|---------|---------|
|**AnalysisServicesDatabase**     |Namnet på Azure Analysis Services-databasen <br/> Exempel: AdventureWorksDB         |
|**AnalysisServicesServer**     |Azure Analysis Services-servernamnet. <br/> Exempel: https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ av uppdatering för att utföra. <br/> Exempel: Fullständig         |

Exempel-JSON-texten:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Dessa parametrar definieras i runbook PowerShell-skript.  När webbaktiviteten körs är JSON-nyttolasten skickas WEBHOOKDATA.

Detta är avserialiseras och lagras som PowerShell-parametrar som används sedan av Invoke-ProcesASDatabase PowerShell-kommando.

![Avserialiserade Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Använda en Hybrid Worker med Azure Analysis Services

En Azure-dator med en statisk offentlig IP-adress kan användas som en Azure Automation Hybrid Worker.  Den här offentliga IP-adressen kan sedan läggas till i Azure Analysis Services-brandväggen.

> [!IMPORTANT]
> Se till att den offentliga IP-adressen för virtuell dator är konfigurerad som statisk.
>
>Läs mer om att konfigurera Azure Automation Hybrid Worker-arbeten i [automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

När du har konfigurerat en Hybrid Worker, skapa en Webhook som beskrivs i avsnittet [förbruka med Data Factory](#consume-with-data-factory).  Den enda skillnaden är att välja den **körs på** > **Hybrid Worker** alternativ när du konfigurerar Webhooken.

Exempel webhook med hjälp av Hybrid Worker:

![Exempel Hybrid Worker-Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exempel på PowerShell-Runbook

Följande kodavsnitt är ett exempel på hur du utför Azure Analysis Services-modelluppdatering med hjälp av en PowerShell-Runbook.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Nästa steg

[Exempel](analysis-services-samples.md)  
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)
