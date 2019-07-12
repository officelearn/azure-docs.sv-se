---
title: Konfigurera en datauppsättningsmappning
description: Konfigurera en datauppsättningsmappning
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789246"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Så här konfigurerar du en datauppsättning mappning av en mottagna resurs i Azure dela förhandsgranskningen

Den här artikeln förklarar hur du konfigurerar en dataset-mappning för en emot filresurs med hjälp av Azure förhandsgranskning för resursen. Du vill göra detta om du har accepterat inbjudan en data-resurs men valde att ”acceptera och konfigurera senare”. I annat fall kan du bara vill ändra källagringskontot för mottagna data. 

## <a name="navigate-to-a-received-data-share"></a>Navigera till en resurs för mottagna data

I tjänsten Azure Data delar, navigerar du till mottagna filresursen och välj den **information** fliken. 

![Mappning av datauppsättningen](./media/dataset-mapping.png "datauppsättning mappning") 

Markera kryssrutan bredvid den datauppsättning som du vill tilldela ett mål för och klicka på **+ mappas till målet**. Du kan behöva Avmappa först om du redan har konfigurerat en mållagringskontot och du vill ändra mappningen till ett annat lagringskonto. 

![Mappa till målet](./media/dataset-map-target.png "mappas till mål") 

## <a name="select-a-new-storage-account"></a>Välj ett nytt lagringskonto 

Välj ett lagringskonto som du vill att data hamnar i. Observera att alla data som redan finns i någon tidigare mappade storage-konton kommer inte flyttas automatiskt till det nya lagringskontot.

![Peka storage-konto](./media/map-target.png "Mållagringskontot") 

## <a name="next-steps"></a>Nästa steg

Information om hur du kan börja dela data, fortsätter du till den [dela dina data](share-your-data.md) självstudien.



