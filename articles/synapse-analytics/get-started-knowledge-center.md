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
ms.openlocfilehash: 611d2163e242d7851398821344c3ed595df364cb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460257"
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
* **Använd exempel omedelbart**. Välj det här alternativet om du vill ha ett snabbt exempel på hur Synapse fungerar.
* **Bläddra i galleriet**. Med det här alternativet kan du länka exempel data uppsättningar och lägga till exempel kod i formatet SQL-skript, antecknings böcker och pipeliner.
* **Guidad visning Synapse Studio**. Det här alternativet tar dig en kort genom gång av de grundläggande delarna av Synapse Studio. Detta är användbart om du aldrig har använt Synapse Studio tidigare.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Utforska Blob Storage med Server lös SQL-pool

1. Gå till **kunskaps Center** och klicka på **Använd exempel omedelbart**
1. Välj **fråga data med SQL** 
1. Klicka på **Använd exempel omedelbart**
1. Ett nytt SQL-skript skapas.
1. Bläddra till den första frågan (rader 28 till 32) och markera frågetexten
1. Klicka på Kör. Den text som du har valt körs.

## <a name="loading-more-nyc-taxi-data"></a>Läser in fler NYC taxi-data
1. Gå till **kunskaps centret**, klicka på **Bläddra i galleriet** 
1. Välj fliken **SQL-skript** överst
1. Välj **Läs in New York taxidata-datauppsättning**
1. Under **indata** väljer du **Välj en befintlig pool** och väljer **SQLDB1**
1. Klicka på **Öppna skript**
1. Ett nytt SQL-skript visas.
1. Klicka på **Kör**
1. Detta skapar flera tabeller för alla NYC taxi-data och läser in dem med hjälp av kommandot T-SQL COPY.

    > [!NOTE] 
    > När du använder exempel galleriet för SQL-skript med en dedikerad SQL-pool (tidigare SQL DW) kommer du bara att kunna använda en befintlig dedikerad SQL-pool (tidigare SQL DW).

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
