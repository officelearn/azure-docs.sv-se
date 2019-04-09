---
title: Klientbibliotek som krävs för att ansluta till Azure Analysis Services | Microsoft Docs
description: Beskriver klientbibliotek som krävs för program och verktyg att ansluta Azure Analysis Services
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 334859aecf3a5f7a5678c0af656c6837d5661dac
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270871"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Klientbibliotek för att ansluta till Azure Analysis Services

Klientbibliotek är nödvändiga för program och verktyg att ansluta till Analysis Services-servrar. Microsoft-klientprogram som Power BI Desktop, Excel, SQL Server Management Studio (SSMS) och SQL Server Data Tools (SSDT) installerar samtliga tre klientbibliotek och uppdatera dem tillsammans med regelbundna programuppdateringar. I vissa fall kan behöva du installera nyare version av klientbiblioteken. Anpassade klientprogram kräver också klientbibliotek är installerade.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Ladda ned de senaste klientbibliotek (Windows Installer)  

|Ladda ned  |Produktversion  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.15.26    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.15.26      |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.17.1.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.17.1.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO och ADOMD (NuGet-paket)

Klientbibliotek för Analysis Services AMO (Management Objects) och ADOMD är tillgängliga som kan installeras paket från [NuGet.org](https://www.nuget.org/). Vi rekommenderar att du migrerar till NuGet-referenser i stället för med hjälp av Windows Installer. 

|Paket  | Produktversion  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.17.1     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.17.1      |

NuGet-paketet sammansättningar AssemblyVersion Följ semantiska versionshantering: STÖRRE. MINDRE. PATCH. NuGet-referenser läsa in den förväntade versionen, även om det finns en annan version i GAC (vilket ger från MSI-installation). PATCH ökas för varje version. AMO och ADOMD-versioner hålls synkroniserade.

## <a name="understanding-client-libraries"></a>Förstå klientbibliotek

Analysis Services använder tre klientbibliotek, även kallat dataleverantörer. ADOMD.NET och tjänster objekt AMO (Analysis Management) är hanterade klientbibliotek. Analysis Services OLE DB-providern (MSOLAP DLL) är ett interna klientbibliotek. Alla tre installeras vanligtvis på samma gång. **Azure Analysis Services kräver de senaste versionerna av alla tre bibliotek**. 

Microsoft-klientprogram som Power BI Desktop och Excel installerar samtliga tre klientbibliotek och uppdatera dem när nya versioner är tillgängliga. Beroende på version eller hur ofta den uppdateras kanske inte vissa klientbibliotek de senaste versionerna som krävs av Azure Analysis Services. Detsamma gäller för anpassade program eller andra gränssnitt som AsCmd, TOM, ADOMD.NET. Dessa program kräver att biblioteken installeras manuellt eller programmässigt. Klientbibliotek för manuell installation ingår i funktionspaketen för SQL Server som distribuerbar paket. Dessa klientbibliotek är knutna till SQL Server-versionen och kanske inte den senaste versionen.  

Klientbibliotek för klientanslutningar skiljer sig från dataleverantörer som krävs för att ansluta från en Azure Analysis Services-server till en datakälla. Mer information om anslutningar till datakälla finns [anslutningar till datakälla](analysis-services-datasource.md).

## <a name="client-library-types"></a>Biblioteket klienttyper

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-providern (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) är det inbyggda klientbiblioteket för Analysis Services-databasanslutningar. Den används indirekt av både ADOMD.NET och AMO, delegera anslutningsförfrågningar till dataprovidern. Du kan också anropa OLE DB-providern direkt från koden.  
  
 Analysis Services OLE DB-providern installeras automatiskt som de flesta verktyg och klientprogram som används för åtkomst till Analysis Services-databaser. Den måste installeras på datorer som används för åtkomst till Analysis Services-data.  
  
 OLE DB-providers anges ofta i anslutningssträngar. En anslutningssträng för Analysis Services använder en annan terminologi för att referera till OLE DB-providern: MSOLAP.\<version>.dll.

### <a name="amo"></a>AMO  

 AMO är ett hanterat klientbibliotek som används för serveradministration och definition av data. Den har installerats och används av verktyg och program. Till exempel använder SQL Server Management Studio (SSMS) AMO för att ansluta till Analysis Services. En anslutning med AMO är vanligtvis minimal, som består av `"data source=\<servername>"`. När en anslutning har upprättats kan använda du API: et för att arbeta med databassamlingar och större objekt. Både SSDT och SSMS kan du använda AMO för att ansluta till en Analysis Services-instans.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET är ett klientbibliotek för hanterade data som används för att fråga Analysis Services-data. Den har installerats och används av verktyg och program. 
  
 När du ansluter till en databas, liknar egenskaperna för anslutningssträngen för alla tre bibliotek. Nästan alla anslutningssträngen som du definierar för ADOMD.NET med hjälp av [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) fungerar även för AMO och den Analysis Services OLE DB-providern (MSOLAP). Mer information finns i [anslutningssträngsegenskaper &#40;Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Så här kontrollerar du biblioteket klientversion   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Gå till `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Om du har mer än en mapp väljer du den högre nummer.
  
2.  Högerklicka på **msolap.dll** > **egenskaper** > **information**. Om filnamnet är msolap140.dll, är äldre än den senaste versionen och bör uppgraderas.
    
    ![Information om klient-biblioteket](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Om du har mer än en mapp väljer du den högre nummer.
2. Högerklicka på **Microsoft.AnalysisServices** > **egenskaper** > **information**.  

### <a name="adomd"></a>ADOMD

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Om du har mer än en mapp väljer du den högre nummer.
2. Högerklicka på **Microsoft.AnalysisServices.AdomdClient** > **egenskaper** > **information**.  


## <a name="next-steps"></a>Nästa steg
[Anslut med Excel](analysis-services-connect-excel.md)    
[Anslut med Power BI](analysis-services-connect-pbi.md)
