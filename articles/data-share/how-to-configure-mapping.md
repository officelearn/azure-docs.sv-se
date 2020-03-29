---
title: Konfigurera en datauppsättningsmappning i Azure Data Share
description: Lär dig hur du konfigurerar en datauppsättningsmappning för en mottagen resurs med Hjälp av Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964251"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Konfigurera en datauppsättningsmappning för en mottagen resurs i Azure Data Share

I den här artikeln beskrivs hur du konfigurerar en datauppsättningsmappning för en mottagen resurs med Azure Data Share. Du vill göra detta om du har accepterat en inbjudan till datadelning men valt att acceptera och konfigurera senare, eller om data delas på plats. Du kanske vill konfigurera en datauppsättningsmappning om du behöver ändra målet för data som delas med dig, eller om du vill ta emot data till en SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navigera till en mottagen dataresurs

I Azure Data Share-tjänsten navigerar du till din mottagna resurs och väljer fliken **Information.** 

![Mappning av datauppsättning](./media/dataset-mapping.png "Mappning av datauppsättning") 

Markera rutan bredvid den datauppsättning som du vill tilldela ett mål till. Välj **Ta bort mappningen** om du vill ta bort mappningen. Välj **+ Karta för att rikta in dig** på för att välja en ny målbutik. 

![Karta till mål](./media/dataset-map-target.png "Karta till mål") 

## <a name="select-a-new-target-store"></a>Välj en ny målbutik

Välj en måldatatyp som du vill att data ska landa i. För ögonblicksbildbaserad delning flyttas inte alla data som redan finns i tidigare mappade lagringskonton automatiskt till det nya målarkivet. För delning på plats väljer du ett datalager på den angivna platsen. Platsen är Azure-datacenter där dataleverantörens källdatalager finns på.

![Mållagringskonto](./media/dataset-map-target-sql.png "Mållagring") 

## <a name="select-a-file-format-sql-sources-only"></a>Välj ett filformat (endast SQL-källor)

Om källdata kommer från en SQL-baserad källa kan du välja vilket format de tas emot i. 

![Välj format](./media/sql-file-formats.png "SQL-filformat")

## <a name="next-steps"></a>Nästa steg

Om du vill veta hur du börjar dela data fortsätter du till [att dela din datahandledning.](share-your-data.md)



