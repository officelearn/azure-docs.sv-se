---
title: "Använda Batch-API:er och verktyg i Azure för att utveckla lösningar för storskalig parallellbearbetning | Microsoft Docs"
description: "Läs mer om API:er och verktyg som är tillgängliga för utveckling av lösningar med Azure Batch-tjänsten."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 405cb202e2b6da0dd24e6f7b8ed55ce8c56e0fe1
ms.lasthandoff: 03/31/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>Översikt över Batch-API:er och verktyg

Bearbetning av parallella arbetsbelastningar med Azure Batch görs normalt programmässigt med någon av [Batch-API:erna](#batch-development-apis). Ditt klientprogram eller -tjänst kan använda Batch-API:erna för att kommunicera med Batch-tjänsten. Med Batch-API:erna kan du skapa och hantera pooler för beräkningsnoder, antingen virtuella datorer eller molntjänster. Sedan kan du schemalägga jobb och uppgifter som ska köras på dessa noder. 

Du kan effektivt bearbeta storskaliga arbetsbelastningar för din organisation eller tillhandahålla en tjänstportal för dina kunder så att de kan köra jobb och aktiviteter (på begäran eller enligt ett schema) på en eller flera hundra tusen noder. Du kan också använda Azure Batch som en del av ett större arbetsflöde, med verktyg som [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> När du är redo att fördjupa dig i Batch-API:et rekommenderar vi att du börjar med [Batch-funktionsöversikten för utvecklare](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-youll-need"></a>Azure-konton som du behöver
När du utvecklar Batch-lösningar använder du följande konton i Microsoft Azure.

* **Azure-konto och Azure-prenumeration** – Om du inte redan har en Azure-prenumeration kan du aktivera din [MSDN-prenumerantförmån][msdn_benefits] eller registrera dig för ett [kostnadsfritt Azure-konto][free_account]. När du skapar ett konto skapas en standardprenumeration åt dig.
* **Batch-konto** – Azure Batch-resurser, inklusive pooler, beräkningsnoder, jobb och aktiviteter, är associerade med ett Azure Batch-konto. Om programmet frågar mot batchtjänsten, autentiserar det begäran med hjälp av Azure Batch-kontonamnet, URL-adressen för kontot och en åtkomstnyckel. Du kan [skapa ett Batch-konto](batch-account-create-portal.md) i Azure Portal.
* **Storage-konto** – Batch innehåller inbyggt stöd för att arbeta med filer i [Azure Storage][azure_storage]. Nästan alla Batch-scenarier använder Azure Blob Storage, dels för att mellanlagra programmen som dina aktiviteter kör och de data som de bearbetar, dels för att lagra de utdata som de genererar. Om du vill skapa ett Storage-konto läser du [Om Azure Storage-konton](../storage/storage-create-storage-account.md).

## <a name="batch-development-apis"></a>API:er för Batch-utveckling
Dina program och tjänster kan skicka direkta REST-API-anrop eller använda ett eller flera av följande klientbibliotek för att köra och hantera dina Azure Batch-arbetsbelastningar.

| API | API-referens | Ladda ned | Självstudier | Kodexempel | Mer information |
| --- | --- | --- | --- | --- | --- |
| **REST för Batch** |[MSDN][batch_rest] |Saknas |- |- | [Versioner som stöds](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET för Batch** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Självstudie](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Viktig information](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Python för Batch** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Självstudie](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Viktigt](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [Viktigt](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Java för Batch** (förhandsversion) |[github.io][api_java] |[Maven][api_java_jar] |- |[Viktigt][api_sample_java] | [Viktigt](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>Batch kommandoradsverktyg

Funktioner som tillhandahålls av utvecklings-API:er är också tillgängliga med hjälp av kommandoradsverktyg: 

* [PowerShell-cmdlets för Batch ][batch_ps]: Du kan använda Azure Batch-cmdlets i [Azure PowerShell](/powershell/azureps-cmdlets-docs)-modulen om du vill hantera Batch-resurser med PowerShell.
* [Azure CLI](/cli/azure/overview): Azure-kommandoradsgränssnittet (Azure CLI) är en plattformsoberoende verktygsuppsättning som tillhandahåller shell-kommandon för interaktion med många Azure-tjänster, inklusive Batch.

## <a name="batch-resource-management"></a>Resurshantering med Batch

Azure Resource Manager-API:erna för Batch ger programmatisk åtkomst till Batch-konton. Med hjälp av dessa API:er kan du programmatiskt hantera Batch-konton, kvoter och programpaket.  

| API | API-referens | Ladda ned | Självstudier | Kodexempel |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |Saknas |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Självstudie](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>Batch-verktyg
Du måste inte skapa lösningar med hjälp av Batch, men det finns några värdefulla verktyg du kan använda när du utvecklar och felsöker Batch-baserade program och tjänster.

* [Azure Portal][portal]: Du kan skapa, övervaka och ta bort Batch-pooler, jobb och aktiviteter på Batch-bladet på Azure Portal. Du kan visa statusinformation för dessa och andra resurser när du kör dina jobb, och även ladda ned filer från beräkningsnoderna i din pooler (t.ex. ladda ned en misslyckad uppgifts `stderr.txt` vid felsökning). Du kan också hämta fjärrskrivbordsfiler (RDP) som du kan använda för att logga in till beräkningsnoder.
* [Azure Batch Explorer][batch_explorer]: Batch Explorer tillhandahåller Batch-resurshanteringsfunktioner som liknar dem som Azure Portal tillhandahåller, men i ett fristående WPF-klientprogram (Windows Presentation Foundation). Ett av de Batch .NET-exempelprogram som är tillgängliga på [GitHub][github_samples] kan du skapa med Visual Studio 2015 eller senare och använda för att söka efter och hantera resurserna i ditt Batch-konto när du utvecklar och felsöker dina Batch-lösningar. Visa information om jobb, pooler och uppgifter från beräkningsnoder, och fjärranslut till noder med fjärrskrivbordsfiler (RDP) som du kan ladda ned med Batch Explorer.
* [Microsoft Azure Storage Explorer][storage_explorer]: Även om det strikt sett inte är ett Azure Batch-verktyg är Storage Explorer ett annat värdefullt verktyg som du kan använda när du utvecklar och felsöker dina Batch-lösningar.

## <a name="next-steps"></a>Nästa steg

* Läs [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md). Här finns viktig information för alla som tänker använda Batch. Artikeln innehåller mer detaljerad information om Batch-tjänstresurser som pooler, noder, jobb och uppgifter, och de många API-funktioner som du kan använda när du skapar ett Batch-program.
* [Komma igång med Azure Batch-biblioteket för .NET](batch-dotnet-get-started.md) innehåller information om hur du använder C# och Batch .NET-biblioteket för att köra en enkel arbetsbelastning med ett vanligt Batch-arbetsflöde. Den här artikeln bör vara en av dina självklara utgångspunkter när du lär dig hur man använder Batch-tjänsten. Det finns även en [Python-version](batch-python-tutorial.md) av självstudien.
* Hämta [kodexemplet på GitHub][github_samples] om du vill se hur både C# och Python kan användas med Batch för att schemalägga och bearbeta exempelarbetsbelastningar.
* Ta en titt på [utbildningsvägen för Batch][learning_path] om du vill veta mer om de tillgängliga resurserna när du lär dig hur du arbetar med Batch.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

