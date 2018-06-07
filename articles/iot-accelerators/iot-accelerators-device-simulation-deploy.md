---
title: Distribuera lösningen för enheten simuleringen - Azure | Microsoft Docs
description: Den här kursen visar hur du etablerar enheten simuleringen-lösning från azureiotsuite.com.
author: troyhopwood
manager: ''
ms.author: troyhop
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2017
ms.topic: conceptual
ms.openlocfilehash: e96c0f16ca4b69d103674b7f895c52d197280ca5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627014"
---
# <a name="deploy-the-azure-iot-device-simulation-solution"></a>Distribuera lösningen för simulering av Azure IoT-enhet

Den här kursen visar hur du etablerar en enhet simuleringen lösning. Du distribuerar lösningen från azureiotsuite.com.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera simulering av enheten lösningen
> * Distribuera lösningen för simulering av enheten
> * Logga in på enheten simuleringen-lösning

## <a name="prerequisites"></a>Förutsättningar

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-solution"></a>Distribuera lösningen

Innan du distribuerar lösningen till din Azure-prenumeration måste du välja vissa konfigurationsalternativ:

1. Logga in på [azureiotsuite.com](https://www.azureiotsolutions.com) med din Azure kontoautentiseringsuppgifter och klicka på **+** att skapa en ny lösning:

    ![Skapa en ny lösning](./media/iot-accelerators-device-simulation-deploy/createnewsolution.png)

1. Klicka på **Välj** på den **enheten simuleringen** panelen:

    ![Välj enhet simulering](./media/iot-accelerators-device-simulation-deploy/select.png)

1. På den **skapa enheten simuleringen lösning** anger en **lösningsnamn** för din enhet simuleringen lösning.

1. Välj den **prenumeration** och **region** som du vill använda för att etablera lösningen.

1. Ange om du vill att en ny IoT-hubb som distribueras med din enhet simuleringen lösning. Om den här kryssrutan är en ny IoT-hubb distribuerad till din prenumeration. Du kan alltid peka simuleringen på alla IoT-hubb oavsett val.

1. Klicka på **Skapa lösning** för att påbörja etableringen. Denna process brukar ta flera minuter att köra:

    ![Information om enhet simuleringen lösning](./media/iot-accelerators-device-simulation-deploy/createsolution.png)

## <a name="sign-in-to-the-solution"></a>Logga in på lösningen

När etableringen är klar, kan du logga in i enheten simuleringen lösningen.

1. På den **etablerade lösningar** klickar du på **starta** under den nya enheten simuleringen lösningen:

    ![Välj ny lösning](./media/iot-accelerators-device-simulation-deploy/newsolution.png)

1. Du kan visa information om enheten simuleringen lösningen i panelen som visas. Välj **lösning instrumentpanelen** att ansluta till din enhet simuleringen lösning.

    > [!NOTE]
    > Du kan ta bort din enhet simuleringen lösning på den här panelen när du är klar med den.

    ![Lösning panelen](./media/iot-accelerators-device-simulation-deploy/properties.png)

1. Enheten simuleringen lösning instrumentpanelen visas i webbläsaren.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera lösningen
> * Distribuera lösningen
> * Logga in på lösningen

Nu när du har distribuerat enheten simuleringen lösningen nästa steg är att [utforska funktionerna i enheten simuleringen lösningen](iot-accelerators-device-simulation-explore.md).

<!-- Next tutorials in the sequence -->
