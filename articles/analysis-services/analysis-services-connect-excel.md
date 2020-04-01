---
title: Ansluta till Azure Analysis Services med Excel | Microsoft-dokument
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Excel. När användarna är anslutna kan de skapa pivottabeller för att utforska data.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b5c46c8b85af59efaf611300e2ab7129b3ef3cde
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411372"
---
# <a name="connect-with-excel"></a>Anslut med Excel

När du har skapat en server och distribuerat en tabellmodell till den kan klienter ansluta och börja utforska data. 

## <a name="before-you-begin"></a>Innan du börjar

Kontot du loggar in med måste tillhöra en modelldatabasroll med minst läsbehörighet. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Anslut i Excel

Det går inte att ansluta till en server i Excel med hjälp av Hämta data i Excel 2016 och senare. Det går inte att ansluta med guiden Importera tabell i Power Pivot. 

1. Klicka på **Hämta externa data** > **från andra källor** > **från Analysis Services**i menyfliksområdet **Data** i Excel .

2. Ange servernamnet inklusive protokoll och URI **i**guiden Dataanslutning i Guiden Dataanslutning. Till exempel asazure://westcentralus.asazure.windows.net/advworks. Välj sedan Använd följande **användarnamn och lösenord**i nancy@adventureworks.com **inloggningsuppgifter**och skriv sedan organisationsanvändarnamnet, till exempel , och lösenord.

    > [!IMPORTANT]
    > Om du loggar in med ett Microsoft-konto, live-ID, Yahoo, Gmail, etc., eller om du måste logga in med multifaktorautentisering, lämna lösenordsfältet tomt. Du uppmanas att ange ett lösenord när du har klickat på Nästa. 

    ![Ansluta från Excel-inloggning](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. Markera databasen och modellen eller perspektivet i **Välj databas och tabell**och klicka sedan på **Slutför**.
   
    ![Anslut från Excel select-modell](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Se även

[Klientbibliotek](analysis-services-data-providers.md)   
[Hantera servern](analysis-services-manage.md)     


