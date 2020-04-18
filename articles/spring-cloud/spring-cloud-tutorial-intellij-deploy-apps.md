---
title: Självstudiekurs - Använd IntelliJ för att distribuera Azure Spring Cloud-program
description: Använd IntelliJ för att distribuera program till Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: dbd0313c3edca8c5a875572fe52d7ec751ba65e8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641991"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>Använda IntelliJ för att distribuera Azure Spring Cloud-program
IntelliJ-plugin-programmet för Azure Spring Cloud stöder programdistribution från IntelliJ IDEA.  

## <a name="prerequisites"></a>Krav
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA Community Edition, version [2020.1]](https://www.jetbrains.com/idea/download/#section=windows)
* Eller: IntelliJ IDEA Ultimate Edition, version [2020.1]

## <a name="install-the-plug-in"></a>Installera plugin-programmet
Du kan Azure Toolkit för IntelliJ IDEA 3.35.0 från IntelliJ **Plugins** ANVÄNDARGRÄNSSNITTET.

1. Starta IntelliJ.  Om du har öppnat ett projekt tidigare stänger du projektet för att visa välkomstdialogrutan. Välj **Konfigurera** från länken längst ned till höger och klicka sedan på **Plugins** för att öppna dialogrutan för plugin-program och välj **Installera insticksprogram från disk**.

    ![Välj Konfigurera](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Sök efter Azure Toolkit for IntelliJ.  Klicka på **Installera**.

    ![Installera plugin-program](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klicka på **Starta om IDE**.

## <a name="tutorial-procedures"></a>Rutiner för handledning
Följande procedurer distribuerar ett Hello World-program med IntelliJ IDEA.

* Öppet gs-fjäderstartprojekt
* Distribuera till Azure Spring Cloud
* Visa strömmande loggar

## <a name="open-gs-spring-boot-project"></a>Öppet gs-fjäderstartprojekt

1. Hämta och packa upp källarkivet för den här självstudien, eller klona den med Git: git klonhttps://github.com/spring-guides/gs-spring-boot.git 
1. cd till gs-spring-boot\complete.
1. Öppna dialogrutan Välkommen **till** IntelliJ och välj **Importera projekt** för att öppna importguiden.
1. Välj `gs-spring-boot\complete` mapp.

    ![Importera projekt](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Distribuera till Azure Spring Cloud
För att kunna distribuera till Azure måste du logga in med ditt Azure-konto och välja din prenumeration.  Information om inloggning finns i [Installation och inloggning](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

1. Högerklicka på projektet i IntelliJ-projektutforskaren och välj **Azure** -> **Deploy till Azure Spring Cloud**.

    ![Distribuera till Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Acceptera namnet på appen i fältet **Namn.** **Namn** refererar till konfigurationen, inte appnamnet. Användare behöver vanligtvis inte ändra det.
1. Acceptera identifieraren från projektet för **artefakten**.
1. Välj **App:** och klicka sedan på **Skapa app...**.

    ![Distribuera till Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Ange **appnamn**och klicka sedan på **OK**.

    ![Distribuera till Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Starta distributionen genom att klicka på **Knappen Kör.** 

    ![Distribuera till Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Plugin-programmet kör kommandot `mvn package` i projektet och skapar sedan den nya appen `package` och distribuerar burken som genereras av kommandot.

1. Om app-URL:en inte visas i utdatafönstret hämtar du den från Azure-portalen. Navigera från din resursgrupp till instansen av Azure Spring Cloud.  Klicka sedan på **Appar**.  Den löpappen visas.

    ![Hämta test-URL](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navigera till webbadressen i webbläsaren.

    ![Navigera i webbläsare 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Visa strömmande loggar
Så här hämtar du loggarna:
1. Välj **Azure Explorer**och sedan Spring **Cloud**.
1. Högerklicka på den löpapp som körs.
1. Välj **Strömmande loggar** i listrutan.

    ![Välj strömmande loggar](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Välj instans.

    ![Välj förekomst](media/spring-cloud-intellij-howto/select-instance.png)

1. Strömningsloggen visas i utdatafönstret.

    ![Utdata för strömmande logg](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="see-also"></a>Se även
* [Skapa en Hello World-webbapp för Azure App Service i IntelliJ](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)
* [Förbereda ett Java Spring-program för distribution i Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment) 