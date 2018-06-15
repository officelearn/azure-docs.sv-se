---
title: Med hjälp av Express-emulatorn att köra och felsöka en Azure-molntjänst på en lokal dator | Microsoft Docs
description: Med hjälp av Express-emulatorn att köra och felsöka en molnbaserad tjänst på en lokal dator
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 638aa005321963ce8bdcbb7df4317fc4bdec74ae
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292208"
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Med hjälp av Express-emulatorn att köra och felsöka en Azure-molntjänst på en lokal dator
Du kan testa och felsöka en molnbaserad tjänst utan att köra Visual Studio som administratör genom att använda emulatorn Express. Du kan ange dina Projektinställningar att använda emulatorn Express eller den fullständiga emulatorn, beroende på kraven för din tjänst i molnet. Mer information om den fullständiga emulatorn finns [kör ett Azure-program i Compute Emulator](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Med hjälp av emulatorn i Visual Studio
När du skapar ett Azure-projekt i Azure SDK 2.3 eller senare används automatiskt emulatorn Express. För befintliga projekt som har skapats med en tidigare version av Azure SDK, använder du följande steg för att välja Express-emulatorn:

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn, Välj **egenskaper**.

1. Välj i egenskapssidor projekt i **Web** fliken.

    ![Egenskaper för ett Azure cloud service-projekt](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. Under **lokala utvecklingsserver**väljer **används IIS Express alternativet**.

1. Under **emulatorn**väljer **Använd emulatorn Express**.
   
1. Om du vill starta emulatorn Express, kör du följande kommando i Kommandotolken: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulatorn Express begränsningar
Följande är kända begränsningar i emulatorn Express: 

- Emulatorn Express är inte kompatibel med IIS-webbserver.
- Din molntjänst kan innehålla flera roller, men varje roll är begränsad till en instans.
- Du kan inte komma åt portnummer nedan 1000. Om du använder en autentiseringsprovider som normalt använder en port under 1000 kan behöva du ändra värdet till ett portnummer som är högre än 1000.
- Eventuella begränsningar som gäller för Azure Compute Emulator gäller även för Express-emulatorn. Du kan till exempel inte fler än 50 rollinstanser per distribution. Läs mer om Azure Compute Emulator [kör ett Azure-program i Compute Emulator](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Nästa steg
[Felsöka Azure-molntjänster](https://msdn.microsoft.com/library/azure/ee405479.aspx)
