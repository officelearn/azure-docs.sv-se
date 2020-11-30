---
title: Machine Learning Services i Azure SQL-hanterad instans (för hands version)
description: Den här artikeln innehåller en översikt eller Machine Learning Services i Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: c805bacbd4a2219fb79168ad6426efd8b0a390df
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324524"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Machine Learning Services i Azure SQL-hanterad instans (för hands version)

Machine Learning Services är en funktion i Azure SQL-hanterad instans (för hands version) som tillhandahåller maskin inlärning i databasen med stöd för både python-och R-skript. Funktionen innehåller Microsoft python-och R-paket för förutsägelse analys och maskin inlärning med höga prestanda. Relations data kan användas i skript genom lagrade procedurer, T-SQL-skript som innehåller python-eller R-uttryck, eller python-eller R-kod som innehåller T-SQL.

> [!IMPORTANT]
> Machine Learning Services är en funktion i Azure SQL Managed Instance som för närvarande är i allmänt tillgänglig förhandsversion.
> Den här förhands gransknings funktionen är inlednings vis tillgänglig i ett begränsat antal regioner i USA, Europa, och Australien med ytterligare regioner som läggs till senare.
>
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> [Registrera dig för för hands versionen](#signup) nedan.

## <a name="what-is-machine-learning-services"></a>Vad är Machine Learning Services?

Machine Learning Services i Azure SQL-hanterad instans kan du köra python-och R-skript i-databasen. Du kan använda den för att förbereda och rensa data, utföra funktions teknik och träna, utvärdera och distribuera maskin inlärnings modeller i en databas. Funktionen kör skripten där data finns och eliminerar överföringen av data över nätverket till en annan server.

Använd Machine Learning Services med R/python-stöd i Azure SQL-hanterad instans för att:

- **Kör r-och Python-skript för förberedelse av data och generell användning av data bearbetning** – du kan nu hämta R/Python-skript till Azure SQL-hanterad instans där dina data finns, i stället för att behöva flytta ut data till en annan server för att köra R-och Python-skript. Du kan eliminera behovet av data förflyttning och relaterade problem som rör svars tid, säkerhet och efterlevnad.

- **Träna maskin inlärnings modeller i databasen** – du kan träna modeller med alla algoritmer för öppen källkod. Du kan enkelt skala din utbildning till hela data uppsättningen i stället för att förlita dig på exempel data uppsättningar som hämtas från databasen.

- **Distribuera modeller och skript till produktion i lagrade procedurer** – skripten och de tränade modellerna kan användas helt enkelt genom att de bäddas in i T-SQL-lagrade procedurer. Appar som ansluter till en hanterad Azure SQL-instans kan dra nytta av förutsägelser och intelligens i dessa modeller genom att bara anropa en lagrad procedur. Du kan också använda den inbyggda T-SQL PREDICT-funktionen för att operationalisera modeller för snabba resultat i scenarier med real tids resultat i real tid.

Bas distributioner av python och R ingår i Machine Learning Services. Du kan installera och använda paket och ramverk med öppen källkod, till exempel PyTorch, TensorFlow och scikit-lär, förutom Microsoft-paketen [revoscalepy](/sql/advanced-analytics/python/ref-py-revoscalepy) och [microsoftml](/sql/advanced-analytics/python/ref-py-microsoftml) för python, och [RevoScaleR](/sql/advanced-analytics/r/ref-r-revoscaler), [microsoftml](/sql/advanced-analytics/r/ref-r-microsoftml), [OLAP](/sql/advanced-analytics/r/ref-r-olapr)och [sqlrutils](/sql/advanced-analytics/r/ref-r-sqlrutils) för R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrera dig för förhandsversionen

Denna begränsade offentliga för hands version omfattas av [villkoren för för hands versionen av Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Om du är intresse rad av att delta i för hands versions programmet och accepterar dessa villkor kan du begära registrering genom att skapa ett support ärende för Azure på [**https://azure.microsoft.com/support/create-ticket/**](https://azure.microsoft.com/support/create-ticket/) . 

1. På sidan **skapa ett support ärende** klickar du på **skapa en incident**.

1. På sidan **Hjälp + Support** klickar du på **ny supportbegäran** om du vill skapa ett nytt ärende.

1. Välj följande alternativ:
   - Typ av problem – **teknisk**
   - Prenumeration – *Välj din prenumeration*
   - Tjänst- **SQL-hanterad instans**
   - Resurs – *Välj din hanterade instans*
   - Sammanfattning – *Ange en kort beskrivning av din begäran*
   - Problem typ- **Machine Learning Services för SQL-hanterad instans (för hands version)**
   - Underordnat problem – **andra problem eller frågor om hur**

1. Klicka på **Nästa: lösningar**.

1. Läs informationen om förhands granskningen och klicka sedan på **Nästa: information**.

1. På den här sidan:
   - För frågan **försöker du registrera dig för för hands versionen?** Välj **Ja**. 
   - För **Beskrivning** anger du information om din begäran, inklusive det logiska Server namn, den region och det PRENUMERATIONS-ID som du vill registrera i förhands granskningen. Ange andra uppgifter efter behov.
   - Välj önskad kontakt metod. 

1. När du är klar klickar du på **Nästa: granska + skapa** och klicka sedan på **skapa**.

När du har registrerat dig i programmet introducerar Microsoft dig och aktiverar Machine Learning Services för din befintliga eller nya databas.

Machine Learning Services i SQL-hanterad instans är inte rekommenderat för produktionsarbetsbelastningar under den allmänt tillgängliga förhandsversionen.

## <a name="next-steps"></a>Nästa steg

- Se [viktiga skillnader från SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Information om hur du använder python i Machine Learning Services finns i [köra Python-skript](/sql/machine-learning/tutorials/quickstart-python-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Information om hur du använder R i Machine Learning Services finns i [Kör R-skript](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context&view=sql-server-ver15).
- Mer information om Machine Learning på andra SQL-plattformar finns i [dokumentationen för SQL Machine Learning](/sql/machine-learning/).