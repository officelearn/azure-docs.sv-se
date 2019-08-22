---
title: ta med fil
description: ta med fil
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: eb84dc1b5bf3f756e484ef27aaa998ab6b94cc51
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891649"
---
Ett experiment körs på ett beräknings mål, en beräknings resurs som är kopplad till din arbets yta.  När du har skapat ett beräknings mål kan du återanvända det för framtida körningar.

1. Kör experimentet genom att välja **Kör** längst ned.

1. Om din arbets yta redan har en beräknings resurs i dialog rutan **konfigurations mål** visas kan du välja den nu.  Annars väljer du **Skapa ny**.

    > [!NOTE]
    > Det visuella gränssnittet kan bara köra experiment på Machine Learning-beräkning mål. Andra beräknings mål visas inte.

1. Ange ett namn för beräknings resursen.

1. Välj **Kör**.

    ![Konfigurera beräknings mål](./media/aml-ui-create-training-compute/set-compute.png)

    Beräknings resursen kommer nu att skapas. Visa status i det övre högra hörnet av experimentet. 

    > [!NOTE]
    > Det tar cirka 5 minuter att skapa en beräknings resurs. När resursen har skapats kan du återanvända den och hoppa över vänte tiden för framtida körningar.
    >
    > Beräknings resursen skalar automatiskt till 0 noder när den är inaktiv för att spara pengar.  När du använder den igen efter en fördröjning kan du återigen uppleva cirka 5 minuters vänte tid medan den skalas upp.
