---
title: Ansluta till Azure Analysis Services med Excel | Microsoft Docs
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5c46d4e4d23744cf07ccf7857a33990bf405a6a1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189022"
---
# <a name="connect-with-excel"></a>Anslut till Excel

När du har skapat en server och distribuerat en tabellmodell till den, kan klienterna ansluta och börja utforska data. 

## <a name="before-you-begin"></a>Innan du börjar

Du loggar in med kontot måste tillhöra en databasroll i modellen med minst läsbehörighet. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Ansluta i Excel

Ansluta till en server i Excel stöds genom att använda hämta Data i Excel 2016 och senare. Det går inte att ansluta med hjälp av guiden Importera tabell i Power Pivot. 

1. I Excel, på den **Data** menyfliksområdet, klickar du på **hämta externa Data** > **från andra källor** > **från Analysis Services**.

2. I guiden Dataanslutning i **servernamn**, ange namnet på servern, inklusive protokoll och URI: N. Till exempel asazure://westcentralus.asazure.windows.net/advworks. I **logga in autentiseringsuppgifter**väljer **använder följande användarnamn och lösenord**, och skriv sedan organisationens användarnamnet, till exempel nancy@adventureworks.com, och lösenord.

    > [!IMPORTANT]
    > Om du loggar in med en Account, Live ID, Yahoo, Gmail, etc., eller du måste logga in med multifaktorautentisering, lämna lösenordsfältet tomt. Du uppmanas ange ett lösenord när du klickar på Nästa. 

    ![Ansluta från Excel-inloggning](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. I **Markera databas och tabell**väljer databasen och modellen eller ett perspektiv och klicka sedan på **Slutför**.
   
    ![Ansluta från Excel väljer datamodellen](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Se också

[Klientbibliotek](analysis-services-data-providers.md)   
[Hantera servern](analysis-services-manage.md)     


