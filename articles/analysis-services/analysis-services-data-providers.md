---
title: Azure Analysis Services-klientbibliotek | Microsoft-dokument
description: Beskriver klientbibliotek som krävs för klientprogram och verktyg för att ansluta Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5914c7987d5a54a6bcc779231287309517f5121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129227"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Klientbibliotek för anslutning till Analysis Services

Klientbibliotek är nödvändiga för klientprogram och verktyg för att ansluta till Analysis Services-servrar. Microsoft-klientprogram som Power BI Desktop, Excel, SQL Server Management Studio (SSMS) och Analysis Services-projekttillägg för Visual Studio installerar alla tre klientbiblioteken och uppdaterar dem tillsammans med regelbundna programuppdateringar. I vissa fall kan du behöva installera nyare versioner av klientbiblioteken. Anpassade klientprogram kräver också att klientbibliotek installeras.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Ladda ned de senaste klientbiblioteken (Windows Installer)  

|Ladda ned  |Produktversion  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.29.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.29.25       |
|[Amo](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.8.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.8.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO och ADOMD (NuGet paket)

AMO-klientbibliotek (Analysis Services Management Objects) och ADOMD finns tillgängliga som installerbara paket från [NuGet.org](https://www.nuget.org/). Vi rekommenderar att du migrerar till NuGet-referenser i stället för att använda Windows Installer. 

|Paket  | Produktversion  | 
|---------|---------|
|[Amo](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.8.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.8.0      |

NuGet paketenheter AssemblyVersion följa semantiska versionshantering: MAJOR. Mindre. Patch. NuGet referenser läsa in den förväntade versionen även om det finns en annan version i GAC (som härrör från MSI installera). PATCH ökas för varje utgåva. AMO- och ADOMD-versioner hålls synkroniserade.

## <a name="understanding-client-libraries"></a>Förstå klientbibliotek

Analysis Services använder tre klientbibliotek, även kallade dataleverantörer. ADOMD.NET och Analysis Services Management Objects (AMO) hanteras klientbibliotek. Analysis Services OLE DB Provider (MSOLAP DLL) är ett inbyggt klientbibliotek. Vanligtvis installeras alla tre samtidigt. **Azure Analysis Services kräver de senaste versionerna av alla tre biblioteken**. 

Microsoft-klientprogram som Power BI Desktop och Excel installerar alla tre klientbiblioteken och uppdaterar dem när nya versioner är tillgängliga. Beroende på version eller frekvens av uppdateringar kanske vissa klientbibliotek inte är de senaste versionerna som krävs av Azure Analysis Services. Detsamma gäller för anpassade program eller andra gränssnitt som AsCmd, TOM, ADOMD.NET. Dessa program kräver manuell eller programmatiskt installera biblioteken. Klientbiblioteken för manuell installation ingår i SQL Server-funktionspaket som distributablea paket. Dessa klientbibliotek är dock knutna till SQL Server-versionen och kanske inte den senaste.  

Klientbibliotek för klientanslutningar skiljer sig från dataleverantörer som krävs för att ansluta från en Azure Analysis Services-server till en datakälla. Mer information om datakällanslutningar finns i [Datakällanslutningar](analysis-services-datasource.md).

## <a name="client-library-types"></a>Typer av klientbibliotek

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB Provider (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) är det inbyggda klientbiblioteket för Analysis Services-databasanslutningar. Den används indirekt av både ADOMD.NET och AMO, delegera anslutningsbegäranden till dataleverantören. Du kan också anropa OLE DB Provider direkt från programkoden.  
  
 Analysis Services OLE DB Provider installeras automatiskt av de flesta verktyg och klientprogram som används för att komma åt Analysis Services-databaser. Den måste installeras på datorer som används för att komma åt Analysis Services-data.  
  
 OLE DB-providers anges ofta i anslutningssträngar. En Analysis Services-anslutningssträng använder en annan nomenklatur för att referera till OLE DB Provider: MSOLAP. \<version>.dll.

### <a name="amo"></a>Amo  

 AMO är ett hanterat klientbibliotek som används för serveradministration och datadefinition. Den installeras och används av verktyg och klientprogram. SQL Server Management Studio (SSMS) använder till exempel AMO för att ansluta till Analysis Services. En anslutning med AMO är `"data source=\<servername>"`vanligtvis minimal, bestående av . När en anslutning har upprättats använder du API:et för att arbeta med databassamlingar och huvudobjekt. Både Visual Studio och SSMS använder AMO för att ansluta till en Analysis Services-instans.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET är ett hanterat dataklientbibliotek som används för att fråga Analysis Services-data. Den installeras och används av verktyg och klientprogram. 
  
 När du ansluter till en databas är anslutningssträngegenskaperna för alla tre biblioteken liknande. Nästan alla anslutningssträngar som du definierar för ADOMD.NET med hjälp av [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) fungerar också för AMO och Analysis Services OLE DB Provider (MSOLAP). Mer information finns i [Egenskaper för anslutningssträng &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Så här tar du reda på klientbiblioteksversionen   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Gå till `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Om du har mer än en mapp väljer du det högre numret.
  
2.  Högerklicka på**egenskaper för** > **Details** **msolap.dll** > . Om filnamnet är msolap140.dll är det äldre än den senaste versionen och bör uppgraderas.
    
    ![Information om klientbibliotek](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>Amo

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Om du har mer än en mapp väljer du det högre numret.
2. Högerklicka på**egenskapsinformation****för** >  **Microsoft.AnalysisServices** > .  

### <a name="adomd"></a>ADOMD

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Om du har mer än en mapp väljer du det högre numret.
2. Högerklicka på information om egenskaper**Properties** > **för** **Microsoft.AnalysisServices.AdomdClient** > .  


## <a name="next-steps"></a>Nästa steg
[Anslut med Excel](analysis-services-connect-excel.md)    
[Anslut med Power BI](analysis-services-connect-pbi.md)
