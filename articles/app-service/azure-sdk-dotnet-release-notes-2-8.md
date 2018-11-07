---
title: Azure SDK för .NET 2.8 viktig information
description: Azure SDK för .NET 2.8 viktig information
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
ms.openlocfilehash: 6aa2684a900dffecd481d51b8876b0e674c1a6ea
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235538"
---
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK för .NET 2.8, 2.8.1 och 2.8.2
## <a name="overview"></a>Översikt
Den här artikeln innehåller viktig information (som innehåller kända problem och de senaste ändringarna) för Azure SDK för .NET 2.8 2.8.1 och 2.8.2 versioner. 

Fullständig lista över nya funktioner och uppdateringar som görs i den här versionen finns i den [Azure SDK 2.8 för Visual Studio 2013 och Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) meddelande. 

## <a name="azure-sdk-for-net-28"></a>Azure SDK för .NET 2.8
### <a name="download-azure-sdk-for-net-28"></a>Hämta Azure SDK för .NET 2.8
[Azure SDK för .NET 2.8 för Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK för .NET 2.8 för Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=699287)

### <a name="net-452-support"></a>Stöd för .NET 4.5.2
#### <a name="known-issues"></a>Kända problem
Azure SDK 2.8 för .NET kan du skapa .NET 4.5.2 Cloud Service-paket. Men .NET 4.5.2 framework installeras inte på standard gäst-OS-avbildningar fram till januari 2016 gäst-OS-versionen. Innan, 4.5.2 för .NET framework ska vara tillgänglig via en separat gäst-OS-Slutversion – November 2015-02. Se den [Azure Gästoperativsystemversioner och SDK-Kompatibilitetsöversikten](../cloud-services/cloud-services-guestos-update-matrix.md) sidan för att spåra när avbildningen kommer att släppas.  När avbildningen November 2015-02 släpps kan du använda den avbildningen genom att uppdatera filen (.cscfg) för Molntjänsten konfigurationsfilen. I konfigurationen av filen anger osVersion-attributet till elementet ServiceConfiguration till strängen ”WA-GUEST-OS-4.26_201511-02”. Om du vill välja för att använda den här bilden kommer du inte längre få automatiska uppdateringar av gäst-OS. Få automatiska uppdateringar osVersion måste anges till ”*” och .NET 4.5.2 kommer endast att vara tillgänglig via Automatiska uppdateringar i januari 2016.

### <a name="azure-data-factory"></a>Azure Data Factory
#### <a name="known-issues"></a>Kända problem
Under en **Data Factory mallen** projekt skapas med exempeldata, azure power shell-skript kan misslyckas om azure power shell-version som installerats på datorn efter 0.9.8.

Om du vill skapa den här typen av projektet, måste du installera [azure power shell-version 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).

### <a name="azure-resource-manager-tools"></a>Verktyget Azure Resource Manager
#### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar
PowerShell-skript som tillhandahålls av Azure-resursgrupp-projekt har uppdaterats i den här versionen att arbeta med de nya Azure PowerShell-cmdletarna version 1.0.  Den här nya skriptet fungerar inte från med Visual Studio när du använder en version av SDK: N innan 2.8.  

Skript från projekt som skapats i tidigare versioner av SDK: N kan inte köras från Visual Studio när du använder 2,8 SDK.  Alla skript kommer att fortsätta att arbeta utanför Visual Studio med rätt version av Azure PowerShell-cmdlets.  

2,8 SDK kräver version 1.0 av Azure PowerShell-cmdlets.  Alla andra versioner av SDK: N kräver version 0.9.8 av Azure PowerShell-cmdlets.  Mer information finns i [detta](https://go.microsoft.com/fwlink/?LinkID=623011) blogg.

### <a name="web-tools-extensions"></a>Web Tools tillägg
#### <a name="known-issues"></a>Kända problem
Följande kända problem åtgärdas i följande utgåva.

* App Service-relaterad moln och Server Explorer gest för icke-produktionsmiljöer (till exempel Azure Kina eller Azure Stack-kunder) inte fungerar. För kunder i dessa påverkas områden aktiverar hämta publiceringsprofilen från Azure portal publicering möjlighet. En framtida version kommer reparera gester, till exempel ”koppla felsökaren” och ”View Streaming Logs” för Azure Kina och Stack-kunder. 
* Kunder kan se fel under App Service när App Insights instansen som de distribuerar skapas i en annan region än USA, östra. I dessa scenarier kan aktiverar skapa en App Service i portalen och ladda ned profilen som publicering scenarier. 

### <a name="azure-hdinsight-tools"></a>Azure HDInsight-verktyg
#### <a name="new-updates"></a>Nya uppdateringar
* Du kan köra Hive-frågan i klustret via HiveServer2 nästan utan merkostnader och se jobbet loggar i realtid.
* Mer information finns den nya Hive-vy för uppgiftskörning kan du granska i ditt jobb som är djupare och identifiera potentiella problem.

Mer information finns i [Azure SDK 2.8 för Visual Studio 2013 och Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK för .NET 2.8.1
### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Kända problem med Visual Studio 2013 och Visual Studio 2015
1. Utlösta Webbjobbet publicerar till platser att visa och fel och inte ange ett schema, men det skickar Webbjobbet till Azure. Kunder som behöver ett schemalagt jobb kan sedan använda den Azure-portalen för att ställa in schemat för WebJob. 
2. Felsökare problem kan uppstå i Python-kunder. Service-teamet lanserar en korrigering för detta, men låt Microsoft vet i forum eller på meddelandebloggen eller frigöra kommentarsavsnittet om information om kunder som påverkas. 
3. Kunder i vissa regioner (till exempel södra Indien) kommer att få App Service etablering fel. Det här är konsekvent med portalen och kunder som det här problemet uppstår kan använda Azure-portalen för att begära åtkomst till att publicera till dessa geo-regioner. När de begär åtkomst till dessa regioner med hjälp av ska Azure portal etableringen fungera. 

## <a name="azure-sdk-for-net-282"></a>Azure SDK för .NET 2.8.2
Efter installationen av 2.8.2 följande problem kan uppstå i Verktyg för kunder.         

* Om du använder Windows 10 och inte har installerat Internet Explorer, kan du få felmeddelandet ”Det gick inte att hitta Internet Explorer”.
  Lös problemet genom att installera Internet Explorer genom att använda dialogen Lägg till/ta bort Windows-komponenter.

Om du ser det här problemet kan du använda funktionen Skicka ett Leende för att rapportera.

Mer information finns i [detta](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) publicera.

## <a name="other-updates"></a>Övriga uppdateringar
Andra uppdateringar, se [Azure SDK 2.8 meddelande efter](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="also-see"></a>Se även
[Azure SDK 2.8 meddelande inlägg](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Support och tillbakadragande Information om Azure SDK för .NET och API: er](https://msdn.microsoft.com/library/azure/dn479282.aspx)

