---
title: Azure Analysis Services klient bibliotek | Microsoft Docs
description: Beskriver klient bibliotek som krävs för klient program och-verktyg för att ansluta Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f78ac8686f23c47995cab9686c2ca3f6cf88fa6b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573058"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Klient bibliotek för anslutning till Azure Analysis Services

Klient bibliotek krävs för klient program och-verktyg för att ansluta till Analysis Services-servrar. Microsoft-klientprogram som Power BI Desktop, Excel, SQL Server Management Studio (SSMS) och Analysis Services Projects-tillägg för Visual Studio installerar alla tre klient biblioteken och uppdaterar dem tillsammans med regelbundna program uppdateringar. I vissa fall kan du behöva installera nyare versioner av klient biblioteken. Klient bibliotek måste också vara installerade för anpassade klient program.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Hämta de senaste klient biblioteken (Windows Installer)  

|Ladda ned  |Produkt version  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.5.15    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.5.15       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.0.9.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.0.9.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO och ADOMD (NuGet-paket)

Analysis Services Management Objects (AMO) och ADOMD-klient bibliotek är tillgängliga som Installerbara paket från [NuGet.org](https://www.nuget.org/). Vi rekommenderar att du migrerar till NuGet-referenser i stället för att använda Windows Installer. 

|Paket  | Produkt version  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.0.9     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.0.9      |

NuGet paket sammansättningar AssemblyVersion följer semantisk versions hantering: MAJOR. Del. 9.0a. NuGet-referenser läser in den förväntade versionen även om det finns en annan version i GAC (som orsakas av MSI-installation). KORRIGERINGen ökar för varje version. AMO-och ADOMD-versionerna hålls synkroniserade.

## <a name="understanding-client-libraries"></a>Förstå klient bibliotek

Analysis Services använder tre klient bibliotek, även kallade data leverantörer. ADOMD.NET och Analysis Services Management Objects (AMO) är hanterade klient bibliotek. Analysis Services OLE DB-provider (MSOLAP-DLL) är ett internt klient bibliotek. Normalt installeras alla tre samtidigt. **För Azure Analysis Services krävs de senaste versionerna av alla tre bibliotek**. 

Microsoft-klientprogram som Power BI Desktop och Excel installerar alla tre klient biblioteken och uppdaterar dem när nya versioner är tillgängliga. Beroende på version eller frekvensen av uppdateringar kanske vissa klient bibliotek inte är de senaste versionerna som krävs av Azure Analysis Services. Detsamma gäller för anpassade program eller andra gränssnitt som AsCmd, TOM, ADOMD.NET. Dessa program kräver manuell eller program mässigt installera biblioteken. Klient biblioteken för manuell installation ingår i SQL Server Feature Packs som distribuerbara paket. Dessa klient bibliotek är dock knutna till den SQL Server versionen och kanske inte är det senaste.  

Klient bibliotek för klient anslutningar skiljer sig från data leverantörer som krävs för att ansluta från en Azure Analysis Services-server till en data källa. Mer information om anslutningar för data källor finns i [anslutningar till data källor](analysis-services-datasource.md).

## <a name="client-library-types"></a>Klient biblioteks typer

### <a name="analysis-services-ole-db-provider-msolap"></a>Analysis Services OLE DB-provider (MSOLAP) 

 Analysis Services OLE DB-provider (MSOLAP) är det interna klient biblioteket för Analysis Services databas anslutningar. Den används indirekt av både ADOMD.NET och AMO och delegerar anslutnings begär anden till data leverantören. Du kan också anropa OLE DB-providern direkt från program koden.  
  
 Analysis Services OLE DB-provider installeras automatiskt av de flesta verktyg och klient program som används för att få åtkomst till Analysis Services-databaser. Den måste installeras på datorer som används för att komma åt Analysis Services data.  
  
 OLE DB-providers anges ofta i anslutnings strängar. En Analysis Services anslutnings sträng använder en annan nomenklatur för att referera till OLE DB providern: MSOLAP.\<version >. dll.

### <a name="amo"></a>AMO  

 AMO är ett hanterat klient bibliotek som används för Server Administration och data definition. Den installeras och används av verktyg och klient program. Exempel: SQL Server Management Studio (SSMS) använder AMO för att ansluta till Analysis Services. En anslutning med AMO är vanligt vis minimal, bestående av `"data source=\<servername>"`. När en anslutning har upprättats använder du API: et för att arbeta med databas samlingar och större objekt. Både Visual Studio och SSMS använder AMO för att ansluta till en Analysis Services-instans.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET är ett klient bibliotek för hanterade data som används för att skicka frågor till Analysis Services data. Den installeras och används av verktyg och klient program. 
  
 När du ansluter till en databas är egenskaperna för anslutnings strängen för alla tre bibliotek likartade. Nästan vilken anslutnings sträng som helst som du definierar för ADOMD.NET med hjälp av [Microsoft. AnalysisServices. AdomdClient. AdomdConnection. ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) fungerar även för AMO och Analysis Services OLE DB-Provider (MSOLAP). Mer information finns i [Egenskaper &#40;för anslutnings sträng Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Så här fastställer du klient biblioteks versionen   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Gå till `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Om du har mer än en mapp väljer du det högre talet.
  
2.  Högerklicka på **MSOLAP. dll** > **Egenskaper** > **information**. Om fil namnet är msolap140. dll, är det äldre än den senaste versionen och bör uppgraderas.
    
    ![Information om klient bibliotek](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Om du har mer än en mapp väljer du det högre talet.
2. Högerklicka på **Microsoft. AnalysisServices** > **Egenskaper** > **information**.  

### <a name="adomd"></a>ADOMD

1. Gå till `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Om du har mer än en mapp väljer du det högre talet.
2. Högerklicka på **Microsoft. AnalysisServices. AdomdClient** > **Egenskaper** > **information**.  


## <a name="next-steps"></a>Nästa steg
[Anslut till Excel](analysis-services-connect-excel.md)    
[Anslut med Power BI](analysis-services-connect-pbi.md)
