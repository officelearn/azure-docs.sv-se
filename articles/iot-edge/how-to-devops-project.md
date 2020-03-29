---
title: CI/CD-pipeline med Azure DevOps-projekt – Azure IoT Edge | Microsoft-dokument
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en Azure IoT Edge-app som du väljer i några få snabba steg.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66401678f03ee0043345208eb32560f589829226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510319"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps-projekt

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för ditt IoT Edge-program med DevOps-projekt. DevOps Projects förenklar den initiala konfigurationen av en bygg- och versionspipeline i Azure Pipelines.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure DevOps. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).

1. I den vänstra fönsterrutan väljer du **Skapa en resurs** och söker sedan efter **DevOps Projects**.  

1. Välj **Skapa**.

## <a name="create-a-new-application-pipeline"></a>Skapa en ny programpipeline

1. Dina Azure IoT Edge-moduler kan skrivas i [C#](tutorial-csharp-module.md), [Node.js,](tutorial-node-module.md) [Python](tutorial-python-module.md), [C](tutorial-c-module.md) och [Java](tutorial-java-module.md). Välj önskat språk för att starta ett nytt program: **.NET**, **Node.js**, **Python**, **C**eller **Java**. Välj **Nästa** för att fortsätta.

   ![Välj språk för att skapa ett nytt program](./media/how-to-devops-project/select-language.png)

2. Välj **Enkel IoT** som programramverk och välj sedan **Nästa**.

   ![Välj enkelt IoT-ramverk](media/how-to-devops-project/select-iot.png)

3. Välj **IoT Edge** som Azure-tjänst som distribuerar ditt program och välj sedan **Nästa**.

   ![Välj IoT Edge-tjänst](media/how-to-devops-project/select-iot-edge.png)

4. Skapa en ny kostnadsfri Azure DevOps-organisation eller använd en befintlig organisation.

   1. Ange ett namn för projektet.

   2. Välj din Azure DevOps-organisation. Om du inte har en befintlig organisation väljer du **Ytterligare inställningar** för att skapa en ny.

   3. Välj din Azure-prenumeration.

   4. Använd IoT Hub-namnet som genereras av ditt projektnamn eller ange ditt eget.

   5. Acceptera standardplatsen eller välj en nära dig.

   6. Välj **Ytterligare inställningar** för att konfigurera De Azure-resurser som DevOps-projekt skapar för din räkning.

   7. Välj **Klar** om du vill slutföra skapandet av projektet.

   ![Namnge och skapa program](media/how-to-devops-project/select-devops.png)

Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. Välj projektnamnet för att se förloppet. Du kan behöva uppdatera sidan. Ett exempel på IoT Edge-program har konfigurerats i en databas i din Azure DevOps-organisation, en version körs och ditt program distribueras till IoT Edge-enheten. Den här instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och ditt program i Azure.

   ![Visa program i Azure-portalen](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps-projekt har skapat en Git-databas för ditt projekt i Azure Repos. I det här avsnittet kan du visa databasen och göra kodändringar i ditt program.

1. Om du vill navigera till den repo som skapats för projektet väljer du **Databaser på** menyn på projektinstrumentpanelen.  

   ![Visa databas som genereras i Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. Följande steg går igenom med hjälp av webbläsaren för att göra kodändringar. Om du vill klona databasen lokalt i stället väljer du **Klona** längst upp till höger i fönstret. Använd den angivna webbadressen för att klona Git-databasen i Visual Studio-kod eller önskat utvecklingsverktyg.

3. Databasen innehåller redan kod för en modul som heter **FilterModule** baserat på det programspråk som du valde i skapandeprocessen. Öppna **modulen/FilterModule/module.json-filen.**

   ![Öppna module.json-filen i Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Observera att den här filen använder [Azure DevOps-byggvariabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) i versionsparametern. **version** Den här konfigurationen säkerställer att en ny version av modulen skapas varje gång en ny version körs.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

I föregående avsnitt konfigurerade Azure DevOps Projects automatiskt en fullständig CI/CD-pipeline för ditt IoT Edge-program. Nu, utforska och anpassa rörledningen efter behov. Följ följande steg för att bekanta dig med Azure DevOps-bygg- och utgivningspipelines.

1. Om du vill visa byggpipelines i ditt DevOps-projekt väljer du **Skapa pipelines** på menyn på projektinstrumentpanelen. Länken öppnar en webbläsarflik och Azure DevOps-bygg-pipelinen för det nya projektet.

   ![Visa build-pipelines i Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Välj **Redigera**.

    ![Redigera byggpipeline](media/how-to-devops-project/click-edit-button.png)

3. På panelen som öppnas kan du undersöka de aktiviteter som uppstår när byggpipelinen körs. Build-pipelinen utför olika uppgifter, till exempel hämtar källor från Git-databasen, skapar IoT Edge-modulavbildningar, skickar IoT Edge-moduler till ett behållarregister och publicerar utdata som används för distributioner. Mer information om Azure IoT Edge-uppgifter i Azure DevOps finns i [Konfigurera Azure Pipelines för kontinuerlig integrering](how-to-ci-cd.md#configure-continuous-integration).

4. Välj **pipeline-huvudet** högst upp i byggpipelinen för att öppna pipeline-informationen. Ändra namnet på din bygg-pipeline till något mer beskrivande.

   ![Redigera pipelineinformation](./media/how-to-devops-project/edit-build-pipeline.png)

5. Välj **Spara & kö**och välj sedan **Spara**.

6. Välj **Utlösare** på byggpipeline-menyn. DevOps Projects skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

7. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

8. Välj **Historik**. Historikpanelen innehåller en granskningskedja över de senaste ändringarna i byggnaden. Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

9. När du är klar med utforska byggpipelinen navigerar du till motsvarande versionspipeline. Välj **Versioner** under **Pipelines**och välj sedan **Redigera** om du vill visa pipelineinformationen.

    ![Visa utgivningspipeline](media/how-to-devops-project/release-pipeline.png)

10. Under **Artefakter** väljer du **Släpp**. Källan som denna artefakt klockor är resultatet av bygga pipeline du undersökt i föregående steg.

11. Bredvid **ikonen Släpp** väljer du den **kontinuerliga distributionsutlösaren** som ser ut som en blixt. Den här versionspipelinen har aktiverat utlösaren, som kör en distribution varje gång det finns en ny versionsartefakt tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.  

12. På menyn för versionspipelinen väljer du **Uppgifter** och väljer sedan **utvecklingsstadiet** i listrutan. DevOps Projects skapade en versionsfas för dig som skapar en IoT-hubb, skapar en IoT Edge-enhet i den hubben, distribuerar exempelmodulen från byggpipelinen och etablerar en virtuell dator för att köras som din IoT Edge-enhet. Mer information om Azure IoT Edge-uppgifter för CD finns i [Konfigurera Azure Pipelines för kontinuerlig distribution](how-to-ci-cd.md#configure-continuous-deployment).

    ![Visa aktiviteter för kontinuerlig distribution](media/how-to-devops-project/dev-release.png)

13. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

14. Välj namnet på en version om du vill visa mer information om den.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om uppgifter för Azure IoT Edge på Azure DevOps i [kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md)
* Förstå IoT Edge-distributionen i [Förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [Distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
