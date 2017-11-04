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
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: 9c9ceea9cc7fa9b6e4a63660bfa988bf50c0f5ae
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Klientbibliotek för att ansluta till Azure Analysis Services

Klientbibliotek är nödvändiga för program och verktyg att ansluta till Analysis Services-servrar. 

Analysis Services använder tre klientbibliotek. ADOMD.NET och tjänster objekt AMO (Analysis Management), är hanterade klientbibliotek. Analysis Services OLE DB-providern (MSOLAP DLL) är en inbyggd klientbiblioteket. Alla tre installeras vanligtvis på samma gång. Azure Analysis Services kräver den senaste versionen. 

Microsoft-klientprogram, till exempel Power BI Desktop- och Excel installera alla tre klientbibliotek. Men, beroende på version eller ofta uppdateras, klientbibliotek kanske inte de senaste versionerna som krävs av Azure Analysis Services. Detsamma gäller för anpassade program eller andra gränssnitt som AsCmd, TOM, ADOMD.NET. Dessa program kräver att du manuellt installerar biblioteken. Klientbibliotek för manuell installation ingår i SQL Server-funktionspaket som distribuerbara paket. Dessa klientbibliotek är knutna till SQL Server-versionen och kanske inte den senaste versionen.  

Klientbibliotek för klientanslutningar skiljer sig från dataleverantörer som krävs för att ansluta från Azure Analysis Services-servern till en datakälla. Mer information om datakällan anslutningar finns [Datasource anslutningar](analysis-services-datasource.md).

## <a name="download-the-latest-client-libraries"></a>Hämta senaste klientbibliotek  
Använd följande klientbibliotek om du är i en produktionsmiljö och kräver helt utgivna och stöds versioner.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Nästa steg
[Ansluta till Excel](analysis-services-connect-excel.md)    
[Anslut med Power BI](analysis-services-connect-pbi.md)
