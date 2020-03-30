---
title: Uppdatera Azure Analysis Services-modeller med Azure Automation | Microsoft-dokument
description: I den här artikeln beskrivs hur du kodar uppdateringar för modeller för Azure Analysis Services med hjälp av Azure Automation.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: a79123d57f80474e1871ef68f9a92ea9417089ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572354"
---
# <a name="refresh-with-azure-automation"></a>Uppdatera med Azure Automation

Genom att använda Azure Automation och PowerShell Runbooks kan du utföra automatiska datauppdateringsåtgärder på dina Azure Analysis-tabellmodeller.  

I exemplet i den här artikeln används [PowerShell SqlServer-modulerna](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps).

Ett exempel på PowerShell Runbook, som visar att en modell uppdateras, finns senare i den här artikeln.  

## <a name="authentication"></a>Autentisering

Alla anrop måste autentiseras med en giltig Azure Active Directory -token (OAuth 2).  Exemplet i den här artikeln kommer att använda ett servicehuvudnamn (SPN) för att autentisera till Azure Analysis Services.

Mer information om hur du skapar en tjänsthuvudnamn finns i [Skapa ett huvudnamn för tjänsten med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Krav

> [!IMPORTANT]
> I följande exempel förutsätts att Azure Analysis Services-brandväggen är inaktiverad. Om brandväggen är aktiverad måste den offentliga IP-adressen för begärandeinitieraren vitlistas i brandväggen.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installera SqlServer-moduler från PowerShell-galleriet.

1. I ditt Azure Automation-konto klickar du på Moduler och **bläddrar**sedan **i galleriet**.

2. Sök efter **SqlServer**i sökfältet .

    ![Sök moduler](./media/analysis-services-refresh-azure-automation/1.png)

3. Välj SqlServer och klicka sedan på **Importera**.
 
    ![Importera modul](./media/analysis-services-refresh-azure-automation/2.png)

4. Klicka på **OK**.
 
### <a name="create-a-service-principal-spn"></a>Skapa ett tjänsthuvudnamn (SPN)

Mer information om hur du skapar en tjänsthuvudnamn finns i [Skapa ett huvudnamn för tjänsten med hjälp av Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurera behörigheter i Azure Analysis Services
 
Den tjänstansvarige som du skapar måste ha serveradministratörsbehörighet på servern. Mer information finns i [Lägga till ett tjänsthuvudnamn i serveradministratörsrollen](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Designa Azure Automation Runbook

1. Skapa en **autentiseringsresurs** som ska användas för att lagra tjänstens huvudnamn på ett säkert sätt.

    ![Skapa autentiseringsuppgifter](./media/analysis-services-refresh-azure-automation/6.png)

2. Ange information om autentiseringsuppgifterna.  För **användarnamnet**anger du **SPN ClientId**för **lösenordet**och anger **SPN-hemligheten**.

    ![Skapa autentiseringsuppgifter](./media/analysis-services-refresh-azure-automation/7.png)

3. Importera Automation Runbook

    ![Importera Runbook](./media/analysis-services-refresh-azure-automation/8.png)

4. Bläddra efter filen **Refresh-Model.ps1,** ange ett **namn** och **en beskrivning**och klicka sedan på **Skapa**.

    ![Importera Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. När Runbook har skapats går den automatiskt in i redigeringsläge.  Välj **Publicera**.

    ![Publicera Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Den autentiseringsresurs som skapades tidigare hämtas av runbooken med kommandot **Get-AutomationPSCredential.**  Det här kommandot skickas sedan till kommandot **Anropa-ProcessASADatabase** PowerShell för att utföra autentiseringen till Azure Analysis Services.

6. Testa runbooken genom att klicka på **Start**.

    ![Starta runbooken](./media/analysis-services-refresh-azure-automation/11.png)

7. Fyll i parametrarna **DATABASNAMN,** **ANALYSISSERVER**och **REFRESHTYPE** och klicka sedan på **OK**. Parametern **WEBHOOKDATA** krävs inte när Runbook körs manuellt.

    ![Starta runbooken](./media/analysis-services-refresh-azure-automation/12.png)

Om Runbooken har körts får du en utdata som följande:

![Lyckad körning](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Använda en fristående Azure Automation Runbook

Runbook kan konfigureras för att utlösa Azure Analysis Services-modellen uppdateras på schemalagd basis.

Detta kan konfigureras på följande sätt:

1. Klicka på **Scheman**i Automation Runbook och lägg sedan **till ett schema**.
 
    ![Skapa schema](./media/analysis-services-refresh-azure-automation/14.png)

2. Klicka på **Schema** > **Skapa ett nytt schema**och fyll sedan i informationen.

    ![Konfigurera schema](./media/analysis-services-refresh-azure-automation/15.png)

3. Klicka på **Skapa**.

4. Fyll i parametrarna för schemat. Dessa används varje gång Runbook utlöses. Parametern **WEBHOOKDATA** ska lämnas tom när du kör via ett schema.

    ![Konfigurera parametrar](./media/analysis-services-refresh-azure-automation/16.png)

5. Klicka på **OK**.

## <a name="consume-with-data-factory"></a>Förbruka med Data Factory

Om du vill använda runbooken med hjälp av Azure Data Factory skapar du först en **Webhook** för runbooken. **Webhook** tillhandahåller en URL som kan anropas via en Azure Data Factory-webbaktivitet.

> [!IMPORTANT]
> Om du vill skapa en **Webhook**måste statusen för **Runbooken publiceras**.

1. Klicka på **Webhooks**i Automation Runbook och klicka sedan på **Lägg till Webhook**.

   ![Lägg till Webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Ge Webhook ett namn och en utgång.  Namnet identifierar bara Webhook i Automation Runbook, det utgör inte en del av webbadressen.

   >[!CAUTION]
   >Se till att du kopierar webbadressen innan du stänger guiden eftersom du inte kan få tillbaka den när den är stängd.
    
   ![Konfigurera Webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Parametrarna för webhook kan förbli tomma.  När du konfigurerar webbaktiviteten för Azure Data Factory kan parametrarna skickas till webbanropet.

3. Konfigurera en **webbaktivitet i** Data Factory

### <a name="example"></a>Exempel

   ![Exempel på webbaktivitet](./media/analysis-services-refresh-azure-automation/19.png)

**URL:en** är url:en som skapats från Webhook.

**Brödtexten** är ett JSON-dokument som ska innehålla följande egenskaper:


|Egenskap  |Värde  |
|---------|---------|
|**AnalysisServicesDatabase**     |Namnet på Azure Analysis Services-databasen <br/> Exempel: AdventureWorksDB         |
|**AnalysisServicesServer**     |Servernamnet för Azure Analysis Services. <br/> Exempel: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabasRefreshType**     |Den typ av uppdatering som ska utföras. <br/> Exempel: Fullständig         |

Exempel JSON kropp:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Dessa parametrar definieras i powershell-skriptet runbook.  När webbaktiviteten körs är den skickade JSON-nyttolasten WEBHOOKDATA.

Detta är deserialiserat och lagrat som PowerShell-parametrar, som sedan används av kommandot Anropa-ProcesASDatabase PowerShell.

![Deserialiserade Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Använda en hybridarbetare med Azure Analysis Services

En virtuell Azure-dator med en statisk offentlig IP-adress kan användas som en Azure Automation Hybrid Worker.  Den här offentliga IP-adressen kan sedan läggas till i Azure Analysis Services-brandväggen.

> [!IMPORTANT]
> Kontrollera att den offentliga IP-adressen för den virtuella datorn är konfigurerad som statisk.
>
>Mer information om hur du konfigurerar Azure Automation Hybrid Workers finns [i Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

När en hybridarbetare har konfigurerats skapar du en Webhook enligt beskrivningen i avsnittet [Förbruka med datafabriken](#consume-with-data-factory).  Den enda skillnaden här är att välja alternativet **Kör på** > **hybridarbetare** när du konfigurerar Webhook.

Exempel webhook med hybridarbetare:

![Exempel på hybridarbetares webbkrok](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Exempel på PowerShell-runbook

Följande kodavsnitt är ett exempel på hur du utför uppdateringen av Azure Analysis Services-modellen med hjälp av en PowerShell Runbook.

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

[Prover](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
