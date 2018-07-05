---
title: Ansluta till Azure Analysis Services med Powerbi | Microsoft Docs
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Power BI.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 00a975ae3acb470912c702d8a37717e5c270c9af
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446800"
---
# <a name="connect-with-power-bi"></a>Anslut till Powerbi

När du har skapat en server i Azure och distribuerat en tabellmodell till den, är användare i din organisation redo att ansluta och börja utforska data. 

> [!TIP]
> Se till att använda den senaste versionen av [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Ansluta i Power BI Desktop

1. I Power BI Desktop klickar du på **Hämta data** > **Azure** > **Azure Analysis Services-databas**.

2. I **Server**, ange namnet på servern. Se till att inkludera en fullständig URL; till exempel asazure://westcentralus.asazure.windows.net/advworks.

3. I **databasen**, om du känner till namnet på den tabellmodelldatabas eller ett perspektiv som du vill ansluta till, klistra in den här. I annat fall kan du lämna fältet tomt och välja en databas eller ett perspektiv senare.

4. Välj ett anslutningsalternativ för och tryck sedan på **Connect**. 

    Båda **Anslut live** och **Import** alternativ stöds. Men rekommenderar vi att du använder live-anslutningar eftersom importläge har vissa begränsningar. framför, kan serverprestanda påverkas under importen. Även om modellen är uppdateras i Power BI-tjänsten i **tillåter åtkomst från Power BI** inställningen gäller bara när du väljer **Anslut live**.

5. Om du uppmanas, anger du dina inloggningsuppgifter. 

6. I **Navigator**, expandera servern och välj sedan modellen eller perspektiv som du vill ansluta till och klicka sedan på **Connect**. Klicka på en modell eller ett perspektiv för att visa alla objekt för vyn.

    Modellen öppnas i Power BI Desktop med en tom rapport i rapportvyn. Fältlistan visar alla ej dold modellobjekt. Anslutningsstatus visas i det nedre högra hörnet.

## <a name="connect-in-power-bi-service"></a>Ansluta i Power BI (tjänst)

1. Skapa en Power BI Desktop-fil som har en aktiv anslutning till din modell på din server.
2. I [Power BI](https://powerbi.microsoft.com), klickar du på **hämta Data** > **filer**, leta upp och välj din .pbix-fil.



## <a name="see-also"></a>Se också
[Ansluta till Azure Analysis Services](analysis-services-connect.md)   
[Klientbibliotek](analysis-services-data-providers.md)

