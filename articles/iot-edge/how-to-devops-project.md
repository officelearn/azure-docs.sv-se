---
title: CI/CD-pipeline med Azure DevOps-projekt – Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en Azure IoT Edge-app väljer med några enkla steg.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074222"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps-projekt (förhandsgranskning)

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din IoT Edge-program med DevOps-projekt. DevOps Projects förenklar den initiala konfigurationen av en bygg- och versionspipeline i Azure Pipelines.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure DevOps. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).

1. I den vänstra fönsterrutan väljer du ikonen **Skapa en resurs** i det vänstra navigeringsfältet och söker sedan efter **DevOps Projects**.  

1.  Välj **Skapa**.

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogram och en Azure-tjänst

1. Azure IoT Edge-moduler kan skrivas [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) och [Java](tutorial-java-module.md). Välj det språk du föredrar att starta ett nytt program. På motsvarande sätt kan du välja **.NET**, **Node.js**, **Python**, **C**, eller **Java**, och klicka sedan på **Nästa**.

    ![Välj språk för att skapa ett nytt program](./media/how-to-devops-project/select-language.png)

2. Välj **enkla IoT (förhandsversion)**, och klicka sedan på **nästa**.

    ![Välj enkla IoT-ramverk](media/how-to-devops-project/select-iot.png)

3. Välj **IoT Edge**, och klicka sedan på **nästa**.

    ![Välj IoT Edge-tjänst](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration

1. Skapa en ny kostnadsfri Azure DevOps-organisation eller använd en befintlig organisation.

    a. Välj ett namn för projektet. 

    b. Välj din Azure-prenumeration och plats, välj ett namn för programmet och välj sedan **Klar**.  

    ![Namnge och skapa program](media/how-to-devops-project/select-devops.png)

1. Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. Ett exempel på IoT Edge-programmet har ställts in på en lagringsplats i din organisation med Azure DevOps, en version körs och programmet distribueras till IoT Edge-enhet. Den här instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och ditt program i Azure.

    ![Visa programmet i DevOps-portalen](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps Projects skapade en Git-lagringsplats i Azure Repos eller GitHub. Om du vill visa databasen och gör ändringar i koden i programmet, gör du följande:

1. Till vänster om DevOps Projects-instrumentpanelen väljer du länken för din **huvudgren**.  
Den här länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

1. Om du vill visa webbadressen för den klonade lagringsplatsen väljer du **Klona** längst upp till höger i webbläsaren. Du kan klona Git-lagringsplatsen i VS Code eller andra föredragna verktyg. I nästa steg du använda webbläsaren för att göra och commit kod ändrar direkt till huvudgrenen.

    ![Klona git-lagringsplats](media/how-to-devops-project/clone.png)

1. Till vänster i webbläsaren går du till den **modules/FilterModule/module.json** fil.

1. Välj **redigera**, och sedan ändrar till `"version"` under den `"tag"`. Du kan till exempel uppdatera den till `"version": "${BUILD_BUILDID}"` att använda [Azure DevOps Skapa variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) som en del av din Azure IoT Edge-modulen bildtagg.

    ![Redigera version för att godkänna build variabler](media/how-to-devops-project/update-module-json.png)

1. Välj **Checka in** och spara sedan ändringarna.

1. I webbläsaren går du till instrumentpanelen för Azure DevOps-projektet.  Du bör nu se att en version håller på att skapas. De ändringar du utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

    ![Visa status för pågående](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

I föregående steg konfigureras automatiskt en fullständig CI/CD-pipeline för din IoT Edge-program i Azure DevOps-projekt. Utforska och anpassa pipelinen efter behov. Bekanta dig med Azure DevOps-bygg- och versionspipelines genom att utföra följande steg.

1. Välj **Bygg-pipelines** längst upp på DevOps Projects-instrumentpanelen.  
Länken öppnar en webbläsarflik och Azure DevOps-bygg-pipelinen för det nya projektet.

1. Välj **Redigera**.

    ![Redigera build pipeline](media/how-to-devops-project/click-edit-button.png)

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen. Versionen utför olika uppgifter, till exempel hämtar källor från Git-lagringsplats, att skapa IoT Edge modulen bilder, push-överföra IoT Edge-moduler och publicera utdata som används som används för distributioner. Om du vill veta mer om Azure IoT Edge-uppgifter för CI, kan du besöka [konfigurera Azure Pipelines för kontinuerlig integrering](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration).

    ![Kontinuerlig integrering i uppgiftsvyn](media/how-to-devops-project/ci.png)

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande och välj alternativet för att **spara och placera i kö**. Välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.   
I fönsterrutan **Historik** ser du en spårningslogg över de senaste ändringarna för versionen.  Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**. DevOps Projects skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **versionen** under **Pipelines**. DevOps-projekt skapas en pipeline för versionen för att hantera distributioner till Azure IoT Edge.

    ![Visa releasepipeline](media/how-to-devops-project/release-pipeline.png)

1. Välj **Redigera**. Versionspipelinen innehåller en pipeline som definierar versionsprocessen.  

1. Under **Artefakter** väljer du **Släpp**. Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Intill ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.  

1. Till vänster väljer du **Uppgifter**. Uppgifter är de aktiviteter som distributionsprocessen utför. En uppgift har skapats för att distribuera dina modulen avbildningar till Azure IoT Edge i det här exemplet. Om du vill veta mer om Azure IoT Edge-uppgifter för CD, kan du besöka [konfigurera Azure Pipelines för kontinuerlig distribution](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment).

    ![Visa kontinuerlig distribution aktiviteter](media/how-to-devops-project/dev-release.png)

1. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

1. Välj ellipsen (...) bredvid en av versionerna och välj **Öppna**.  
Det finns flera menyer att utforska, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.

1. Välj **Incheckningar**. Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **Loggar**. Loggarna innehåller användbar information om distributionsprocessen. De kan visas både under och efter distributionerna.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg
* Läs om uppgifterna för Azure IoT Edge på Azure DevOps i [kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md)
* Förstå IoT Edge-distribution i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
