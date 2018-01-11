---
title: "Distribuera fjärråtkomst övervakningslösning - Azure | Microsoft Docs"
description: "Den här kursen visar hur du etablerar fjärråtkomst övervakning förkonfigurerade lösningen från azureiotsuite.com."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 768c32e30509c74a292b2355a249ec010af2cd1b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>Distribuera den förkonfigurerade lösningen för fjärrövervakning

I den här självstudiekursen lär du dig hur du etablerar den förkonfigurerade lösningen för fjärrövervakning. Du distribuerar lösningen från azureiotsuite.com. Du kan också distribuera lösningen med hjälp av CLI att lära dig om det här alternativet finns [distribuerar en förkonfigurerade lösning från kommandoraden](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line).

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Konfigurera förkonfigurerade lösningen
> * Distribuera förkonfigurerade lösningen
> * Logga in på den förkonfigurerade lösningen

## <a name="prerequisites"></a>Krav

Du behöver en aktiv Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="deploy-the-preconfigured-solution"></a>Distribuera förkonfigurerade lösningen

Innan du distribuerar den förkonfigurerade lösningen till din Azure-prenumeration måste du välja vissa konfigurationsalternativ:

1. Logga in på [azureiotsuite.com](https://www.azureiotsuite.com) med din Azure kontoautentiseringsuppgifter och klicka på  **+**  att skapa en ny lösning:

    ![Skapa en ny lösning](media/iot-suite-remote-monitoring-deploy/createnewsolution.png)

1. Klicka på **Välj** på den **fjärråtkomst övervakning preview** panelen.

    ![Välj fjärrövervaknings](media/iot-suite-remote-monitoring-deploy/remotemonitoring.png)

1. På den **skapa Fjärrövervaknings lösning** anger en **lösningsnamn** för fjärråtkomst övervakningen förkonfigurerade lösningen.

1. Välj en **grundläggande** eller **Standard** distribution. Om du distribuerar lösningen för att lära dig hur det fungerar eller för att köra en demonstration, Välj den **grundläggande** alternativet för att minimera kostnaderna.

1. Välj antingen **Java** eller **.NET** som språk. Alla mikrotjänster är tillgängliga som Java eller .NET-implementeringar.

1. Granska de **lösningsinformation** för mer information om dina konfigurationsalternativ.

1. Välj den **prenumeration** och **region** som du vill använda för att etablera lösningen.

1. Klicka på **Skapa lösning** för att påbörja etableringen. Denna process brukar ta flera minuter att köra:

    ![Fjärråtkomst övervakning lösningsinformation](media/iot-suite-remote-monitoring-deploy/createform.png)

Information om felsökning finns [vad du gör om en distribution misslyckas](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails) i GitHub-lagringsplatsen.

## <a name="sign-in-to-the-preconfigured-solution"></a>Logga in på den förkonfigurerade lösningen

När etableringen är klar kan du logga in i fjärråtkomst övervakning förkonfigurerade lösningen.

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
> * Konfigurera förkonfigurerade lösningen
> * Distribuera förkonfigurerade lösningen
> * Logga in på den förkonfigurerade lösningen

Nu när du har distribuerat remote övervakningslösning nästa steg är att [utforska funktionerna i lösningen instrumentpanelen](./iot-suite-remote-monitoring-explore.md).

<!-- Next tutorials in the sequence -->