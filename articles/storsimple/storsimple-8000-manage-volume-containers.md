---
title: Hantera din StorSimple-volymbehållare på StorSimple 8000-serien enheten | Microsoft Docs
description: Beskriver hur du kan använda StorSimple Enhetshanteraren volym behållare sidan för att lägga till, ändra eller ta bort en volymbehållare.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 0f8e00d6d07224f56625482f339e612e68914be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874845"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att hantera behållare för StorSimple-volym

## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du använda Enhetshanteraren för StorSimple-tjänsten för att skapa och hantera behållare för StorSimple-volym.

En volymbehållare i en Microsoft Azure StorSimple-enhet innehåller en eller flera volymer som har lagringskonto, kryptering och förbrukning av bandbreddsinställningarna. En enhet kan ha flera volymbehållare för alla volymer. 

En volymbehållare har följande attribut:

* **Volymer** – nivåer eller lokalt Fäst StorSimple-volymer som ingår i volymbehållaren. 
* **Kryptering** – en krypteringsnyckel som kan definieras för varje volymbehållare. Den här nyckeln används för att kryptera data som skickas från din StorSimple-enhet till molnet. En militära klass AES 256 bitarsnyckel används med nyckeln anges av användaren. Om du vill skydda dina data rekommenderar vi att du alltid aktiverar molnet lagringskryptering.
* **Storage-konto** – Azure storage-konto som används för att lagra data. Alla volymer som finns i en volymbehållare dela det här lagringskontot. Du kan välja ett lagringskonto från en befintlig lista eller skapa ett nytt konto när du skapar volymbehållaren och anger sedan autentiseringsuppgifterna för det kontot.
* **Molnet bandbredd** – bandbredden som används av enheten när data från enheten skickas till molnet. Du kan tillämpa en bandbreddskontroll genom att ange ett värde mellan 1 och 1 000 Mbps när du skapar den här behållaren. Om du vill att enheten använder all tillgänglig bandbredd, ange fältet till **obegränsad**. Du kan också skapa och tillämpa en bandbreddsmall för att allokera bandbredd enligt schemat.

Följande procedurer beskriver hur du använder den virtuella StorSimple **volymbehållare** bladet för att slutföra följande vanliga åtgärder:

* Lägg till volymbehållare
* Ändra en volymbehållare
* Ta bort en volymbehållare

## <a name="add-a-volume-container"></a>Lägg till volymbehållare
Utför följande steg för att lägga till en volymbehållare.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Ändra en volymbehållare
Utför följande steg om du vill ändra en volymbehållare.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Ta bort en volymbehållare
En volymbehållare har volymer i den. Det kan endast tas bort om de volymer som finns i den först tas bort. Utför följande steg för att ta bort en volymbehållare.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [hantera StorSimple-volymer](storsimple-8000-manage-volumes-u2.md). 
* Lär dig mer om [använder Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

