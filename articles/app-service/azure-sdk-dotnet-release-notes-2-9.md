---
title: Azure SDK för .NET 2.9 viktig information
description: Azure SDK för .NET 2.9 viktig information
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: ''
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 01b8ccc9fe6b5469408131bce68a903f15382998
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222536"
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Azure SDK för .NET 2.9 viktig information

Det här avsnittet innehåller viktig information om version 2.9 och 2.9.6 av Azure SDK för .NET.

## <a name="azure-sdk-for-net-296-release-summary"></a>Azure SDK för .NET 2.9.6 viktig sammanfattning

Utgivningsdatum: 11/16/2016
 
Inga ändringar de senaste Azure SDK 2.9 har införts i den här versionen. Det finns inga uppgraderingsprocessen som behövs för att utnyttja denna SDK med befintliga Cloud Service-projekt.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 RC

- Den här versionen av Azure SDK för .NET är inbyggda för Azure-arbetsbelastningen i Visual Studio 2017 RC. Alla verktyg du behöver göra Azure-utveckling kommer att ingå i Visual Studio 2017 RC framöver. SDK: N kan fortfarande tillgängliga via WebPI för Visual Studio 2015 och Visual Studio 2013. Vi kommer att upphöra Azure SDK för .NET-versioner för Visual Studio 2013 när Visual Studio 2017 ger ut som en slutliga produkt. Följ den här länken för att ladda ned Visual Studio 2017 RC: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Azure Diagnostics

- Ändra beteendet för att endast lagra en partiell anslutningssträng med nyckeln ersättas med en token för Cloud Services diagnostik lagringsanslutningssträng. Den faktiska lagringsnyckeln lagras nu på mappen så att åtkomsten kan styras. Visual Studio läser lagringsnyckeln från mapp för lokal felsökning och publiceringsprocessen. 
- Som svar på ändringar som beskrivs ovan, förbättrad Azure Cloud Services-Distributionsmall för uppgiften i Visual Studio Online-teamet så att användarna kan ange lagringsnyckel för att ställa in diagnostiktillägget vid publicering till Azure för kontinuerlig integrering och Distribution.
- Vi har gjort det möjligt att lagra säker anslutningssträng och tokenisering för Azure Diagnostics SÄKERHETSSPECIFIKA, för att lösa problem med konfigurationen i environements.
 
### <a name="windows-server-2016-virtual-machines"></a>Virtuella datorer i Windows Server 2016

- Visual Studio stöder nu distribution av molntjänster till OS-familj 5 (Windows Server 2016) virtuella datorer. Du kan ändra dina inställningar om du vill anpassa den nya OS-familj för befintliga molntjänster. När du skapar nya molntjänster, om du väljer att skapa tjänsten med hjälp av .net 4.6 eller senare, det kommer som standard tjänsten att använda OS-familj 5.  Mer information hittar du den [OS-Gästfamiljen stöder tabellen](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Kända problem

- Azure .NET SDK 2.9.6 introducerades en begränsning som blockerar distributionen av projekt med hjälp av stöds inte .NET Framework-program (till exempel .NET 4.6) till någon OS-familj < 5. En åtgärdsmetod [här](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Azure cachelagring i Rollinstanser 

- Stöd för Azure cachelagring i Rollinstanser avslutas den 30 November 2016. Mer information klickar du på [här](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Azure Resource Manager-mallar för Azure Stack

- Vi har infört Azure Stack som mål för en distribution för din Azure Resource Manager-mallar.


## <a name="azure-sdk-for-net-29-summary"></a>Azure SDK för .NET 2.9 sammanfattning

## <a name="overview"></a>Översikt
Det här dokumentet innehåller viktig information för Azure SDK för .NET 2.9 versionen. 

Detaljerad information om uppdateringar i den här versionen finns i den [Azure SDK 2.9 meddelande efter](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Förhandsversion av Azure SDK 2.9 för Visual Studio 2015 Update 2 och Visual Studio ”15”
Den här uppdateringen innehåller följande felkorrigeringar:

* Problem som rör REST API-klienten Generation där strängen ”okänd typ” visas som namnet på mappen generations kod och/eller namnet på namnområdet som släpps till den genererade koden.
* Problem som rör schemalagda WebJobs där autentiseringsinformationen misslyckades kontrollerades som ska skickas till Scheduler etableringsprocessen.

Den här uppdateringen innehåller följande nya funktion:

* Stöd för sekundära Apptjänster på fliken ”tjänster” i dialogrutan för Apptjänst-etablering. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake Tools för Visual Studio 2015 Update 2
De här uppdateringarna omfattar följande:

* **Azure Data Lake Tools** för Visual Studio nu sammanfogas i Azure SDK för .NET-versionen. Verktyget installeras automatiskt när du installerar Azure SDK. 
  
    Verktyget uppdateras ofta, gå [här](http://aka.ms/datalaketool) kan hämta uppdateringarna.
* **Server Explorer** nu kan du visa alla och skapa vissa entiteter för U-SQL-metadata. Mer information finns i [detta](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogg.

## <a name="hdinsight-tools"></a>HDInsight-verktyg
**HDInsight Tools** för Visual Studio nu stöder HDInsight version 3.3, inklusive som visar Tez-diagram och andra språk korrigeringar.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Den här versionen lägger till [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) stöd för Resource Manager-mallar.

## <a name="see-also"></a>Se också
[Azure SDK 2.9 meddelande inlägg](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

