---
title: Anslut till Azure Analysis Services med Excel | Microsoft Docs
description: "Lär dig hur du ansluter till en Azure Analysis Services-server med hjälp av Excel."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: f5f77b70874f10a29b4ea4dba307a67361c8e2bc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="connect-with-excel"></a>Anslut till Excel

När du har skapat en server i Azure och distribuerat en tabellmodell till den, är du redo att ansluta och utforska data.


## <a name="connect-in-excel"></a>Ansluta i Excel

Ansluter till en server i Excel stöds med hämta Data i Excel 2016. Det går inte att ansluta med hjälp av guiden Importera tabell i Power Pivot. 

**Ansluta Excel 2016**

1. I Excel 2016, på den **Data** band klickar du på **hämta externa Data** > **från andra källor** > **från Analysis Services**.

2. I guiden Dataanslutning i **servernamn**, ange namnet på servern inklusive protokoll och URI. I **logga in autentiseringsuppgifter**väljer **använder följande användarnamn och lösenord**, och skriv sedan organisationens användarnamnet, till exempel nancy@adventureworks.com, och lösenord.

    ![Ansluta från Excel-inloggning](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. I **Välj databas och tabell**, väljer du databasen och modellen eller perspektiv och klicka sedan på **Slutför**.
   
    ![Ansluta från Excel väljer modellen](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Se också
[Klientbibliotek](analysis-services-data-providers.md)   
[Hantera servern](analysis-services-manage.md)     


