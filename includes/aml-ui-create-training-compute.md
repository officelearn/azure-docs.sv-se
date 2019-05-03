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
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028522"
---
Ett experiment körs på ett beräkningsmål, en beräkningsresurs som är kopplad till din arbetsyta.  När du skapar ett beräkningsmål kan återanvända du den för framtida körningar.

1. Välj **kör** längst ned för att köra experimentet.

     ![Kör experimentet](./media/aml-ui-create-training-compute/run-experiment.png)

1. När den **installationsprogrammet Compute mål** dialogruta visas om din arbetsyta har redan en beräkningsresurs, kan du välja den nu.  Annars väljer **Skapa ny**.

    > [!NOTE]
    > Det visuella gränssnittet kan endast köra experiment på beräkning av Machine Learning mål. Andra beräkningsmål visas inte.

1. Ange ett namn för beräkningsresursen.

1. Välj **Kör**.

    ![Installationsprogrammet beräkningsmål](./media/aml-ui-create-training-compute/set-compute.png)

    Beräkningsresursen skapas nu. Visa status i det övre högra hörnet av experimentet. 

    > [!NOTE]
    > Det tar cirka 5 minuter att skapa en beräkningsresurs. När resursen har skapats kan du hoppa över det här väntetid för framtida körningar återanvända den.
    >
    > Beräkningsresursen autoskalas till 0 noder när den är inaktiv kan minska kostnaderna.  När du använder det igen när du har en fördröjning uppstå igen cirka 5 minuter för väntetid medan den skalar tillbaka upp.
