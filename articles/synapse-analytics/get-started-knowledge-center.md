---
title: 'Självstudie: kom igång utforska Synapse Knowledge Center'
description: I den här självstudien får du lära dig hur du använder Synapse Knowledge Center.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: c01d1bcb682a5f711dcba3cc7b32ef69b2642ef6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900770"
---
# <a name="explore-the-synapse-knowledge-center"></a>Utforska Synapse Knowledge Center

I den här självstudien får du lära dig hur du använder Synapse Studio Knowledge Center.

## <a name="getting-to-the-knowledge-center"></a>Komma till kunskaps centret

Det finns två sätt att hitta kunskaps Center i Synapse Studio:

  1. I Home Hub, under användbara länkar, klickar du på den första länken som kallas **kunskaps Center**.
  2. I meny fältet högst upp klickar du på **?** och sedan på  **kunskaps Center**.

Välj någon av metoderna och öppna **kunskaps centret**.

## <a name="overview"></a>Översikt

I **kunskaps centret** kan du göra tre saker:
* **Använd exempel omedelbart**. Det här alternativet är optimerat för att du ska kunna se analyser i åtgärd så snabbt som möjligt. Välj det här alternativet om du vill ha ett snabbt exempel på hur Synapse fungerar.
* **Tillgängligt exempel för webbläsare**. Med det här alternativet kan du länka exempel data uppsättningar och lägga till exempel kod i formatet SQL-skript, antecknings böcker och pipeliner.
* **Guidad visning Synapse Studio**. Det här alternativet tar dig en kort genom gång av de grundläggande delarna av Synapse Studio. Detta är användbart om du aldrig har använt Synapse Studio tidigare.

## <a name="exploring-blob-storage-with-sql-on-demand"></a>Utforska Blob Storage med SQL på begäran

1. **Kunskaps centret**, klicka på **Använd exempel omedelbart**
1. Välj **fråga data med SQL** 
1. Klicka på **Använd exempel omedelbart**
1. Ett nytt SQL-skript skapas.
1. Bläddra till den första frågan (rader 28 till 32) och markera frågetexten
1. Klicka på Kör. Den text som du har valt körs.

## <a name="loading-more-nyc-taxi-data"></a>Läser in fler NYC taxi-data

1. **Kunskaps centret**, klicka på **Bläddra bland tillgängliga exempel** 
1. Välj fliken **SQL-skript** överst
1. Välj **Läs in New York taxidata-datauppsättning**
1. Under **indata**väljer du **Välj en befintlig pool** och väljer **SQLDB1**
1. Klicka på **Öppna skript**
1. Ett nytt SQL-skript visas.
1. Klicka på **Kör**
1. Detta skapar flera tabeller för alla NYC taxi-data och läser in dem med hjälp av kommandot T-SQL COPY.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera med en SQL-pool](get-started-analyze-sql-pool.md)
