---
title: Ansluta till Azure Analysis Services med Excel | Microsoft Docs
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Excel. När du är ansluten kan användarna skapa pivottabeller för att utforska data.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 123e271ae1b83603d599b9ef0381e25b3c963def
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85361565"
---
# <a name="connect-with-excel"></a>Anslut med Excel

När du har skapat en server och distribuerat en tabell modell till den, kan klienterna ansluta till och börja utforska data. 

## <a name="before-you-begin"></a>Innan du börjar

Det konto som du loggar in med måste tillhöra en modell databas roll med minst Läs behörighet. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Anslut i Excel

Att ansluta till en server i Excel stöds med hämta data i Excel 2016 och senare. Att ansluta med hjälp av guiden Importera tabell i Power Pivot stöds inte. 

1. I Excel, i menyfliksområdet för **data** , klickar du på **Hämta externa data**  >  **från andra källor**  >  **från Analysis Services**.

2. I guiden data anslutning, i **Server namn**, anger du Server namnet inklusive protokoll och URI. Till exempel asazure://westcentralus.asazure.windows.net/advworks. I **inloggnings uppgifter**väljer du sedan **Använd följande användar namn och lösen ord**och anger sedan organisationens användar namn, till exempel nancy@adventureworks.com och lösen ord.

    > [!IMPORTANT]
    > Om du loggar in med ett Microsoft-konto, Live ID, Yahoo, Gmail osv. eller om du behöver logga in med Multi-Factor Authentication lämnar du fältet lösen ord tomt. Du uppmanas att ange ett lösen ord när du klickar på Nästa. 

    ![Anslut från Excel-inloggning](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. I **Välj databas och tabell**väljer du databasen och modellen eller perspektivet och klickar sedan på **Slutför**.
   
    ![Anslut från Excel Välj modell](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Se även

[Klient bibliotek](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[Hantera servern](analysis-services-manage.md)     


