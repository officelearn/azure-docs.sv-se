---
title: Distribuera fjärråtkomst övervakning lösningen - Azure | Microsoft Docs
description: Den här kursen visar hur du etablerar Fjärrövervaknings solution accelerator från azureiotsuite.com.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 42f6afcd3cb6880ba6c9cdd2a51e2a3e9ff2c2d4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626861"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator"></a>Distribuera fjärråtkomst övervakning solution accelerator

Den här kursen visar hur du etablerar Fjärrövervaknings solution accelerator. Du distribuerar lösningen från azureiotsuite.com. Du kan också distribuera lösningen med hjälp av CLI att lära dig om det här alternativet finns [distribuera en lösningsaccelerator från kommandoraden](iot-accelerators-remote-monitoring-deploy-cli.md).

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

    ![Välj Fjärrövervaknings](./media/iot-accelerators-remote-monitoring-deploy/remotemonitoring.png)

1. På den **skapa Fjärrövervaknings lösning** anger en **lösningsnamn** för din Fjärrövervaknings solution accelerator.

1. Välj en **grundläggande** eller **Standard** distribution. Om du distribuerar lösningen för att lära dig hur det fungerar eller för att köra en demonstration, Välj den **grundläggande** alternativet för att minimera kostnaderna.

1. Välj antingen **Java** eller **.NET** som språk. Alla mikrotjänster är tillgängliga som Java eller .NET-implementeringar.

1. Granska de **lösningsinformation** för mer information om dina konfigurationsalternativ.

1. Välj den **prenumeration** och **region** som du vill använda för att etablera lösningen.

1. Klicka på **Skapa lösning** för att påbörja etableringen. Denna process brukar ta flera minuter att köra:

    ![Fjärråtkomst övervakning lösningsinformation](./media/iot-accelerators-remote-monitoring-deploy/createform.png)

Information om felsökning finns [vad du gör om en distribution misslyckas](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) i GitHub-lagringsplatsen.

## <a name="sign-in-to-the-solution-accelerator"></a>Logga in på solution accelerator

När etableringen är klar kan du logga in på din Fjärrövervaknings lösningsaccelerator.

1. På den **etablerade lösningar** väljer du den nya Fjärrövervaknings lösningen:

    ![Välj ny lösning](./media/iot-accelerators-remote-monitoring-deploy/choosenew.png)

1. Du kan visa information om Fjärrövervaknings lösningen i panelen som visas. Välj **lösning instrumentpanelen** att ansluta till din lösning för övervakning av fjärråtkomst.

    > [!NOTE]
    > Du kan ta bort din lösning för övervakning av fjärråtkomst från den här panelen när du är klar med den.

    ![Lösning panelen](./media/iot-accelerators-remote-monitoring-deploy/solutionpanel.png)

1. Fjärrövervaknings lösning instrumentpanelen visas i webbläsaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera solution accelerator
> * Distribuera solution accelerator
> * Logga in på solution accelerator

Nu när du har distribuerat lösningen Fjärrövervaknings nästa steg är att [utforska funktionerna i lösningen instrumentpanelen](iot-accelerators-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->