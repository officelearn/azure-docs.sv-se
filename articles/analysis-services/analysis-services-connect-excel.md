---
title: Anslut till Azure Analysis Services med Excel | Microsoft Docs
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Excel.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8414597c2c394e0b642ff47cba79c87488f56b24
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="connect-with-excel"></a>Anslut till Excel

När du har skapat en server i Azure och distribuerat en tabellmodell till den, är du redo att ansluta och utforska data.


## <a name="connect-in-excel"></a>Ansluta i Excel

Ansluter till en server i Excel stöds med hämta Data i Excel 2016. Det går inte att ansluta med hjälp av guiden Importera tabell i Power Pivot. 

**Ansluta Excel 2016**

1. I Excel 2016, på den **Data** band klickar du på **hämta externa Data** > **från andra källor** > **från Analysis Services**.

2. I guiden Dataanslutning i **servernamn**, ange namnet på servern inklusive protokoll och URI. I **logga in autentiseringsuppgifter**väljer **använder följande användarnamn och lösenord**, och skriv sedan organisationens användarnamnet, till exempel nancy@adventureworks.com, och lösenord.

    > [!NOTE]
    > Om du loggar in med ett Account, Live ID, Yahoo, Gmail, etc., eller du måste logga in med multifaktorautentisering, lämnar du lösenordsfältet tomt. Du uppmanas att ange ett lösenord när du klickar på Nästa.

    ![Ansluta från Excel-inloggning](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. I **Välj databas och tabell**, väljer du databasen och modellen eller perspektiv och klicka sedan på **Slutför**.
   
    ![Ansluta från Excel väljer modellen](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Se också
[Klientbibliotek](analysis-services-data-providers.md)   
[Hantera servern](analysis-services-manage.md)     


