---
title: Använda Emulator Express för att köra och felsöka en Azure-molntjänst på en lokal dator | Microsoft Docs
description: Använda Emulator Express för att köra och felsöka en molntjänst på en lokal dator
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
ms.openlocfilehash: 3432e8778b2c5b5fd4db53a82ca23b55d75bcac5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969540"
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Använda Emulator Express för att köra och felsöka en Azure-molntjänst på en lokal dator
Genom att använda Emulator Express kan du testa och felsöka en tjänst i molnet utan att köra Visual Studio som administratör. Du kan ange dina Projektinställningar att använda Emulator Express eller den fullständiga emulatorn, beroende på kraven för din molntjänst. Läs mer om den fullständiga emulatorn [kör ett Azure-program i Compute-emulatorn](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Använda Emulator Express i Visual Studio
När du skapar ett Azure-projekt i Azure SDK 2.3 eller senare, används Emulator Express automatiskt. För befintliga projekt som har skapats med en tidigare version av Azure SDK, använder du följande steg för att välja Emulator Express:

1. Skapa eller öppna ett Azure cloud service-projekt i Visual Studio.

1. I **Solution Explorer**, högerklicka på projektet och, från snabbmenyn väljer **egenskaper**.

1. I egenskapssidorna projekt väljer du den **Web** fliken.

    ![Egenskaper för ett Azure cloud service-projekt](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. Under **lokala utvecklingsservern**väljer **Använd IIS Express alternativet**.

1. Under **emulatorn**väljer **använda Emulator Express**.
   
1. Om du vill starta Emulator Express, kör du följande kommando i Kommandotolken: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulator Express begränsningar
Följande problem är kända begränsningar i Emulator Express: 

- Emulator Express är inte kompatibel med IIS-webbserver.
- Din molntjänst kan innehålla flera roller, men varje roll är begränsad till en instans.
- Du kan inte komma åt portnummer nedan 1000. Om du använder en autentiseringsprovider som normalt använder en port nedan 1000 kan behöva du ändra det här värdet till ett portnummer som är över 1 000.
- Alla begränsningar som gäller för Azure Compute Emulator gäller även för Emulator Express. Du kan till exempel inte fler än 50 rollinstanser per distribution. Läs mer om Azure Compute Emulator [kör ett Azure-program i Compute-emulatorn](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Nästa steg
[Distribuera Azure cloud services](https://msdn.microsoft.com/library/azure/ee405479.aspx)
