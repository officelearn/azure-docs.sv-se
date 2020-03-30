---
title: Aliasservernamn för Azure Analysis Services | Microsoft-dokument
description: Lär dig hur du skapar Azure Analysis Services-servernamnsalias. Användare kan sedan ansluta till servern med ett kortare aliasnamn i stället för servernamnet.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572281"
---
# <a name="alias-server-names"></a>Namn på aliasserver

Genom att använda ett servernamnsalias kan användare ansluta till din Azure Analysis Services-server med ett kortare *alias* i stället för servernamnet. När du ansluter från ett klientprogram anges aliaset som **link://** en slutpunkt med hjälp av link://-protokollformatet. Slutpunkten returnerar sedan det verkliga servernamnet för att kunna ansluta.

Alias server namn är bra för:

- Migrera modeller mellan servrar utan att påverka användarna. 
- Vänliga servernamn är lättare för användarna att komma ihåg. 
- Dirigera användare till olika servrar vid olika tidpunkter på dagen. 
- Dirigera användare i olika regioner till instanser som är geografiskt närmare, till exempel när du använder Azure Traffic Manager. 

Alla HTTPS-slutpunkter som returnerar ett giltigt Azure Analysis Services-servernamn kan fungera som ett alias. The endpoint must support HTTPS over port 443 and the port must not be specified in the URI.

![Alias med hjälp av länkformat](media/analysis-services-alias/aas-alias-browser.png)

När du ansluter från en klient anges aliasserverns namn med **link://** protokollformat. Till exempel i Power BI Desktop:

![Power BI-skrivbordsanslutning](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Skapa ett alias

Om du vill skapa en aliasslutpunkt kan du använda vilken metod som helst som returnerar ett giltigt Azure Analysis Services-servernamn. En referens till en fil i Azure Blob Storage som innehåller det verkliga servernamnet eller skapa och publicera ett ASP.NET webbformulärprogram.

I det här exemplet skapas ett ASP.NET webbformulärprogram i Visual Studio. Huvudsidesreferensen och användarkontrollen tas bort från sidan Default.aspx. Innehållet i Default.aspx är helt enkelt följande siddirektiv:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Händelsen Page_Load i Default.aspx.cs använder metoden Response.Write() för att returnera Azure Analysis Services-servernamnet.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Se även

[Klientbibliotek](analysis-services-data-providers.md)   
[Anslut från Power BI Desktop](analysis-services-connect-pbi.md)
