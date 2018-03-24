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
ms.openlocfilehash: a62268cee0750c544703fb4c3f7e91238e26b872
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-sdk-for-net-29-release-notes"></a>Azure SDK för .NET 2.9 viktig information

Det här avsnittet innehåller viktig information för version 2.9 och 2.9.6 av Azure SDK för .NET.

## <a name="azure-sdk-for-net-296-release-summary"></a>Azure SDK för .NET 2.9.6 släpper sammanfattning

Utgivningsdatum: 11/16/2016
 
Inga senaste Azure SDK 2.9 ändringar har införts i den här versionen. Det finns inga uppgraderingsprocessen som behövs för att utnyttja detta SDK med befintliga Cloud Service-projekt.

### <a name="visual-studio-2017-release-candidate"></a>Visual Studio 2017 Release Candidate

- I Visual Studio 2017 RC är den här versionen av Azure SDK för .NET inbyggt i Azure-arbetsbelastning. Alla verktyg som du behöver göra Azure-utveckling ska ingå i Visual Studio 2017 RC framöver. Visual Studio 2015 och Visual Studio 2013 är SDK fortfarande tillgänglig för via WebPI. Vi kommer att upphöra Azure SDK för .NET-versioner för Visual Studio 2013 när Visual Studio 2017 släpper som en slutlig produkt. Följ länken för att hämta Visual Studio 2017 RC: https://www.visualstudio.com/vs/visual-studio-2017-rc/

### <a name="azure-diagnostics"></a>Azure Diagnostics

- Ändra beteende för att lagra endast en partiell anslutningssträng med nyckel ersättas med en token för anslutningssträngen för lagring av molntjänster diagnostik. Den faktiska lagringsnyckeln lagras nu på mappen så att dess åtkomst kan kontrolleras. Visual Studio läser lagringsnyckeln från mapp för lokala felsökning och publiceringsprocessen. 
- Som svar på ändringar som beskrivs ovan, förbättrad Visual Studio Online-teamet aktivitet för Azure Cloud Services Distributionsmall så att användarna kan ange lagringsnyckeln för att ställa in diagnostik tillägget vid publicering till Azure i kontinuerlig integrering och distribution.
- Vi har gjort det möjligt att lagra säker anslutningssträngen och tokenisering för Azure Diagnostics (BOMULLSTUSS), som hjälper dig att lösa problem med konfigurationen över environements.
 
### <a name="windows-server-2016-virtual-machines"></a>Windows Server 2016 virtuella datorer

- Visual Studio stöder nu distribuera Cloud Services till OS-familjen 5 (Windows Server 2016) virtuella datorer. Du kan ändra dina inställningar om du vill rikta nya OS-familjen för befintliga molntjänster. När du skapar nya molntjänster, om du väljer att skapa tjänsten med hjälp av .net 4.6 eller högre, kommer den som standard tjänsten om du vill använda OS-familjen 5.  Mer information kan du granska den [gäst-OS-familjen stöder tabellen](https://azure.microsoft.com/en-us/documentation/articles/cloud-services-guestos-update-matrix/).

#### <a name="known-issues"></a>Kända problem

- Azure .NET SDK 2.9.6 introducerades en begränsning som blockerar distributionen av projekt med hjälp av stöds inte .NET Framework-program (till exempel .NET 4.6) till OS-familj < 5. En åtgärdsmetod [här](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

 
### <a name="azure-in-role-cache"></a>Azure i Rollinstanser 

- Stöd för cachelagring i Rollinstanser för Azure ends i 30 November 2016. Mer information klickar du på [här](https://azure.microsoft.com/en-us/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

### <a name="azure-resource-manager-templates-for-azure-stack"></a>Azure Resource Manager-mallar för Azure-Stack

- Vi har introducerat Azure Stack som mål för en distribution för din Azure Resource Manager-mallar.


## <a name="azure-sdk-for-net-29-summary"></a>Azure SDK för .NET 2.9 sammanfattning

## <a name="overview"></a>Översikt
Det här dokumentet innehåller viktig information för Azure SDK för .NET 2.9 versionen. 

Detaljerad information om uppdateringar i den här versionen finns i [Azure SDK 2.9 meddelande efter](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Förhandsgranska Azure SDK 2.9 för Visual Studio 2015 Update 2 och Visual Studio ”15”
Den här uppdateringen innehåller följande felkorrigeringar:

* Problem som rör REST API-klient Generation som strängen ”okänd typ” visas som namnet på mappen kod gen och/eller namnet på namnområdet släppa i den genererade koden.
* Problem som rör schemalagda Webbjobb där autentiseringsinformationen har fel ska skickas till Schemaläggaren etableringsprocessen.

Den här uppdateringen innehåller följande nya funktion:

* Stöd för sekundära Apptjänster på fliken ”tjänster” i dialogrutan för Apptjänst-etablering. 

## <a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Azure Data Lake-verktyg för Visual Studio 2015 Update 2
Den här uppdateringar omfattar följande:

* **Azure Data Lake-verktyg** för Visual Studio nu samman i Azure SDK för .NET-versionen. Verktyget installeras automatiskt när du installerar Azure SDK. 
  
    Verktyget uppdateras ofta, gå [här](http://aka.ms/datalaketool) att hämta uppdateringarna.
* **Server Explorer** nu kan du visa alla och skapa entiteter vissa U-SQL-metadata. Mer information finns i [detta](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogg.

## <a name="hdinsight-tools"></a>HDInsight-verktyg
**HDInsight Tools** för Visual Studio nu stöder HDInsight version 3.3, inklusive visar Tez-diagram och andra språk åtgärdas.

## <a name="azure-resource-manager"></a>Azure Resource Manager
Den här versionen lägger till [KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) stöd för Resource Manager-mallar.

## <a name="see-also"></a>Se också
[Azure SDK 2.9 meddelande post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)

