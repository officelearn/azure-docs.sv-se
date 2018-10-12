---
title: Azure Analysis Services-servern aliasnamn | Microsoft Docs
description: Beskriver hur du skapar och använder server name-alias.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc649379ffc375282b853687573266ded2bf80eb
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093773"
---
# <a name="alias-server-names"></a>Aliasnamn för servern

Genom att använda ett servernamnalias kan användare kan ansluta till Azure Analysis Services-servern med en kortare *alias* i stället för namnet på servern. När du ansluter från ett klientprogram, alias har angetts som en slutpunkt med hjälp av den **länk: / /** protocol-format. Slutpunkten returnerar sedan verkliga servernamnet för att kunna ansluta.

Alias-servernamn är bra för:

- Migrera modeller mellan servrar utan att påverka användare. 
- Eget namn är lättare för användarna att komma ihåg. 
- Dirigera användarna till olika servrar vid olika tidpunkter på dagen. 
- Dirigera användarna i olika regioner till instanser som är geografiskt närmare, t.ex. när du använder Azure Traffic Manager. 

HTTPS-slutpunkt som returnerar ett giltigt namn för Azure Analysis Services-server kan fungera som ett alias. Slutpunkten måste ha stöd för HTTPS via port 443 och porten får inte anges i URI: N.

![Alias med hjälp av länkformat](media/analysis-services-alias/aas-alias-browser.png)

När du ansluter från en klient, server aliasnamnet anges med hjälp av **länk: / /** protocol-format. Till exempel i Power BI Desktop:

![Power BI Desktop-anslutning](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Skapa ett alias

Du kan använda valfri metod som returnerar ett giltigt servernamn för Azure Analysis Services för att skapa ett alias-slutpunkt. Exempelvis kan en referens till en fil i Azure Blob Storage som innehåller verkliga server name, eller skapa och publicera ett ASP.NET Web Forms-program.

I det här exemplet skapas en ASP.NET Web Forms-program i Visual Studio. Huvudsida referens- och kontrollen tas bort från Default.aspx-sidan. Innehållet i Default.aspx är bara följande sida-direktiv:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Händelsen Page_Load i Default.aspx.cs använder Response.Write()-metod för att returnera namnet på Azure Analysis Services-servern.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Se också

[Klientbibliotek](analysis-services-data-providers.md)   
[Ansluta från Power BI Desktop](analysis-services-connect-pbi.md)
