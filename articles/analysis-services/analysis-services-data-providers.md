---
title: "Klientbibliotek som krävs för att ansluta till Azure Analysis Services | Microsoft Docs"
description: "Beskriver klientbibliotek som krävs för program och verktyg att ansluta Azure Analysis Services"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/22/2018
ms.author: owend
ms.openlocfilehash: e5159f8c6e8c271230151f71bf3057d07fc75c21
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Klientbibliotek för att ansluta till Azure Analysis Services

Klientbibliotek är nödvändiga för program och verktyg att ansluta till Analysis Services-servrar. 

## <a name="download-the-latest-client-libraries-windows-installer"></a>Hämta de senaste klientbibliotek (Windows Installer)  

|Ladda ned  |Version  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.300.129.01      |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.300.129.01      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.0.2      |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.0.2      |

## <a name="amo-and-adomd-nuget-packages"></a>AMO och ADOMD (NuGet-paket)

Analysis Services AMO (Management Objects) och ADOMD klientbibliotek är tillgängliga som kan installeras paket från [NuGet.org](https://www.nuget.org/). Det rekommenderas att du migrerar till NuGet-referenser i stället för med Windows Installer. 

|Paket  |Version  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.0.2      |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.0.2      |



NuGet-paketet sammansättningar AssemblyVersion följer semantiska versionshantering: större. LÄGRE. KORRIGERING. NuGet-referenser läsa in den förväntade versionen, även om det finns en annan version i den globala Sammansättningscachen (som härrör från MSI-installation). KORRIGERING ökas för varje version. AMO och ADOMD versioner hålls synkroniserad.

## <a name="understanding-client-libraries"></a>Förstå klientbibliotek

Analysis Services använder tre klientbibliotek, även kallat dataleverantörer. Är hanterad klientbibliotek ADOMD.NET och tjänster AMO Analysis Management Objects (). Analysis Services OLE DB-providern (MSOLAP DLL) är en inbyggd klientbiblioteket. Alla tre installeras vanligtvis på samma gång. **Azure Analysis Services kräver att de senaste versionerna av alla tre bibliotek**. 

Klientprogram för Microsoft Power BI Desktop-och Excel installera alla tre klientbibliotek och uppdatera dem när nya versioner är tillgängliga. Beroende på version eller ofta uppdateras kanske några klientbibliotek inte de senaste versionerna som krävs av Azure Analysis Services. Detsamma gäller för anpassade program eller andra gränssnitt som AsCmd, TOM, ADOMD.NET. Dessa program kräver att du manuellt eller programmässigt installerar biblioteken. Klientbibliotek för manuell installation ingår i SQL Server-funktionspaket som distribuerbara paket. Dessa klientbibliotek är knutna till SQL Server-versionen och kanske inte den senaste versionen.  

Klientbibliotek för klientanslutningar skiljer sig från dataleverantörer som krävs för att ansluta från Azure Analysis Services-servern till en datakälla. Mer information om datakällan anslutningar finns [Datasource anslutningar](analysis-services-datasource.md).

## <a name="client-library-types"></a>Biblioteket klienttyper

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-providern (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) är inbyggda klientbiblioteket för Analysis Services-databasanslutningar. Den används indirekt av både ADOMD.NET och AMO, delegera anslutningsförfrågningar till dataprovidern. Du kan också kontakta OLE DB-providern direkt från programkod.  
  
 Analysis Services OLE DB-providern installeras automatiskt som de flesta verktyg och klientprogram som används för åtkomst till Analysis Services-databaser. Den måste installeras på datorer som används för åtkomst till Analysis Services-data.  
  
 OLE DB-providrar anges ofta i anslutningssträngar. En anslutningssträng för Analysis Services använder en annan terminologi för att referera till OLE DB-providern: MSOLAP. \<version > .dll.

### <a name="amo"></a>AMO  

 AMO är en hanterad klientbibliotek som används för serveradministration och datadefinitionen. Det har installerats och används av verktyg och program. Till exempel använder SQL Server Management Studio (SSMS) AMO för att ansluta till Analysis Services. En anslutning med AMO är vanligtvis minimal, som består av `“data source=\<servername>”`. När en anslutning har upprättats kan använda du API för att arbeta med databasen samlingar och större objekt. Både SSDT och SSMS använder AMO för att ansluta till en Analysis Services-instans.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET är en hanterad data klientbiblioteket som används för att fråga efter Analysis Services-data. Det har installerats och används av verktyg och program. 
  
 När du ansluter till en databas påminner om egenskaperna för anslutningssträngen för alla tre bibliotek. Nästan alla anslutningssträngen som du definierar för ADOMD.NET med [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) fungerar även för AMO och den Analysis Services OLE DB-providern (MSOLAP). Läs mer i [anslutningssträngsegenskaper &#40; Analysis Services &#41; ](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Hur du fastställer biblioteket klientversion   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Gå till ”C:\Program Files\Microsoft Analysis Services\AS OLEDB\. Om du har mer än en mapp väljer du den högre nummer.
  
2.  Högerklicka på **msolap.dll** > **egenskaper** > **information**. Om filnamnet är msolap140.dll, är äldre än den senaste versionen och bör uppgraderas.
    
    ![Information om biblioteket för klient](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Om du har mer än en mapp väljer du den högre nummer.
2. Högerklicka på **Microsoft.AnalysisServices** > **egenskaper** > **information**.  

### <a name="adomd"></a>ADOMD

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Om du har mer än en mapp väljer du den högre nummer.
2. Högerklicka på **Microsoft.AnalysisServices.AdomdClient** > **egenskaper** > **information**.  


## <a name="next-steps"></a>Nästa steg
[Ansluta till Excel](analysis-services-connect-excel.md)    
[Anslut med Power BI](analysis-services-connect-pbi.md)
