---
title: 'Självstudie: kom igång utforska Synapse Knowledge Center'
description: I den här självstudien får du lära dig hur du använder Synapse Knowledge Center.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 57213e228359fd6c201d501d3bb75f7267054277
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685434"
---
# <a name="explore-the-synapse-knowledge-center"></a>Utforska Synapse Knowledge Center

I den här självstudien får du lära dig hur du använder Synapse Studio Knowledge Center.

## <a name="getting-to-the-knowledge-center"></a>Komma till kunskaps centret

Det finns två sätt att hitta kunskaps Center i Synapse Studio:

  1. I Home Hub, längst upp till höger på sidan, klickar du på **Lär dig**.
  2. I meny fältet högst upp klickar du på **?** och sedan på  **kunskaps Center**.

Välj någon av metoderna och öppna **kunskaps centret**.

## <a name="overview"></a>Översikt

I **kunskaps centret** kan du göra tre saker:
* **Använd exempel omedelbart**. Det här alternativet är optimerat för att du ska kunna se analyser i åtgärd så snabbt som möjligt. Välj det här alternativet om du vill ha ett snabbt exempel på hur Synapse fungerar.
* **Tillgängligt exempel för webbläsare**. Med det här alternativet kan du länka exempel data uppsättningar och lägga till exempel kod i formatet SQL-skript, antecknings böcker och pipeliner.
* **Guidad visning Synapse Studio**. Det här alternativet tar dig en kort genom gång av de grundläggande delarna av Synapse Studio. Detta är användbart om du aldrig har använt Synapse Studio tidigare.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Utforska Blob Storage med Server lös SQL-pool

1. Gå till **kunskaps Center** och klicka på **Använd exempel omedelbart**
1. Välj **fråga data med SQL** 
1. Klicka på **Använd exempel omedelbart**
1. Ett nytt SQL-skript skapas.
1. Bläddra till den första frågan (rader 28 till 32) och markera frågetexten
1. Klicka på Kör. Den text som du har valt körs.

## <a name="loading-more-nyc-taxi-data"></a>Läser in fler NYC taxi-data
1. Gå till **kunskaps centret**, klicka på **Bläddra bland tillgängliga exempel** 
1. Välj fliken **SQL-skript** överst
1. Välj **Läs in New York taxidata-datauppsättning**
1. Under **indata** väljer du **Välj en befintlig pool** och väljer **SQLDB1**
1. Klicka på **Öppna skript**
1. Ett nytt SQL-skript visas.
1. Klicka på **Kör**
1. Detta skapar flera tabeller för alla NYC taxi-data och läser in dem med hjälp av kommandot T-SQL COPY.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
