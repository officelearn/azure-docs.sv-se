---
title: CI/CD-pipeline med Azure DevOps-projekt – Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en Azure IoT Edge-app väljer med några enkla steg.
author: shizn
manager: ''
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 02977032c5975de4098600ddbebccfcbb9b0fafd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595311"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps-projekt (förhandsgranskning)

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för din IoT Edge-program med DevOps-projekt. DevOps Projects förenklar den initiala konfigurationen av en bygg- och versionspipeline i Azure Pipelines.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure DevOps. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).

1. I den vänstra fönsterrutan väljer du **Skapa en resurs** och söker sedan efter **DevOps Projects**.  

1.  Välj **Skapa**.

## <a name="create-a-new-application-pipeline"></a>Skapa en ny pipeline för program 

1. Azure IoT Edge-moduler kan skrivas [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) och [Java](tutorial-java-module.md). Välj det språk du föredrar att starta ett nytt program: **.NET**, **Node.js**, **Python**, **C**, eller **Java**. Välj **Nästa** för att fortsätta.

   ![Välj språk för att skapa ett nytt program](./media/how-to-devops-project/select-language.png)

2. Välj **enkla IoT (förhandsversion)** som programramverk och välj sedan **nästa**.

   ![Välj enkla IoT-ramverk](media/how-to-devops-project/select-iot.png)

3. Välj **IoT Edge** som Azure-tjänst som distribuerar programmet och välj sedan **nästa**.

   ![Välj IoT Edge-tjänst](media/how-to-devops-project/select-iot-edge.png)

4. Skapa en ny kostnadsfri Azure DevOps-organisation eller använd en befintlig organisation.

   1. Ange ett namn för ditt projekt. 

   2. Välj din Azure DevOps-organisation. Om du inte har en befintlig organisation kan välja **ytterligare inställningar** att skapa en ny. 

   3. Välj din Azure-prenumeration.

   4. Använd IoT Hub-namn som genererats av ditt projektnamn eller ange dina egna.

   5. Välj **ytterligare inställningar** att konfigurera Azure-resurser som DevOps-projekt skapar åt dig.

   6. Välj **klar** skapa projektet. 

   ![Namnge och skapa program](media/how-to-devops-project/select-devops.png)

Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. Välj ditt projektnamn att se förloppet. Du kan behöva uppdatera sidan. Ett exempel på IoT Edge-programmet har ställts in på en lagringsplats i din organisation med Azure DevOps, en version körs och programmet distribueras till IoT Edge-enhet. Den här instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och ditt program i Azure.

   ![Visa programmet i DevOps-portalen](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps-projekt skapas en Git-lagringsplats för ditt projekt i Azure-databaser. I det här avsnittet ska du visa databasen och gör ändringar i koden i programmet.

1. Gå till den lagringsplats som skapats för ditt projekt, välja **databaser** på menyn på instrumentpanelen för projektet.  

   ![Visa databasen som genereras i Azure-lagringsplatser](./media/how-to-devops-project/view-repositories.png)

2. Följande steg beskriver göra kodändringar med hjälp av webbläsaren. Om du vill klona lagringsplatsen lokalt i stället väljer **klona** längst upp höger i fönstret. Använd den tillhandahållna URL: en för att klona Git-lagringsplats i Visual Studio Code eller din önskade utvecklingsverktyg. 

3. Databasen innehåller redan kod för en modul som heter **SampleModule** baserat på programmets språk du valde under skapandeprocessen. Öppna den **modules/SampleModule/module.json** fil.

   ![Öppna module.json fil i Azure-lagringsplatser](./media/how-to-devops-project/open-module-json.png)

4. Välj **redigera**, och sedan ändrar till `"version"` under den `"tag"`. Du kan till exempel uppdatera den till `"version": "${BUILD_BUILDID}"` att använda [Azure DevOps Skapa variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) som en del av din Azure IoT Edge-modulen bildtagg.

   ![Redigera version för att godkänna build variabler](media/how-to-devops-project/update-module-json.png)

5. Välj **Checka in** och spara sedan ändringarna.

6. Gå tillbaka till instrumentpanelen DevOps-projekt i Azure portal i webbläsaren. Du bör nu se att en version håller på att skapas. De ändringar du utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

    ![Visa status för pågående](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

I föregående avsnitt konfigureras automatiskt en fullständig CI/CD-pipeline för din IoT Edge-program i Azure DevOps-projekt. Sedan testat du att skapa pipelinen genom att genomföra ändringarna till en av filerna. Nu kan utforska och anpassa pipelinen efter behov. Bekanta dig med Azure DevOps-bygg- och versionspipelines genom att utföra följande steg.

1. Om du vill visa de skapandet av pipelines i din DevOps-projekt, Välj **skapa Pipelines** på menyn på instrumentpanelen för projektet. Länken öppnar en webbläsarflik och Azure DevOps-bygg-pipelinen för det nya projektet.

   ![Visa skapa pipeliner i Azure-Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Välj **Redigera**.

    ![Redigera build pipeline](media/how-to-devops-project/click-edit-button.png)

3. Du kan undersöka de aktiviteter som inträffar när build pipelinekörningar panelen som öppnas. Build-pipelinen utför olika uppgifter, till exempel hämtar källor från Git-lagringsplats, att skapa IoT Edge modul-avbildningar, push-överföra IoT Edge-moduler till ett behållarregister och publicering matar du ut som används för distributioner. Läs mer om Azure IoT Edge-uppgifter i Azure DevOps i [konfigurera Azure Pipelines för kontinuerlig integrering](how-to-ci-cd.md#configure-continuous-integration).

4. Välj den **Pipeline** rubrik överst i build-pipelinen för att öppna pipeline-information. Ändra namnet på din build-pipeline till något mer beskrivande.

   ![Redigera pipeline-information](./media/how-to-devops-project/edit-build-pipeline.png)

5. Välj **spara och köa**, och välj sedan **spara**.

6. I menyn för build-pipelinen väljer **utlösare** på menyn. DevOps Projects skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

7. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

8. Välj **historik**. Panelen händelser innehåller en översikt över ändringar i bygget. Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

9. När du är klar utforska build-pipeline, navigera till motsvarande releasepipeline. Välj **versioner** under **Pipelines**och välj sedan **redigera** att visa information om pipeline.

    ![Visa releasepipeline](media/how-to-devops-project/release-pipeline.png)

10. Under **Artefakter** väljer du **Släpp**. Källan som den här artefakten bevakar är utdata från build pipelinen du undersöks i föregående steg. 

11. Bredvid den **släppa** ikonen och därefter den **utlösare av kontinuerlig distribution** som ser ut som en blixt. Den här releasepipeline har aktiverat utlösare, som kör en distribution varje gång en ny byggesartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.  

12. I menyn för din releasepipeline väljer **uppgifter** Välj sedan den **dev** steg i listrutan. DevOps-projekt skapas en viktig del som skapar en IoT-hubb, skapar en IoT Edge-enhet i den hubben, distribuerar exempelmodulen från build-pipeline och etablerar en virtuell dator ska köras som IoT Edge-enhet. Läs mer om Azure IoT Edge-uppgifter för CD i [konfigurera Azure Pipelines för kontinuerlig distribution](how-to-ci-cd.md#configure-continuous-deployment).

    ![Visa kontinuerlig distribution aktiviteter](media/how-to-devops-project/dev-release.png)

13. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

14. Välj namnet på en version som du vill visa mer information om den.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg
* Läs om uppgifterna för Azure IoT Edge på Azure DevOps i [kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md)
* Förstå IoT Edge-distribution i [förstå IoT Edge-distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge-moduler i stor skala](how-to-deploy-monitor.md).
