---
title: Övervaka och hantera Azure Stream Analytics-jobb med hjälp av PowerShell
description: Den här artikeln beskriver hur du använder Azure PowerShell och cmdletar för att övervaka och hantera Azure Stream Analytics-jobb.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 6812298bc90446a7b7e136ec81a67641c2a9e18f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43701616"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Övervaka och hantera Stream Analytics-jobb med Azure PowerShell-cmdlets
Lär dig mer om att övervaka och hantera Stream Analytics-resurser med Azure PowerShell-cmdlets och powershell-skript som kör grundläggande Stream Analytics-uppgifter.

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Förutsättningar för att köra Azure PowerShell-cmdlets för Stream Analytics
* Skapa en Azure-resursgrupp i din prenumeration. Följande är ett exempelskript för Azure PowerShell. Azure PowerShell information finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview);  

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
        Get-AzureRmSubscription –SubscriptionName "your sub" | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>



> [!NOTE]
> Stream Analytics-jobb som skapats via programmering behöver inte övervaka aktiverad som standard.  Du kan aktivera övervakning i Azure Portal genom att gå till övervakaren jobbsidan och klicka på Aktivera-knappen eller du kan göra detta via programmering genom att följa stegen i [Azure Stream Analytics - Övervakare för Stream Analytics-jobb Programmässigt](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-cmdlets för Stream Analytics
Följande Azure PowerShell-cmdletar kan användas för att övervaka och hantera Azure Stream Analytics-jobb. Observera att Azure PowerShell har olika versioner. 
**I exemplen som visas är det första kommandot för Azure PowerShell 0.9.8, det andra kommandot är för Azure PowerShell 1.0.** Azure PowerShell 1.0-kommandon har alltid ”AzureRM” i kommandot.

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzureRMStreamAnalyticsJob
Visar en lista över alla Stream Analytics-jobb som definierats i Azure-prenumeration eller angivna resursgruppen eller hämtar jobbinformation om om ett specifikt jobb i en resursgrupp.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

Det här PowerShell-kommandot returnerar information om alla Stream Analytics-jobb i Azure-prenumeration.

**Exempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Det här PowerShell-kommandot returnerar information om alla Stream Analytics-jobb i resursgruppen StreamAnalytics-standard-Central-US.

**Exempel 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Det här PowerShell-kommandot returnerar information om Stream Analytics-jobbet StreamingJob i resursgruppen StreamAnalytics-standard-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzureRMStreamAnalyticsInput
Visar en lista över alla indata som definieras i ett angivet Stream Analytics-jobb eller hämtar information om specifika indata.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Det här PowerShell-kommandot returnerar information om alla de indata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Det här PowerShell-kommandot returnerar information om indata med namnet EntryStream som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzureRMStreamAnalyticsOutput
Visar en lista över alla utdata som definieras i ett angivet Stream Analytics-jobb eller hämtar information om en specifik utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Det här PowerShell-kommandot returnerar information om de utdata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Det här PowerShell-kommandot returnerar information om utdata med namnet utdata som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzureRMStreamAnalyticsQuota
Hämtar information om kvoten för strömningsenheterna i en specifik region.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

Det här PowerShell-kommandot returnerar information om kvot och användning av enheter för strömning i regionen USA, centrala.

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | GetAzureRMStreamAnalyticsTransformation
Hämtar information om en omvandling som definierats i ett Stream Analytics-jobb.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Det här PowerShell-kommandot returnerar information om omvandlingen kallas StreamingJob i jobbet StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>Ny AzureStreamAnalyticsInput | Ny AzureRMStreamAnalyticsInput
Skapar en ny indata inom ett Stream Analytics-jobb eller uppdaterar en befintlig angivna indata.

Namnet på indata kan anges i JSON-fil eller från kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger indata som redan finns och att du inte anger parametern – Force, cmdlet: en får du frågan om att ersätta befintliga indata eller inte.

Om du anger – tvinga parametern och ange en befintlig ange namn, kommer att ersättas indata utan bekräftelse.

Detaljerad information på strukturen för JSON-fil och innehållet i den [skapa indata (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-input] delen av den [Stream Analytics Management REST API-referens Biblioteket][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Det här PowerShell-kommando skapar en ny indata från Input.json-filen. Om en befintlig indata med namnet som angetts i definitionsfilen för indata har redan definierats, ber cmdleten huruvida du ersätta den.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Det här PowerShell-kommando skapar en ny indata i de jobb som heter EntryStream. Om en befintlig indata med det här namnet har redan definierats, ber cmdleten huruvida du ersätta den.

**Exempel 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Det här PowerShell-kommandot ersätter definitionen av befintliga Indatakällan kallas EntryStream med definitionen från filen.

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>Ny AzureStreamAnalyticsJob | Ny-AzureRMStreamAnalyticsJob
Skapar ett nytt Stream Analytics-jobb i Microsoft Azure eller uppdaterar definitionen av ett befintligt angivna jobb.

Namnet på jobbet kan anges i JSON-fil eller från kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger ett Jobbnamn som redan finns och inte anger parametern – Force, cmdlet: en får du frågan om att ersätta det befintliga jobbet eller inte.

Om du anger – framtvinga parametern och ange ett befintligt jobbnamn jobbdefinitionen ersätts utan bekräftelse.

Detaljerad information på strukturen för JSON-fil och innehållet i den [skapa Stream Analytics-jobbet] [ msdn-rest-api-create-stream-analytics-job] delen av den [Stream Analytics Management REST API Reference Library] [stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Det här PowerShell-kommando skapar ett nytt jobb från definitionen i JobDefinition.json. Om ett befintligt projekt med namnet som angetts i definitionsfilen för jobbet har redan definierats, ber cmdleten huruvida du ersätta den.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Det här PowerShell-kommandot ersätter jobbdefinitionen för StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>Ny AzureStreamAnalyticsOutput | Ny AzureRMStreamAnalyticsOutput
Skapar en ny utdatakanal inom ett Stream Analytics-jobb eller uppdaterar en befintlig utdata.  

Namnet på utdata kan anges i JSON-fil eller från kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger utdata som redan finns och att du inte anger parametern – Force, cmdlet: en får du frågan om att ersätta befintliga utdata eller inte.

Om du anger – tvinga parametern och ange en befintlig utdata namn, utdata kommer att ersättas utan bekräftelse.

Detaljerad information på strukturen för JSON-fil och innehållet i den [skapa utdata (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-output] delen av den [Stream Analytics Management REST API-referens Biblioteket][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Det här PowerShell-kommando skapar en ny utdatakanal som kallas ”utdata” i jobbet StreamingJob. Om en befintlig utdata med det här namnet har redan definierats, ber cmdleten huruvida du ersätta den.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Det här PowerShell-kommando ersätter du definitionen för ”utdata” i jobbet StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>Ny AzureStreamAnalyticsTransformation | Ny AzureRMStreamAnalyticsTransformation
Skapar en ny omvandling inom ett Stream Analytics-jobb eller uppdaterar befintliga transformeringen.

Namnet på omvandlingen kan anges i JSON-fil eller från kommandoraden. Om båda anges måste namnet på kommandoraden vara samma som det i filen.

Om du anger en transformation som redan finns och inte anger parametern – Force, cmdlet: en får du frågan om att ersätta befintliga transformeringen eller inte.

Om du anger – framtvinga parametern och ange ett befintligt namn i omvandling transformeringen ersätts utan bekräftelse.

Detaljerad information på strukturen för JSON-fil och innehållet i den [skapa transformering (Azure Stream Analytics)] [ msdn-rest-api-create-stream-analytics-transformation] delen av den [Stream Analytics Management REST API Referensbiblioteket][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Det här PowerShell-kommando skapar en ny omvandling som kallas StreamingJobTransform i jobbet StreamingJob. Om en befintlig omvandling har redan definierats med det här namnet, ber cmdleten huruvida du ersätta den.

**Exempel 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 Det här PowerShell-kommandot ersätter definitionen av StreamingJobTransform i jobbet StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>Ta bort AzureStreamAnalyticsInput | Remove-AzureRMStreamAnalyticsInput
Tar bort specifika indata asynkront från ett Stream Analytics-jobb i Microsoft Azure.  
Om du anger parametern – Force, kommer att tas bort indata utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Det här PowerShell-kommandot tar bort den inkommande EventStream i jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>Ta bort AzureStreamAnalyticsJob | Remove-AzureRMStreamAnalyticsJob
Asynkront tar du bort ett specifikt Stream Analytics-jobb i Microsoft Azure.  
Om du anger parametern – Force, jobbet kommer att tas bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Det här PowerShell-kommandot tar bort jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>Ta bort AzureStreamAnalyticsOutput | Remove-AzureRMStreamAnalyticsOutput
Tar bort en specifik utdata asynkront från ett Stream Analytics-jobb i Microsoft Azure.  
Om du anger parametern – Force, utdata kommer att tas bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Det här PowerShell-kommandot tar bort utdata utdata i jobbet StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzureRMStreamAnalyticsJob
Asynkront distribuerar och startar ett Stream Analytics-jobb i Microsoft Azure.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Det här PowerShell-kommandot startar jobbet StreamingJob med anpassade utdata starttid som har angetts till 12 December 2012 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>Stoppa AzureStreamAnalyticsJob | Stop-AzureRMStreamAnalyticsJob
Asynkront stoppar ett Stream Analytics-jobb körs i Microsoft Azure och frigör resurser som har som användes. Jobbdefinitionen och metadata är fortsatt tillgängliga i din prenumeration via både Azure-portalen och API: er, hantering, så att jobbet kan redigeras och startas om. Du debiteras inte för ett jobb i ett stoppat tillstånd.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Det här PowerShell-kommando stoppar jobbet StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzureRMStreamAnalyticsInput
Testar möjligheten för Stream Analytics för att ansluta till en angivna indata.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Det här PowerShell-kommandot testar anslutningsstatus för inkommande EntryStream i StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzureRMStreamAnalyticsOutput
Testar möjligheten för Stream Analytics för att ansluta till en angiven utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Det här PowerShell-kommando tester anslutningsstatus för utdata utdata i StreamingJob.  

## <a name="get-support"></a>Få support
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

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

