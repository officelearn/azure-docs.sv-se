---
title: Ersätt din Azure Stack Edge-enhet | Microsoft Docs
description: Beskriver hur du får en ersättnings Azure Stack Edge-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 5d5e8cc3a959c7800019107d2c682e144449b46c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100119"
---
# <a name="replace-your-azure-stack-edge-device"></a>Ersätt din Azure Stack Edge-enhet

Den här artikeln beskriver hur du får en ersättnings Azure Stack Edge-enhet. En ersättnings enhet krävs när den befintliga enheten har ett maskin varu problem eller behöver uppgraderas. 


I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Öppna ett support ärende för maskin varu problem
> * Skapa en ny resurs för ersättnings enhet i Azure Portal
> * Installera, aktivera ersättnings enheten
> * Returnera den ursprungliga enheten

## <a name="open-a-support-ticket"></a>Öppna ett support ärende

Om din befintliga enhet har ett maskin varu problem öppnar du ett support ärende. Microsoft Support kommer att avgöra om en FRU-enhet (Field Replacement Unit) inte är tillgänglig för den här instansen eller om enheten behöver en maskin varu uppgradering. I båda fallen beställer support en ersättnings enhet.

1. Öppna ett support ärende med Microsoft Support som anger att du vill returnera enheten. Välj problem typen som **Azure Stack Edge-maskinvara**.

    ![Öppna supportbegäran](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. En Microsoft Support-tekniker kommer att kontakta dig. Ange leverans information.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Skapa en resurs för ersättnings enhet

Följ de här stegen för att skapa en resurs.

1. Följ stegen i [skapa en ny resurs](azure-stack-edge-deploy-prep.md#create-a-new-resource) för att skapa en resurs för ersättnings enheten. 

2. Se till att markera kryss rutan mot att **Jag har en Azure Stack Edge-enhet**. 

    ![Resurs för ersättnings enhet](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Installera och aktivera ersättnings enheten

Följ de här stegen för att installera och aktivera ersättnings enheten:

1. [Installera enheten](azure-stack-edge-deploy-install.md).

2. [Aktivera enheten](azure-stack-edge-deploy-connect-setup-activate.md) mot den nya resurs som du skapade tidigare.

## <a name="return-your-existing-device"></a>Returnera din befintliga enhet

Följ alla steg för att returnera den ursprungliga enheten:

1. [Radera data på enheten](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Starta enhets retur](azure-stack-edge-return-device.md#initiate-device-return) för den ursprungliga enheten.
3. [Schemalägg hämtningen](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Ta bort resursen](azure-stack-edge-return-device.md#delete-the-resource) som är kopplad till den returnerade enheten.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [returnerar en Azure Stack Edge-enhet](azure-stack-edge-return-device.md).
