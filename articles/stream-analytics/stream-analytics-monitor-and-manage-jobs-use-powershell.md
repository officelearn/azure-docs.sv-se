---
title: Övervaka och hantera Azure Stream Analytics-jobb med hjälp av PowerShell
description: Den här artikeln beskriver hur du använder Azure PowerShell och cmdlets för att övervaka och hantera Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 61c26c55725c19f526680d70f3621d41e9590965
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31600918"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Övervaka och hantera Stream Analytics-jobb med Azure PowerShell-cmdlets
Lär dig mer om att övervaka och hantera Stream Analytics-resurser med Azure PowerShell-cmdlets och powershell-skript som kör grundläggande Stream Analytics-aktiviteter.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Förutsättningar för att köra Azure PowerShell-cmdlets för Stream Analytics
* Skapa en Azure-resursgrupp i din prenumeration. Följande är ett exempel Azure PowerShell-skript. Azure PowerShell information finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview);  

Azure PowerShell 0.9.8:  

         # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

         # Log in to your Azure account
        Connect-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>



> [!NOTE]
> Stream Analytics-jobb som skapats inte övervaka aktiverad som standard.  Du kan aktivera övervakning i Azure Portal genom att gå till jobbets övervakaren sidan och klicka på knappen Aktivera eller du kan göra detta via programmering genom att följa stegen i [Azure Stream Analytics - övervakaren Stream Analytics-jobb genom att programmera](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-cmdlets för Stream Analytics
Följande Azure PowerShell-cmdlets kan användas för att övervaka och hantera Azure Stream Analytics-jobb. Observera att Azure PowerShell har olika versioner. 
**I exemplen som visas är det första kommandot för Azure PowerShell 0.9.8, det andra kommandot avser Azure PowerShell 1.0.** Azure PowerShell 1.0-kommandon har alltid ”AzureRM” i kommandot.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Visar en lista över alla Stream Analytics-jobb som definierats i Azure-prenumeration eller angivna resursgruppen eller hämtar jobbinformation om ett specifikt jobb inom en resursgrupp.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Detta PowerShell-kommando returnerar information om Stream Analytics-jobb i Azure-prenumeration.

**Exempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Detta PowerShell-kommando returnerar information om Stream Analytics-jobb i resursgruppen StreamAnalytics-standard-Central-US.

**Exempel 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Detta PowerShell-kommando returnerar information om Stream Analytics-jobbet StreamingJob i resursgruppen StreamAnalytics-standard-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Hämtar information om specifika indata eller en lista över alla indata som definieras i en angiven Stream Analytics-jobbet.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Detta PowerShell-kommando returnerar information om alla indata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Detta PowerShell-kommando returnerar information om indata med namnet EntryStream som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
En lista över alla utdata som definieras i en angiven Stream Analytics-jobbet eller hämtar information om en specifik utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Detta PowerShell-kommando returnerar information om de utdata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Detta PowerShell-kommando returnerar information om utdata med namnet utdata som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Hämtar information om kvoten för strömmande enheter i ett visst område.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Detta PowerShell-kommando returnerar information om kvoten och användning av enheter för strömning i centrala USA region.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Hämtar information om en omvandling definieras i ett Stream Analytics-jobb.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Detta PowerShell-kommando returnerar information om omvandlingen kallas StreamingJob i StreamingJob-jobbet.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Nya AzureStreamAnalyticsInput | Ny AzureRMStreamAnalyticsInput
Skapar en ny inmatning inom ett Stream Analytics-jobb eller uppdaterar befintliga angivna indata.

Namnet på indata kan anges i JSON-fil eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som den i filen.

Om du anger indata som redan finns och inte anger Force-parametern, cmdlet kommer att fråga om att ersätta befintliga indata eller inte.

Om du anger – tvinga parametern och ange en befintlig ange namn, indata ersätts utan bekräftelse.

Detaljerad information om JSON struktur och innehåll som avser den [skapa indata (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] avsnitt i den [Stream Analytics Management REST API-referensbiblioteket][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Detta PowerShell-kommando skapar en ny indata från Input.json-filen. Om en befintlig indata med namnet som angetts i definitionsfilen för indata har redan definierats, ber cmdlet om du vill ersätta det eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Detta PowerShell-kommando skapar en ny inmatning i jobbet kallas EntryStream. Om befintliga indata med det här namnet har redan definierats, frågar cmdlet om du vill ersätta det eller inte.

**Exempel 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Detta PowerShell-kommando ersätter definitionen av befintliga Indatakällan med namnet EntryStream och definitionen från filen.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Nya AzureStreamAnalyticsJob | Ny AzureRMStreamAnalyticsJob
Skapar ett nytt Stream Analytics-jobb i Microsoft Azure eller uppdaterar definitionen av ett befintligt angivna jobb.

Namnet på jobbet kan anges i JSON-fil eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som den i filen.

Om du anger ett namn som redan finns och inte anger Force-parametern, cmdlet kommer att fråga om att ersätta det befintliga projektet eller inte.

Om du anger – framtvinga parametern och ange Jobbnamnet på en befintlig jobbdefinitionen ersätts utan bekräftelse.

Detaljerad information om JSON struktur och innehåll som avser den [skapa Stream Analytics-jobbet] [ msdn-rest-api-create-stream-analytics-job] avsnitt i den [Stream Analytics Management REST API-referensbiblioteket][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Detta PowerShell-kommando skapar ett nytt jobb från definitionen i JobDefinition.json. Om ett befintligt jobb med namnet som angetts i definitionsfilen för jobbet har redan definierats, frågar cmdlet om du vill ersätta det eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Detta PowerShell-kommando ersätter jobbdefinitionen för StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Nya AzureStreamAnalyticsOutput | Ny AzureRMStreamAnalyticsOutput
Skapar en ny utdata inom ett Stream Analytics-jobb eller uppdaterar befintliga utdata.  

Namnet på utdata kan anges i JSON-fil eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som den i filen.

Om du anger ett utgående som redan finns och inte anger Force-parametern, cmdlet kommer att fråga om att ersätta befintliga utdata eller inte.

Om du anger – tvinga parametern och ange en befintlig utdata namn, ersätts utdatan utan bekräftelse.

Detaljerad information om JSON struktur och innehåll som avser den [skapa utdata (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] avsnitt i den [Stream Analytics Management REST API-referensbiblioteket][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Detta PowerShell-kommando skapar en ny utdata som kallas ”utdata” i StreamingJob-jobbet. Om en befintlig utdata med det här namnet används redan frågar cmdlet om att ersätta det eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Detta PowerShell-kommando ersätter du definitionen för ”utdata” i StreamingJob-jobbet.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Nya AzureStreamAnalyticsTransformation | Ny AzureRMStreamAnalyticsTransformation
Skapar en ny omvandling inom ett Stream Analytics-jobb eller uppdaterar befintliga omvandling.

Namnet på omvandlingen kan anges i JSON-fil eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som den i filen.

Om du anger en omvandling som redan finns och inte anger Force-parametern, cmdlet kommer att fråga om att ersätta den befintliga omvandlingen eller inte.

Om du anger – framtvinga parametern och ange namnet på en befintlig omvandling omvandlingen ersätts utan bekräftelse.

Detaljerad information om JSON struktur och innehåll som avser den [skapa omvandling (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] avsnitt i den [Stream Analytics Management REST API-referensbiblioteket][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Detta PowerShell-kommando skapar en ny omvandling kallas StreamingJobTransform i StreamingJob-jobbet. Om en befintlig transformation har redan definierats med samma namn, frågar cmdlet om du vill ersätta det eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Detta PowerShell-kommando ersätter definitionen av StreamingJobTransform i StreamingJob-jobbet.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Ta bort AzureStreamAnalyticsInput | Ta bort AzureRMStreamAnalyticsInput
Tar bort specifika indata asynkront från en Stream Analytics-jobbet i Microsoft Azure.  
Om du anger Force-parametern, kommer att tas bort indata utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Detta PowerShell-kommando bort inkommande EventStream i jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Ta bort AzureStreamAnalyticsJob | Ta bort AzureRMStreamAnalyticsJob
Tar bort ett specifikt Stream Analytics-jobb i Microsoft Azure asynkront.  
Om du anger Force-parametern, kommer att ta bort jobbet utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Det här PowerShell-kommandot tar bort jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Ta bort AzureStreamAnalyticsOutput | Ta bort AzureRMStreamAnalyticsOutput
Tar bort en specifik utdata från ett Stream Analytics-jobb i Microsoft Azure asynkront.  
Om du anger Force-parametern, utdata kommer att tas bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Det här PowerShell-kommandot tar bort utdata utdata i jobbet StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Asynkront distribuerar och startar en Stream Analytics-jobb i Microsoft Azure.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Detta PowerShell-kommando startar jobbet StreamingJob med anpassade utdata starttid inställd på 12 December 2012 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stoppa AzureStreamAnalyticsJob | Stoppa AzureRMStreamAnalyticsJob
Asynkront stoppar ett Stream Analytics-jobb körs i Microsoft Azure och frigör resurser som var som användes. Jobbdefinitionen och metadata förblir tillgängliga i din prenumeration via både Azure-portalen och management API: er, så att jobbet kan redigeras och startas om. Du debiteras inte för ett jobb i ett stoppat tillstånd.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Det här PowerShell-kommandot stoppar jobbet StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Testa AzureStreamAnalyticsInput | Testa AzureRMStreamAnalyticsInput
Testar Stream Analytics förmåga att ansluta till en angivna indata.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Det här PowerShell-kommandot testar anslutningsstatusen för inkommande EntryStream i StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Testa AzureStreamAnalyticsOutput | Testa AzureRMStreamAnalyticsOutput
Testar Stream Analytics förmåga att ansluta till en angiven utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Det här PowerShell-kommandot tester anslutningsstatusen för utdata utdata i StreamingJob.  

## <a name="get-support"></a>Få support
För ytterligare hjälp försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

