---
title: "Azure Batch kör storskaliga parallella beräkningslösningar i molnet | Microsoft Docs"
description: "Lär dig mer om hur du använder Azure Batch-tjänsten för storskaliga parallella arbetsbelastningar och HPC-arbetsbelastningar"
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
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: e204a3c79768ddbd433f3b00a3db456ef3258173
ms.lasthandoff: 02/28/2017


---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Köra parallella arbetsbelastningar med Batch

Azure Batch är en plattformstjänst för att effektivt köra storskaliga parallella program och HPC-program (databehandling med höga prestanda) i molnet. Azure Batch schemalägger beräkningsintensivt arbete för körning i en hanterad samling virtuella datorer. Tjänsten kan automatiskt skala beräkningsresurser efter jobbens behov.

Med Azure Batch kan du enkelt definiera Azure-beräkningsresurser för att köra dina program parallellt och i stor skala. Det finns inget behov av att manuellt skapa, konfigurera och hantera ett HPC-kluster, enskilda virtuella datorer, virtuella nätverk eller en komplex infrastruktur för jobb- och aktivitetsschemaläggning. Azure Batch automatiserar eller förenklar dessa uppgifter åt dig.

## <a name="use-cases-for-batch"></a>Användningsfall för Batch
Batch är en hanterad Azure-tjänst som används för *batchbearbetning* eller *batchbehandling* – dvs. körning av ett stort antal liknande uppgifter för ett visst önskat resultat. Batchbehandling används framför allt av organisationer som regelbundet bearbetar, omvandlar och analyserar stora mängder data.

Batch fungerar bra med parallella program och arbetsbelastningar. Parallella arbetsbelastningar är sådana som lätt delas in i flera aktiviteter som utför arbete samtidigt på flera datorer.

![Parallella aktiviteter][1]<br/>

Några exempel på arbetsbelastningar som vanligtvis bearbetas med den här tekniken är:

* Finansiell riskmodellering.
* Analys av klimat- och hydrologidata
* Rendering, analys och bearbetning av bilder
* Mediekodning och transkodning
* Genetisk sekvensanalys
* Analyser av teknisk spänning
* Programvarutestning

Batch kan också utföra parallella beräkningar med ett reduceringssteg i slutet, samt köra mer komplexa HPC-arbetsbelastningar som [MPI-program (Message Passing Interface)](batch-mpi.md).

En jämförelse mellan Batch och andra HPC-lösningsalternativ i Azure finns i [Batch- och HPC-lösningar](batch-hpc-solutions.md).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="developing-with-batch"></a>Utveckla med Batch
Bearbetning av parallella arbetsbelastningar med Azure Batch görs normalt programmässigt med någon av [Batch-API:erna](#batch-development-apis). Ditt klientprogram eller -tjänst kan använda Batch-API:erna för att kommunicera med Batch-tjänsten. Med Batch-API:erna kan du skapa och hantera pooler för beräkningsnoder, antingen virtuella datorer eller molntjänster. Sedan kan du schemalägga jobb och uppgifter som ska köras på dessa noder. 

Du kan effektivt bearbeta storskaliga arbetsbelastningar för din organisation eller tillhandahålla en tjänstportal för dina kunder så att de kan köra jobb och aktiviteter (på begäran eller enligt ett schema) på en eller flera hundra tusen noder. Du kan också använda Azure Batch som en del av ett större arbetsflöde, med verktyg som [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [!TIP]
> När du är redo att fördjupa dig i Batch-API:et rekommenderar vi att du börjar med [Batch-funktionsöversikten för utvecklare](batch-api-basics.md).
> 
> 

### <a name="azure-accounts-youll-need"></a>Azure-konton som du behöver
När du utvecklar Batch-lösningar använder du följande konton i Microsoft Azure.

* **Azure-konto och Azure-prenumeration** – Om du inte redan har en Azure-prenumeration kan du aktivera din [MSDN-prenumerantförmån][msdn_benefits] eller registrera dig för ett [kostnadsfritt Azure-konto][free_account]. När du skapar ett konto skapas en standardprenumeration åt dig.
* **Batch-konto** – Azure Batch-resurser, inklusive pooler, beräkningsnoder, jobb och aktiviteter, är associerade med ett Azure Batch-konto. Om programmet frågar mot batchtjänsten, autentiserar det begäran med hjälp av Azure Batch-kontonamnet, URL-adressen för kontot och en åtkomstnyckel. Du kan [skapa ett Batch-konto](batch-account-create-portal.md) i Azure Portal.
* **Storage-konto** – Batch innehåller inbyggt stöd för att arbeta med filer i [Azure Storage][azure_storage]. Nästan alla Batch-scenarier använder Azure Blob Storage, dels för att mellanlagra programmen som dina aktiviteter kör och de data som de bearbetar, dels för att lagra de utdata som de genererar. Om du vill skapa ett Storage-konto läser du [Om Azure Storage-konton](../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>API:er för Batch-utveckling
Dina program och tjänster kan skicka direkta REST-API-anrop eller använda ett eller flera av följande klientbibliotek för att köra och hantera dina Azure Batch-arbetsbelastningar.

| API | API-referens | Ladda ned | Självstudier | Kodexempel |
| --- | --- | --- | --- | --- |
| **REST för Batch** |[MSDN][batch_rest] |Saknas |- |- |
| **.NET för Batch** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Självstudie](batch-dotnet-get-started.md) |[GitHub][api_sample_net] |
| **Python för Batch** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Självstudie](batch-python-tutorial.md)|[GitHub][api_sample_python] |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- |
| **Java för Batch** (förhandsversion) |[github.io][api_java] |[Maven][api_java_jar] |- |[GitHub][api_sample_java] |

### <a name="batch-command-line-tools"></a>Batch kommandoradsverktyg

Funktioner som tillhandahålls av utvecklings-API:er är också tillgängliga med hjälp av kommandoradsverktyg: 

* [PowerShell-cmdlets för Batch ][batch_ps]: Du kan använda Azure Batch-cmdlets i [Azure PowerShell](/powershell/azureps-cmdlets-docs)-modulen om du vill hantera Batch-resurser med PowerShell.
* [Azure CLI](../xplat-cli-install.md): Azure-kommandoradsgränssnittet (Azure CLI) är en plattformsoberoende verktygsuppsättning som tillhandahåller shell-kommandon för interaktion med många Azure-tjänster, inklusive Batch.

### <a name="batch-resource-management"></a>Resurshantering med Batch

Azure Resource Manager-API:erna för Batch ger programmatisk åtkomst till Batch-konton. Med hjälp av dessa API:er kan du programmatiskt hantera Batch-konton, kvoter och programpaket.  

| API | API-referens | Ladda ned | Självstudier | Kodexempel |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |Saknas |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Självstudie](batch-management-dotnet.md) |[GitHub][api_sample_net] |


### <a name="batch-tools"></a>Batch-verktyg
Du måste inte skapa lösningar med hjälp av Batch, men det finns några värdefulla verktyg du kan använda när du utvecklar och felsöker Batch-baserade program och tjänster.

* [Azure Portal][portal]: Du kan skapa, övervaka och ta bort Batch-pooler, jobb och aktiviteter på Batch-bladet på Azure Portal. Du kan visa statusinformation för dessa och andra resurser när du kör dina jobb, och även ladda ned filer från beräkningsnoderna i din pooler (t.ex. ladda ned en misslyckad uppgifts `stderr.txt` vid felsökning). Du kan också hämta fjärrskrivbordsfiler (RDP) som du kan använda för att logga in till beräkningsnoder.
* [Azure Batch Explorer][batch_explorer]: Batch Explorer tillhandahåller Batch-resurshanteringsfunktioner som liknar dem som Azure Portal tillhandahåller, men i ett fristående WPF-klientprogram (Windows Presentation Foundation). Ett av de Batch .NET-exempelprogram som är tillgängliga på [GitHub][github_samples] kan du skapa med Visual Studio 2015 eller senare och använda för att söka efter och hantera resurserna i ditt Batch-konto när du utvecklar och felsöker dina Batch-lösningar. Visa information om jobb, pooler och uppgifter från beräkningsnoder, och fjärranslut till noder med fjärrskrivbordsfiler (RDP) som du kan ladda ned med Batch Explorer.
* [Microsoft Azure Storage Explorer][storage_explorer]: Även om det strikt sett inte är ett Azure Batch-verktyg är Storage Explorer ett annat värdefullt verktyg som du kan använda när du utvecklar och felsöker dina Batch-lösningar.

## <a name="scenario-scale-out-a-parallel-workload"></a>Scenario: Skala ut en parallell arbetsbelastning
En vanlig lösning som använder Batch-API:erna för att interagera med Batch-tjänsten är att skala ut parallellt arbete – till exempel renderingen av bilder i 3D-scener – i en pool med beräkningsnoder. Den här poolen med beräkningsnoder kan vara din ”renderingsservergrupp” som till exempel tillhandahåller flera hundratals eller flera tusen kärnor till ditt återgivningsjobb.

Följande diagram illustrerar ett vanligt Batch-arbetsflöde med ett klientprogram eller en värdbaserad tjänst som använder Batch för att köra en parallell arbetsbelastning.

![Arbetsflöde för Batch-lösning][2]

I det här vanliga scenariot bearbetar programmet eller tjänsten en beräkningsarbetsbelastning i Azure Batch genom att utföra följande steg:

1. Ladda upp **indatafilerna** och **programmen** som ska bearbeta dessa filer till ditt Azure Storage-konto. Indatafilerna kan vara data som ska bearbetas i ditt program, till exempel modellering av finansdata eller videofiler som ska kodas. Programfilerna kan vara program som används för databearbetning, till exempel ett program för 3D-rendering eller ett mediekodningsprogram.
2. Skapa en Batch-**pool** med beräkningsnoder i ditt Batch-konto. Dessa noder är de virtuella datorer som ska köra dina uppgifter. Du kan ange egenskaper som [nodstorlek](../cloud-services/cloud-services-sizes-specs.md), operativsystem och platsen i Azure Storage för programmet som ska installeras när noderna ansluter till poolen (det program som du överförde i steg 1). Du kan också konfigurera poolen så att den [skalas automatiskt](batch-automatic-scaling.md) baserat på arbetsbelastningen som dina aktiviteter genererar. Automatisk skalning justerar antalet datornoder i poolen dynamiskt.
3. Skapa ett Batch-**jobb** som ska köra arbetsbelastningen i poolen med beräkningsnoder. När du skapar ett jobb kan du associera det med en Batch-pool.
4. Lägg till **aktiviteter** till jobbet. När du lägger till aktiviteter till ett jobb schemalägger Batch-tjänsten automatiskt aktiviteterna för körning på beräkningsnoderna i poolen. Varje aktivitet använder det program som du överförde för att bearbeta indatafilerna.
   
   * 4a. Innan en aktivitet körs kan den hämta data (indatafilerna) som den ska bearbeta till den beräkningsnod som den är associerad med. Om programmet inte redan har installerats på noden (se steg 2) kan det hämtas här i stället. När nedladdningarna har slutförts körs aktiviteterna på deras tilldelade noder.
5. När aktiviteterna utförs kan du köra frågor mot Batch för att övervaka jobbets och aktiviteternas förlopp. Ditt klientprogram eller -tjänst kommunicerar med Batch-tjänsten över HTTPS. Eftersom du kan övervaka tusentals aktiviteter som körs på tusentals beräkningsnoder, se till att [effektivt fråga Batch-tjänsten](batch-efficient-list-queries.md).
6. När aktiviteterna slutförs kan de ladda upp resultatdata till Azure Storage. Du kan också hämta filer direkt från filsystemet på en beräkningsnod.
7. När övervakningen upptäcker att aktiviteterna i jobbet har slutförts kan klientprogrammet eller tjänsten hämta utdata för vidare bearbetning eller utvärdering.

Tänk på att det här bara är ett av många sätt att använda Batch på, och att det här scenariot bara beskriver några få av alla de tillgängliga funktionerna. Du kan till exempel köra [flera aktiviteter parallellt](batch-parallel-node-tasks.md) på varje beräkningsnod och du kan [köra särskilda aktiviteter](batch-job-prep-release.md) som förbereder noderna för jobben och som rensar upp när allt är klart.

## <a name="next-steps"></a>Nästa steg
Nu när du har en högnivåöversikt över Batch-tjänsten är det dags att gå vidare och se hur du kan använda den för att bearbeta dina beräkningsintensiva parallella arbetsbelastningar.

* Läs [Översikt över Batch-funktioner för utvecklare](batch-api-basics.md). Här finns viktig information för alla som tänker använda Batch. Artikeln innehåller mer detaljerad information om Batch-tjänstresurser som pooler, noder, jobb och uppgifter, och de många API-funktioner som du kan använda när du skapar ett Batch-program.
* [Komma igång med Azure Batch-biblioteket för .NET](batch-dotnet-get-started.md) innehåller information om hur du använder C# och Batch .NET-biblioteket för att köra en enkel arbetsbelastning med ett vanligt Batch-arbetsflöde. Den här artikeln bör vara en av dina självklara utgångspunkter när du lär dig hur man använder Batch-tjänsten. Det finns även en [Python-version](batch-python-tutorial.md) av självstudien.
* Hämta [kodexemplet på GitHub][github_samples] om du vill se hur både C# och Python kan användas med Batch för att schemalägga och bearbeta exempelarbetsbelastningar.
* Ta en titt på [utbildningsvägen för Batch][learning_path] om du vill veta mer om de tillgängliga resurserna när du lär dig hur du arbetar med Batch.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/en-us/rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

