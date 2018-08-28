---
title: Skydda Azure Data Lake Analytics för flera användare
description: Lär dig hur du konfigurerar flera användare för att köra jobb i Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: c6b86e25602f36896855d2593952609904396879
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051592"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurera användaråtkomst till jobbinformation till jobbinformation i Azure Data Lake Analytics 

I Azure Data Lake Analytics kan du använda flera användarkonton eller tjänstens huvudnamn för att köra jobb. 

För dessa samma användare att se den detaljerade Jobbinformationen, måste användarna för att kunna läsa innehållet i mapparna jobbet. Jobb-mappar som finns i `/system/` directory. 

Om behörigheterna som krävs inte har konfigurerats, kan användaren ser ett fel: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurera användaråtkomst till jobbinformation

Du kan använda den **guiden Lägg till användare** att konfigurera ACL: er på mapparna. Mer information finns i [lägga till en ny användare](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Om du behöver mer detaljerad kontroll eller behovet av att skriptet behörighet, sedan skydda mappar enligt följande:

1. Bevilja **köra** behörigheter (via en åtkomst ACL) på rotmappen:
   - /
   
2. Bevilja **köra** och **läsa** behörigheter (via en åtkomst-ACL och standard-ACL) på mapparna som innehåller mappar för jobbet. Till exempel för ett specifikt jobb som körts på den 25 maj 2018 behöver mapparna komma åt:
   - / system
   - / system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - system/jobservice/jobb/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Nästa steg
[Lägg till en ny användare](data-lake-analytics-manage-use-portal.md#add-a-new-user)
