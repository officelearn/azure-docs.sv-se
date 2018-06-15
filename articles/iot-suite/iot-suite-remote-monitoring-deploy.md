---
title: Distribuera fjärråtkomst övervakningslösning - Azure | Microsoft Docs
description: Den här kursen visar hur du etablerar fjärråtkomst övervakning solution accelerator från azureiotsuite.com.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: eb78ce91755c43f1c6fedf62a70238df911b940f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33773640"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Distribuera fjärråtkomst övervakning solution accelerator

Den här kursen visar hur du etablerar fjärråtkomst övervakning solution accelerator. Du distribuerar lösningen från azureiotsuite.com. Du kan också distribuera lösningen med hjälp av CLI att lära dig om det här alternativet finns [distribuera en lösningsaccelerator från kommandoraden](iot-suite-remote-monitoring-deploy-cli.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera solution accelerator
> * Distribuera solution accelerator
> * Logga in på solution accelerator

## <a name="prerequisites"></a>Förutsättningar

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution-accelerator"></a>Distribuera solution accelerator

Innan du distribuerar solution accelerator till din Azure-prenumeration måste du välja vissa konfigurationsalternativ:

1. Logga in på [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) med dina inloggningsuppgifter för Azure-konto.

1. Klicka på **försök nu** på den **Fjärrövervaknings** panelen.

    ![Välj fjärrövervaknings](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. På den **skapa Fjärrövervaknings lösning** anger en **lösningsnamn** för din fjärråtkomst övervakning solution accelerator.

1. Välj en **grundläggande** eller **Standard** distribution. Om du distribuerar lösningen för att lära dig hur det fungerar eller för att köra en demonstration, Välj den **grundläggande** alternativet för att minimera kostnaderna.

1. Välj antingen **Java** eller **.NET** som språk. Alla mikrotjänster är tillgängliga som Java eller .NET-implementeringar.

1. Granska de **lösningsinformation** för mer information om dina konfigurationsalternativ.

1. Välj den **prenumeration** och **region** som du vill använda för att etablera lösningen.

1. Klicka på **Skapa lösning** för att påbörja etableringen. Denna process brukar ta flera minuter att köra:

    ![Fjärråtkomst övervakning lösningsinformation](media/iot-suite-remote-monitoring-deploy/createform.png)

Information om felsökning finns [vad du gör om en distribution misslyckas](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) i GitHub-lagringsplatsen.

## <a name="sign-in-to-the-solution-accelerator"></a>Logga in på solution accelerator

När etableringen är klar kan du logga in på din fjärranslutna övervakning solution accelerator.

1. På den **etablerade lösningar** väljer du den nya lösningen för fjärråtkomst övervakning:

    ![Välj ny lösning](media/iot-suite-remote-monitoring-deploy/choosenew.png)

1. Du kan visa information om din fjärranslutna övervakningslösning på panelen som visas. Välj **lösning instrumentpanelen** att ansluta till din fjärranslutna övervakningslösning.

    > [!NOTE]
    > Du kan ta bort din fjärranslutna övervakningslösning från den här panelen när du är klar med den.

    ![Lösning panelen](media/iot-suite-remote-monitoring-deploy/solutionpanel.png)

1. Instrumentpanelen för fjärråtkomst övervakning lösning visas i webbläsaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera solution accelerator
> * Distribuera solution accelerator
> * Logga in på solution accelerator

Nu när du har distribuerat remote övervakningslösning nästa steg är att [utforska funktionerna i lösningen instrumentpanelen](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->