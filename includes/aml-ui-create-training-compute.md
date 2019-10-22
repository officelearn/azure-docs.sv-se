---
title: ta med fil
description: ta med fil
services: machine-learning
author: peterclu
ms.service: machine-learning
ms.author: peterlu
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/09/2019
ms.openlocfilehash: 28478f38df6ba6ea356626cc36c23ad498fc1f47
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692238"
---
En pipeline körs på ett beräknings mål, en beräknings resurs som är kopplad till din arbets yta.  När du har skapat ett beräknings mål kan du återanvända det för framtida körningar.

1. Välj **Kör** överst på arbets ytan för att köra pipelinen.

1. När fönstret **Inställningar** visas väljer du **Välj Compute Target (Välj Compute Target**).

    Om du redan har ett tillgängligt beräknings mål kan du välja att köra denna pipeline.

    > [!NOTE]
    > Det visuella gränssnittet kan bara köra experiment på Machine Learning-beräkning mål. Andra beräknings mål visas inte.

1. Ange ett namn för beräknings resursen.

1. Välj **Spara**.

    ![Konfigurera beräknings mål](./media/aml-ui-create-training-compute/set-compute.png)

1. Välj **Kör**.

1. I dialog rutan **Konfigurera pipeline-körning** väljer du **+ nytt experiment** för **experimentet**

    * Ange ett beskrivande **experiment namn**

    * Välj **Kör**
    
    Du kan visa körnings status och information överst till höger på arbets ytan.

    > [!NOTE]
    > Det tar cirka 5 minuter att skapa en beräknings resurs. När resursen har skapats kan du återanvända den och hoppa över vänte tiden för framtida körningar.
    >
    > Beräknings resursen skalar automatiskt till 0 noder när den är inaktiv för att spara pengar.  När du använder den igen efter en fördröjning kan du återigen uppleva cirka 5 minuters vänte tid medan den skalas upp.
