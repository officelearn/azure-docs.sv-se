---
title: Konfigurera en data uppsättnings mappning i Azure Data Share
description: Lär dig hur du konfigurerar en data uppsättnings mappning för en mottagen resurs med hjälp av Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490626"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Så här konfigurerar du en data uppsättnings mappning för en mottagen resurs i Azure Data Share

Den här artikeln beskriver hur du konfigurerar en data uppsättnings mappning för en mottagen resurs med hjälp av Azure Data Share. Du måste göra detta om du har accepterat en inbjudan om data delning men valt att godkänna och konfigurera senare. Du kanske vill konfigurera en data uppsättnings mappning om du behöver ändra målet för data som delas med dig, eller om du vill ta emot data i en SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Navigera till en mottagen data resurs

I Azure Data Share-tjänsten navigerar du till din mottagna resurs och väljer fliken **information** . 

![Mappning av data uppsättning](./media/dataset-mapping.png "Mappning av data uppsättning") 

Markera kryss rutan bredvid den data uppsättning som du vill tilldela ett mål. Välj **mappning** för att ta bort mappningen till den befintliga mappningen. Välj **+ Mappa till mål** för att välja ett nytt mål lager. 

![Mappa till mål](./media/dataset-map-target.png "Mappa till mål") 

## <a name="select-a-new-destination-store"></a>Välj ett nytt mål lager

Välj en mål data typ som du vill att data ska hamna i. Observera att alla data som redan finns i tidigare mappade lagrings konton automatiskt flyttas till det nya målet.

![Mål lagrings konto](./media/dataset-map-target-sql.png "Mål lagring") 

## <a name="select-a-file-format-sql-sources-only"></a>Välj ett fil format (endast SQL-källor)

Om käll informationen kommer från en SQL-baserad källa kan du välja vilket format den tas emot i. 

![Välj format](./media/sql-file-formats.png "SQL-filformat")

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du börjar dela data fortsätter du till kursen [dela data](share-your-data.md) .



