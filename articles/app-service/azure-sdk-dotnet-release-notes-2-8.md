---
title: Azure SDK för .NET 2,8 viktig information
description: Azure SDK för .NET 2,8 viktig information
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: de7207ff-ba4f-4008-9141-8742fcaa3254
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 0b9f55d69c824e86245738a082f95fc529583f58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "23836394"
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK för .NET 2.8, 2.8.1 och 2.8.2
## <a name="overview"></a>Översikt
Den här artikeln innehåller viktig information (som innehåller kända problem och viktiga förändringar) för Azure SDK för .NET 2.8 2.8.1 och 2.8.2 versioner. 

Fullständig lista över nya funktioner och uppdateringar som görs i den här versionen finns i [Azure SDK 2.8 för Visual Studio 2013 och Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) meddelande. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK för .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Ladda ner Azure SDK för .NET 2.8
[Azure SDK för .NET 2.8 för Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK för .NET 2.8 för Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Stöd för .NET 4.5.2
#### <a name="known-issues"></a>Kända problem
Azure .NET SDK 2.8 kan du skapa .NET 4.5.2 Cloud Service-paket. Men 4.5.2 för .NET framework installeras inte på standard gäst operativsystemsavbildningar förrän januari 2016 gäst-OS-versionen. Innan, 4.5.2 för .NET framework är tillgänglig via en separat gäst-OS-versionen – November 2015-02. Finns det [versioner för gästoperativsystem till Azure och SDK-Kompatibilitetsmatris](../cloud-services/cloud-services-guestos-update-matrix.md) att spåra när bilden kommer att släppas.  När avbildningen November 2015-02 släpps kan du använda den avbildningen genom att uppdatera Molntjänsten konfigurationsfilen (.cscfg)-fil. I tjänstkonfigurationen filen ställa in osVersion-attributet i elementet ServiceConfiguration till strängen ”WA-GUEST-OS-4.26_201511-02”. Om du vill välja för att använda den här avbildningen kommer du inte längre hämta automatiska uppdateringar för gäst-OS. Få automatiska uppdateringar osVersion måste anges till ”*” och .NET 4.5.2 är bara tillgängligt via Automatiska uppdateringar i januari 2016.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Kända problem
Under en **Data Factory mallen** projekt skapas med exempeldata, azure power shell-skript kan misslyckas om azure power shell-version som installerats på datorn efter 0.9.8.

Om du vill att skapa den här typen av projektet, måste du installera [azure power shell-version 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Azure Resource Manager-verktyg
#### <a name="breaking-changes"></a>Gör ändringar
PowerShell-skriptet som tillhandahålls av Azure-resursgrupp projektet har uppdaterats i den här versionen ska fungera med de nya Azure PowerShell-cmdletarna version 1.0.  Den här nya skriptet fungerar inte från med Visual Studio när du använder en version av SDK innan 2.8.  

Skript från projekt som skapats i tidigare versioner av SDK kan inte köras från Visual Studio när du använder 2,8 SDK.  Alla skript kommer att fortsätta att arbeta utanför Visual Studio med rätt version av Azure PowerShell-cmdlets.  

2,8 SDK kräver version 1.0 av Azure PowerShell-cmdlets.  Alla versioner av SDK kräver version 0.9.8 av Azure PowerShell-cmdlets.  Mer information finns i [detta](http://go.microsoft.com/fwlink/?LinkID=623011) blogg.

### <a name="web-tools-extensions"></a>Tillägg för verktyg för webbprogram
#### <a name="known-issues"></a>Kända problem
Följande kända problem åtgärdas i följande utgåva.

* Apptjänst relaterade molntjänster och Server Explorer gest för icke-produktionsmiljöer (till exempel Azure Kina eller Azure-stacken kunder) inte fungerar. För kunder i dessa påverkas områden aktiverar hämta publiceringsprofilen från Azure-portalen publicering möjlighet. En framtida utgåva reparerar gester, till exempel ”koppla felsökare” och ”visa strömning loggfiler” för Azure Kina och Stack-kunder. 
* Fel kan under App Service när App Insights instansen de distribuerar skapas i en annan region än östra USA kunder visas. I så fall kan aktiverar skapa en Apptjänst i portalen och hämtar profilen som publishing scenarier. 

### <a name="azure-hdinsight-tools"></a>Azure HDInsight-verktyg
#### <a name="new-updates"></a>Nya uppdateringar
* Du kan köra Hive-fråga i klustret via HiveServer2 med nästan utan kostnader och se jobbet loggar realtid.
* Med den nya Hive vy för uppgiftskörning gräva i ditt jobb som är djupare kan hitta mer information och identifiera potentiella problem.

Mer information finns i [Azure SDK 2.8 för Visual Studio 2013 och Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK för .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Kända problem för Visual Studio 2013 och Visual Studio 2015
1. Utlösta Webbjobb publicerar till platser kan visa och fel och inte ange ett schema, men det skickar Webbjobbet till Azure. Kunder som behöver ett schemalagda jobb kan sedan använda Azure Portal för att ställa in schemat för Webbjobbet. 
2. Python kunderna kan ha problem med felsökare. Service-team inför en korrigering för detta men om kunder påverkas, kan du att Microsoft vet i forum eller på bloggen meddelande eller viktiga kommentarer för anteckningar. 
3. Kunderna i vissa regioner (till exempel södra Indien) får Apptjänst etablering fel. Detta är förenligt med portalen och kunder som drabbas av problemet kan använda Azure-portalen för att begära åtkomst till publicera till dessa geografiska regionerna. När de begär åtkomst till dessa regioner med hjälp av ska Azure portal etableringen fungera. 

## <a name="azure-sdk-for-net-282"></a>Azure SDK för .NET 2.8.2
Efter installationen av 2.8.2 verktyg, kunder kan ha följande problem.         

* Om du använder Windows 10 och inte har installerat Internet Explorer kan du få felmeddelandet ”Det gick inte att hitta Internet Explorer”.
  Lös problemet genom att installera Internet Explorer genom att använda dialogrutan Lägg till/ta bort Windows-komponenter.

Om du upptäcker det här problemet, kan du använda funktionen Skicka en le för att rapportera.

Mer information finns i [detta](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) efter.

## <a name="other-updates"></a>Övriga uppdateringar
Andra uppdateringar finns [Azure SDK 2.8 meddelande efter](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Se även
[Azure SDK 2.8 meddelande post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Support och tillbakadragning Information för Azure SDK för .NET och API: er](https://msdn.microsoft.com/library/azure/dn479282.aspx)

