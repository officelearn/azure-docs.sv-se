---
title: Azure Analysis Services-servern aliasnamn | Microsoft Docs
description: "Beskriver hur du skapar och använder server name-alias."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend
ms.openlocfilehash: a947dde1551c653faa54f088c1712c41c7657aa0
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="alias-server-names"></a>Alias servernamn

Med hjälp av ett Serveralias namn användare kan ansluta till din Azure Analysis Services-server med en kortare *alias* i stället för namnet på servern. När du ansluter från ett klientprogram alias har angetts som en slutpunkt med hjälp av den **länk: / /** protokollet format. Slutpunkten returnerar verkliga servernamnet för att kunna ansluta.

Alias-servernamn är bra för:

- Migrera modeller mellan servrar utan att påverka användare. 
- Eget namn är lättare för användare att komma ihåg. 
- Dirigera användarna till olika servrar vid olika tidpunkter på dagen. 
- Dirigera användarna i olika regioner till förekomster som geografiskt närmare, t.ex. när du använder Azure Traffic Manager. 

HTTP-slutpunkt som returnerar ett giltigt namn för Azure Analysis Services-server kan fungera som ett alias.

![Alias formatet länk](media/analysis-services-alias/aas-alias-browser.png)

När du ansluter från en klient, server aliasnamn anges med hjälp av **länk: / /** protokollet format. Till exempel i Power BI Desktop:

![Power BI Desktop-anslutning](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Skapa ett alias

Du kan använda valfri metod som returnerar ett giltigt namn på Azure Analysis Services för att skapa en slutpunkt som alias. En referens till en fil i Azure Blob Storage som innehåller den verkliga servern name, eller skapa och publicera ett program för ASP.NET-webbformulär.

I det här exemplet skapas en ASP.NET Web Forms-program i Visual Studio. Huvudsida referens- och kontrollen tas bort från sidan Default.aspx. Innehållet i Default.aspx är bara följande direktivet:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Page_Load-händelse i Default.aspx.cs används metoden Response.Write() för att returnera Azure Analysis Services-servernamnet.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Se även

[Klientbibliotek](analysis-services-data-providers.md)   
[Ansluta från Power BI Desktop](analysis-services-connect-pbi.md)
