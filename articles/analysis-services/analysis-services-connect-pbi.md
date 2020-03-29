---
title: Ansluta till Azure Analysis Services med Power BI | Microsoft-dokument
description: Lär dig hur du ansluter till en Azure Analysis Services-server med Power BI. När användarna är anslutna kan de utforska modelldata.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: db0c00268c343cd99e439bb49460523cf0563c3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73573101"
---
# <a name="connect-with-power-bi"></a>Anslut med Power BI

När du har skapat en server i Azure och distribuerat en tabellmodell till den, är användarna i organisationen redo att ansluta och börja utforska data. 

> [!TIP]
> Var noga med att använda den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ansluta i Power BI Desktop

1. Klicka på Hämta **data** > **Azure** > **Azure Analysis Services-databas**i Power BI Desktop .

2. Ange servernamnet i **Server.** Var noga med att inkludera den fullständiga webbadressen. till exempel asazure://westcentralus.asazure.windows.net/advworks.

3. Om du känner till namnet på den tabellmodelldatabas eller det perspektiv som du vill ansluta till i **Databas,** klistrar du in den här. Annars kan du lämna det här fältet tomt och välja en databas eller ett perspektiv senare.

4. Välj ett anslutningsalternativ och tryck sedan på **Anslut**. 

    Både **Direkt-** och **importalternativ** stöds. Vi rekommenderar dock att du använder live-anslutningar eftersom importläget har vissa begränsningar. framför allt kan serverprestanda påverkas under importen. Om modellen ska uppdateras i Power BI-tjänsten gäller inställningen **Tillåt åtkomst från Power BI** endast när du väljer Anslut **live**.

5. Om du uppmanas till det anger du dina inloggningsuppgifter. 

6. Expandera servern i **Navigator**och välj sedan den modell eller det perspektiv som du vill ansluta till och klicka sedan på **Anslut**. Klicka på en modell eller ett perspektiv om du vill visa alla objekt för den vyn.

    Modellen öppnas i Power BI Desktop med en tom rapport i rapportvyn. I listan Fält visas alla modellobjekt som inte är dolda. Anslutningsstatus visas i det nedre högra hörnet.

## <a name="connect-in-power-bi-service"></a>Anslut i Power BI (tjänst)

1. Skapa en Power BI Desktop-fil som har en live-anslutning till din modell på servern.
2. Klicka på **Hämta datafiler i** > **Files** [Power BI](https://powerbi.microsoft.com)och leta sedan reda på och markera PBIX-filen.

## <a name="see-also"></a>Se även
[Ansluta till Azure Analysis Services](analysis-services-connect.md)   
[Klientbibliotek](analysis-services-data-providers.md)

