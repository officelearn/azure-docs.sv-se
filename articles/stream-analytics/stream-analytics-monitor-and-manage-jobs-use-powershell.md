---
title: Övervaka och hantera Azure Stream Analytics-jobb med PowerShell
description: I den här artikeln beskrivs hur du använder Azure PowerShell och cmdlets för att övervaka och hantera Azure Stream Analytics-jobb.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 7781f35fe7c17e4a0f307f559945caf648b23f6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431706"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Övervaka och hantera Stream Analytics-jobb med Azure PowerShell-cmdlets
Lär dig hur du övervakar och hanterar Stream Analytics-resurser med Azure PowerShell-cmdlets och powershell-skript som kör grundläggande Stream Analytics-uppgifter.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Förutsättningar för att köra Azure PowerShell-cmdlets för Stream Analytics
* Skapa en Azure Resource Group i din prenumeration. Följande är ett exempel på Azure PowerShell-skript. Information om Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview);  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription �SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Stream Analytics jobb som skapats programmässigt inte har övervakning aktiverat som standard.  Du kan manuellt aktivera övervakning i Azure Portal genom att navigera till jobbets övervaka sida och klicka på knappen Aktivera eller så kan du göra det programmässigt genom att följa stegen i [Azure Stream Analytics - Monitor Stream Analytics Jobs Programmatically](stream-analytics-monitor-jobs.md).
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-cmdlets för Stream Analytics
Följande Azure PowerShell-cmdlets kan användas för att övervaka och hantera Azure Stream Analytics-jobb. Observera att Azure PowerShell har olika versioner. 
**I exemplen i listan är det första kommandot för Azure PowerShell 0.9.8, det andra kommandot är för Azure PowerShell 1.0.** Azure PowerShell 1.0-kommandona har alltid "Az" i kommandot.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Visar alla Stream Analytics-jobb som definierats i Azure-prenumerationen eller den angivna resursgruppen, eller hämtar jobbinformation om ett visst jobb inom en resursgrupp.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Det här PowerShell-kommandot returnerar information om alla Stream Analytics-jobb i Azure-prenumerationen.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Det här PowerShell-kommandot returnerar information om alla Stream Analytics-jobb i resursgruppen StreamAnalytics-Default-Central-US.

**Exempel 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Det här PowerShell-kommandot returnerar information om Stream Analytics-jobbet StreamingJob i resursgruppen StreamAnalytics-Default-Central-US.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Hämta AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Visar alla indata som har definierats i ett angivet Stream Analytics-jobb eller hämtar information om en viss indata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Det här PowerShell-kommandot returnerar information om alla indata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name EntryStream
```

Det här PowerShell-kommandot returnerar information om indata med namnet EntryStream som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Hämta AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Visar alla utdata som har definierats i ett angivet Stream Analytics-jobb eller hämtar information om en viss utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Det här PowerShell-kommandot returnerar information om de utdata som definierats i jobbet StreamingJob.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name Output
```

Det här PowerShell-kommandot returnerar information om utdata med namnet Utdata som definierats i jobbet StreamingJob.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Hämtar information om kvoten för strömningsenheter i ett angivet område.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota �Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota �Location "Central US" 
```

Det här PowerShell-kommandot returnerar information om kvoten och användningen av strömningsenheter i regionen Centrala USA.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Hämtar information om en viss omvandling som definierats i ett Stream Analytics-jobb.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �Name StreamingJob
```

Det här PowerShell-kommandot returnerar information om omvandlingen streamingjob i jobbet StreamingJob.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>Ny-AzureStreamAnalyticsInput | Ny-AzStreamAnalyticsInput
Skapar en ny indata i ett Stream Analytics-jobb eller uppdaterar en befintlig angiven indata.

Namnet på indata kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara detsamma som namnet i filen.

Om du anger en indata som redan finns och inte anger parametern Force, frågar cmdleten om den befintliga indata ska ersättas eller inte.

Om du anger parametern Force och anger ett befintligt indatanamn ersätts indata utan bekräftelse.

Detaljerad information om JSON-filstrukturen och innehållet finns i avsnittet [Skapa indata (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] i [referensbiblioteket för STREAM Analytics Management REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" 
```

Med det här PowerShell-kommandot skapas en ny indata från filen Input.json. Om en befintlig indata med det namn som anges i indatadefinitionsfilen redan har definierats, frågar cmdleten om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream
```

Det här PowerShell-kommandot skapar en ny indata i jobbet Som heter EntryStream. Om en befintlig indata med det här namnet redan har definierats, frågar cmdleten om den ska ersättas eller inte.

**Exempel 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob �File "C:\Input.json" �Name EntryStream -Force
```

Det här PowerShell-kommandot ersätter definitionen av den befintliga indatakällan EntryStream med definitionen från filen.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>Nya AzureStreamAnalyticsJob | Nya AzStreamAnalyticsJob
Skapar ett nytt Stream Analytics-jobb i Microsoft Azure eller uppdaterar definitionen av ett befintligt angivet jobb.

Namnet på jobbet kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara detsamma som namnet i filen.

Om du anger ett jobbnamn som redan finns och inte anger parametern Tvinga, frågar cmdlet om det befintliga jobbet ska ersättas eller inte.

Om du anger parametern Tvinga och anger ett befintligt jobbnamn ersätts jobbdefinitionen utan bekräftelse.

Detaljerad information om JSON-filstrukturen och innehållet finns i avsnittet [Skapa Stream Analytics-jobb][msdn-rest-api-create-stream-analytics-job] i [referensbiblioteket för STREAM Analytics Management REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" 
```

Med det här PowerShell-kommandot skapas ett nytt jobb från definitionen i JobDefinition.json. Om ett befintligt jobb med det namn som anges i jobbdefinitionsfilen redan har definierats, frågar cmdleten om det ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\JobDefinition.json" �Name StreamingJob -Force
```

Det här PowerShell-kommandot ersätter jobbdefinitionen för StreamingJob.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>Ny AzureStreamAnalyticsOutput | Ny-AzStreamAnalyticsOutput
Skapar ett nytt utdata i ett Stream Analytics-jobb eller uppdaterar ett befintligt utdata.  

Namnet på utdata kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara detsamma som namnet i filen.

Om du anger ett utdata som redan finns och inte anger parametern Force, frågar cmdleten om den befintliga utdata ska ersättas eller inte.

Om du anger parametern Force och anger ett befintligt utdatanamn ersätts utdata utan bekräftelse.

Detaljerad information om JSON-filstrukturen och innehållet finns i avsnittet [Skapa utdata (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] i [referensbiblioteket för STREAM Analytics Management REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output
```

Detta PowerShell-kommando skapar en ny utdata som kallas "output" i jobbet StreamingJob. Om en befintlig utdata med det här namnet redan har definierats, frågar cmdleten om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Output.json" �JobName StreamingJob �Name output -Force
```

Det här PowerShell-kommandot ersätter definitionen för "utdata" i jobbet StreamingJob.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>Ny-AzureStreamAnalyticsTransformation | Ny-AzStreamAnalyticsTransformation
Skapar en ny omvandling inom ett Stream Analytics-jobb eller uppdaterar den befintliga omvandlingen.

Namnet på omvandlingen kan anges i .json-filen eller på kommandoraden. Om båda anges måste namnet på kommandoraden vara detsamma som namnet i filen.

Om du anger en omvandling som redan finns och inte anger parametern Force, frågar cmdlet om den befintliga omvandlingen ska ersättas eller inte.

Om du anger parametern Force och anger ett befintligt omvandlingsnamn ersätts omvandlingen utan bekräftelse.

Detaljerad information om JSON-filstrukturen och innehållet finns i avsnittet [Skapa omvandling (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] i [referensbiblioteket för STREAM Analytics Management REST API][stream.analytics.rest.api.reference].

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform
```

Det här PowerShell-kommandot skapar en ny omvandling som kallas StreamingJobTransform i jobbet StreamingJob. Om en befintlig omvandling redan har definierats med det här namnet, frågar cmdlet om den ska ersättas eller inte.

**Exempel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US �File "C:\Transformation.json" �JobName StreamingJob �Name StreamingJobTransform -Force
```

 Det här PowerShell-kommandot ersätter definitionen av StreamingJobTransform i jobbet StreamingJob.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Ta bort AzureStreamAnalyticsInput | Ta bort-AzStreamAnalyticsInput
Tar asynkront bort en specifik indata från ett Stream Analytics-jobb i Microsoft Azure.  
Om du anger parametern Force tas indata bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EventStream
```

Det här PowerShell-kommandot tar bort indata EventStream i jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Ta bort AzureStreamAnalyticsJob | Ta bort-AzStreamAnalyticsJob
Tar asynkront bort ett specifikt Stream Analytics-jobb i Microsoft Azure.  
Om du anger parametern Force tas jobbet bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Det här PowerShell-kommandot tar bort jobbet StreamingJob.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Ta bort AzureStreamAnalyticsOutput | Ta bort-AzStreamAnalyticsOutput
Tar asynkront bort en specifik utdata från ett Stream Analytics-jobb i Microsoft Azure.  
Om du anger parametern Force tas utdata bort utan bekräftelse.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Det här PowerShell-kommandot tar bort utdatautdata i jobbet StreamingJob.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Asynkront distribuerar och startar ett Stream Analytics-jobb i Microsoft Azure.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Det här PowerShell-kommandot startar jobbet StreamingJob med en anpassad starttid för utdata inställd på 12 december 2012, 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stopp-AzStreamAnalyticsJob
Asynkront stoppar ett Stream Analytics-jobb från att köras i Microsoft Azure och avallokerar resurser som användes. Jobbdefinitionen och metadata förblir tillgängliga i din prenumeration via både Azure-portalen och hanterings-API:erna, så att jobbet kan redigeras och startas om. Du debiteras inte för ett jobb i stoppat tillstånd.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US �Name StreamingJob 
```

Det här PowerShell-kommandot stoppar jobbet StreamingJob.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testar möjligheten för Stream Analytics att ansluta till en angiven indata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name EntryStream
```

Det här PowerShell-kommandot testar anslutningsstatusen för indata EntryStream i StreamingJob.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testar möjligheten för Stream Analytics att ansluta till en angiven utdata.

**Exempel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US �JobName StreamingJob �Name Output
```

Det här PowerShell-kommandot testar anslutningsstatusen för utdatautdata i StreamingJob.  

## <a name="get-support"></a>Få support
Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

[msdn-switch-azuremode]: https://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

