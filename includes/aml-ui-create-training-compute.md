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
ms.openlocfilehash: 1ee90e0c99234497b072bbee0b92d76129baea48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929660"
---
En pipeline körs på ett beräknings mål, som är en beräknings resurs som är kopplad till din arbets yta. När du har skapat ett beräknings mål kan du återanvända det för framtida körningar.

1. Välj **Kör** överst på arbets ytan för att köra pipelinen.

1. När fönstret **Inställningar** visas väljer du **Välj Compute Target (Välj Compute Target**).

    Om du redan har ett tillgängligt beräknings mål kan du välja att köra denna pipeline.

    > [!NOTE]
    > Designern kan bara köra experiment på Azure Machine Learning Compute-mål. Andra beräknings mål visas inte.

1. Ange ett namn för beräknings resursen.

1. Välj **Spara**.

    ![Konfigurera beräknings mål](./media/aml-ui-create-training-compute/set-compute.png)

1. Välj **Kör**.

1. I dialog rutan **Konfigurera pipeline-körning** väljer du **+ nytt experiment** för **experimentet**.

    > [!NOTE]
    > Experiment grupp liknande pipeliner körs tillsammans. Om du kör en pipeline flera gånger kan du välja samma experiment för efterföljande körningar.

    1. Ange ett beskrivande namn för **experimentets namn**.

    1. Välj **Kör**.
    
    Du kan visa körnings status och information överst till höger på arbets ytan.

    > [!NOTE]
    > Det tar cirka fem minuter att skapa en beräknings resurs. När resursen har skapats kan du återanvända den och hoppa över vänte tiden för framtida körningar.
    >
    > Beräknings resursen skalar automatiskt till noll noder när den är inaktiv för att spara pengar. När du använder den igen efter en fördröjning kan du uppleva ungefär fem minuters vänte tid medan den skalas upp.
