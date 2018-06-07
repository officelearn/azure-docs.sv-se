---
title: Anslut till Azure Analysis Services med Excel | Microsoft Docs
description: Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Excel.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6585648cbb461f52a22bc6ea239c0899caa2c2e7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597271"
---
# <a name="connect-with-excel"></a>Anslut till Excel

När du har skapat en server och distribuerat en tabellmodell till den, kan klienterna ansluta och börja utforska data. 

## <a name="before-you-begin"></a>Innan du börjar
Det konto som du loggar in med måste tillhöra en databasroll för modellen med minst läsbehörighet. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Ansluta i Excel

Ansluter till en server i Excel stöds med hämta Data i Excel 2016. Det går inte att ansluta med hjälp av guiden Importera tabell i Power Pivot. 

**Ansluta Excel 2016**

1. I Excel 2016, på den **Data** band klickar du på **hämta externa Data** > **från andra källor** > **från Analysis Services**.

2. I guiden Dataanslutning i **servernamn**, ange namnet på servern inklusive protokoll och URI. Till exempel asazure://westcentralus.asazure.windows.net/advworks. I **logga in autentiseringsuppgifter**väljer **använder följande användarnamn och lösenord**, och skriv sedan organisationens användarnamnet, till exempel nancy@adventureworks.com, och lösenord.

    > [!IMPORTANT]
    > Om du loggar in med ett Account, Live ID, Yahoo, Gmail, etc., eller du måste logga in med multifaktorautentisering, lämnar du lösenordsfältet tomt. Du uppmanas att ange ett lösenord när du klickar på Nästa. 

    ![Ansluta från Excel-inloggning](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. I **Välj databas och tabell**, väljer du databasen och modellen eller perspektiv och klicka sedan på **Slutför**.
   
    ![Ansluta från Excel väljer modellen](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Se också
[Klientbibliotek](analysis-services-data-providers.md)   
[Hantera servern](analysis-services-manage.md)     


