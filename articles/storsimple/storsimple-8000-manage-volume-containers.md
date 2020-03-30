---
title: Hantera volymbehållare för StorSimple 8000-serieenheter
description: I artikeln beskrivs hur du kan använda sidan Volymbehållare för StorSimple-tjänstvolymbehållar för att lägga till, ändra eller ta bort en volymbehållare.
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
ms.openlocfilehash: cbdad3c68848ce552811ee658bb29df74a6fad19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267682"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Hantera StorSimple-volymcontainrar med StorSimple Device Manager-tjänsten

## <a name="overview"></a>Översikt
Den här självstudien förklarar hur du använder Tjänsten StorSimple Device Manager för att skapa och hantera StorSimple-volymbehållare.

En volymbehållare i en Microsoft Azure StorSimple-enhet innehåller en eller flera volymer som delar inställningar för lagringskonto, kryptering och bandbreddsförbrukning. En enhet kan ha flera volymbehållare för alla volymer. 

En volymbehållare har följande attribut:

* **Volymer** – De nivåindelade eller lokalt fästa StorSimple-volymerna som finns i volymbehållaren. 
* **Kryptering** – En krypteringsnyckel som kan definieras för varje volymbehållare. Den här nyckeln används för att kryptera data som skickas från din StorSimple-enhet till molnet. En militärklassad AES-256-bitnyckel används med den användarinmata nyckeln. För att skydda dina data rekommenderar vi att du alltid aktiverar molnlagringskryptering.
* **Lagringskonto** – Azure-lagringskontot som används för att lagra data. Alla volymer som finns i en volymbehållare delar det här lagringskontot. Du kan välja ett lagringskonto från en befintlig lista eller skapa ett nytt konto när du skapar volymbehållaren och sedan ange åtkomstautentiseringsuppgifter för det kontot.
* **Molnbandbredd** – Den bandbredd som förbrukas av enheten när data från enheten skickas till molnet. Du kan framtvinga en bandbreddskontroll genom att ange ett värde mellan 1 Mbit/s och 1 000 Mbit/s när du skapar den här behållaren. Om du vill att enheten ska förbruka all tillgänglig bandbredd ställer du in det här fältet på **Obegränsad**. Du kan också skapa och använda en bandbreddsmall för att allokera bandbredd baserat på schema.

Följande procedurer förklarar hur du använder bladet StorSimple **Volume containers** för att slutföra följande vanliga åtgärder:

* Lägga till en volymbehållare
* Ändra en volymbehållare
* Ta bort en volymbehållare

## <a name="add-a-volume-container"></a>Lägga till en volymbehållare
Utför följande steg för att lägga till en volymbehållare.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Ändra en volymbehållare
Gör följande för att ändra en volymbehållare.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Ta bort en volymbehållare
En volymbehållare har volymer i den. Det kan bara tas bort om alla volymer som finns i den tas först bort. Gör följande för att ta bort en volymbehållare.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [hanterar StorSimple-volymer](storsimple-8000-manage-volumes-u2.md). 
* Läs mer om [hur du använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

