---
title: Secure Azure Data Lake Analytics för flera användare
description: Lär dig hur du konfigurerar flera användare för att köra jobb i Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813369"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurera användaråtkomst till jobbinformation till jobbinformation i Azure Data Lake Analytics 

I Azure Data Lake Analytics kan du använda flera användarkonton eller tjänsthuvudnamn för att köra jobb. 

För att samma användare ska kunna se detaljerad jobbinformation måste användarna kunna läsa innehållet i jobbmapparna. Jobbmapparna finns i `/system/` katalogen. 

Om de nödvändiga behörigheterna inte har konfigurerats kan användaren se ett fel:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurera användaråtkomst till jobbinformation

Du kan använda **guiden Lägg till användare** för att konfigurera åtkomstkontrollistorna i mapparna. Mer information finns i [Lägga till en ny användare](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Om du behöver mer detaljerad kontroll, eller behöver skript behörigheterna, sedan säkra mapparna på följande sätt:

1. Bevilja **execute** körningsbehörigheter (via en åtkomst-åtkomstkontroll) i rotmappen:
   - /
   
2. Bevilja **körnings-** och **läsbehörigheter** (via en åtkomstkontrollista och en standard-åtkomstkontroll) på mapparna som innehåller jobbmapparna. För ett visst jobb som kördes den 25 maj 2018 måste dessa mappar till exempel användas:
   - /system
   - /system/jobservice
   - /system/jobservice/jobb
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Nästa steg
[Lägg till en ny användare](data-lake-analytics-manage-use-portal.md#add-a-new-user)
