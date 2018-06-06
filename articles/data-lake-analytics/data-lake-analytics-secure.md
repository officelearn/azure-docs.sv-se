---
title: Säker Azure Data Lake Analytics för flera användare
description: Lär dig hur du konfigurerar flera användare att köra jobb i Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 1d92e6d0e619584dedcbc9a66450c25dd1ac8b75
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701421"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurera användaråtkomst till jobbinformation till jobbinformation i Azure Data Lake Analytics 

I Azure Data Lake Analytics kan du använda flera användarkonton eller tjänstens huvudnamn för att köra jobb. 

För dessa användare att se den detaljerade Jobbinformationen, måste användarna ska kunna läsa innehållet i mapparna jobb. Jobbet mappar finns i `/system/` directory. 

Om behörigheterna som krävs inte har konfigurerats, kan användaren se ett fel: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurera användaråtkomst till jobbinformation

Du kan använda den **guiden Lägg till användare** att konfigurera åtkomstkontrollistor för mappar. Mer information finns i [lägga till en ny användare](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Om du behöver mer detaljerad kontroll eller behöver skript behörigheter, sedan skydda mappar enligt följande:

1. Bevilja **köra** behörigheter (via en åtkomst ACL) på rotmappen:
   - /
   
2. Bevilja **köra** och **läsa** behörigheter (via en ACL för åtkomst och standard ACL) på de mappar som innehåller mappar för jobbet. Till exempel för ett specifikt jobb som kördes på den 25 maj 2018 behöver mapparna komma åt:
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
