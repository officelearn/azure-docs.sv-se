---
title: Namn på Azure Analysis Services Ali Aset Server | Microsoft Docs
description: Beskriver hur du skapar och använder Server namns Ali Aset.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 161df354bae350df533f3991e551401fd56a4a65
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301075"
---
# <a name="alias-server-names"></a>Ali Aset Server namn

Genom att använda ett server namn alias kan användarna ansluta till din Azure Analysis Services-server med ett kortare *alias* i stället för Server namnet. När du ansluter från ett klient program, anges aliaset som en slut punkt med hjälp av protokoll formatet **Link://** . Slut punkten returnerar sedan det riktiga Server namnet för att ansluta.

Ali Asets Server namn är lämpliga för:

- Migrera modeller mellan servrar utan att påverka användare. 
- Användarvänliga Server namn är enklare för användare att komma ihåg. 
- Dirigera användare till olika servrar vid olika tidpunkter på dagen. 
- Dirigera användare i olika regioner till instanser som är geografiskt närmare, som när du använder Azure Traffic Manager. 

En HTTPS-slutpunkt som returnerar ett giltigt Azure Analysis Services server namn kan fungera som ett alias. Slut punkten måste ha stöd för HTTPS via port 443 och porten får inte anges i URI: n.

![Alias med länk format](media/analysis-services-alias/aas-alias-browser.png)

När du ansluter från en klient anges Ali Aset serverns namn med hjälp av protokoll formatet **Link://** . I Power BI Desktop till exempel:

![Power BI Desktop anslutning](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Skapa ett alias

Om du vill skapa en alias-slutpunkt kan du använda valfri metod som returnerar ett giltigt Azure Analysis Services server namn. Till exempel en referens till en fil i Azure Blob Storage som innehåller det riktiga Server namnet eller skapa och publicera ett ASP.NET webb formulär program.

I det här exemplet skapas ett ASP.NET webb formulär program i Visual Studio. Huvud sid referensen och användar kontrollen tas bort från default. aspx-sidan. Innehållet i default. aspx är bara följande sid direktiv:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Händelsen Page_Load i Default.aspx.cs använder metoden Response. write () för att returnera Azure Analysis Services server namnet.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Se också

[Klient bibliotek](analysis-services-data-providers.md)   
[Anslut från Power BI Desktop](analysis-services-connect-pbi.md)
