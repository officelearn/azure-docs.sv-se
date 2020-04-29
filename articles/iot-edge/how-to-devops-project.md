---
title: CI/CD-pipeline med Azure DevOps Projects-Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en Azure IoT Edge app som du väljer i några enkla steg.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c7598aafea85e91f28233645d3d71bf85bf45425
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82130982"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Skapa en CI/CD-pipeline för IoT Edge med Azure DevOps Projects

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för ditt IoT Edge program med DevOps Projects. DevOps Projects förenklar den initiala konfigurationen av en bygg- och versionspipeline i Azure Pipelines.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure DevOps. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).

1. I den vänstra fönsterrutan väljer du **Skapa en resurs** och söker sedan efter **DevOps Projects**.  

1. Välj **Skapa**.

## <a name="create-a-new-application-pipeline"></a>Skapa en ny program pipeline

1. Azure IoT Edge modul (er) kan skrivas i [C#](tutorial-csharp-module.md), [Node. js](tutorial-node-module.md), [python](tutorial-python-module.md), [C](tutorial-c-module.md) och [Java](tutorial-java-module.md). Välj önskat språk för att starta ett nytt program: **.net**, **Node. js**, **python**, **C**eller **Java**. Fortsätt genom att välja **Nästa**.

   ![Välj språk för att skapa ett nytt program](./media/how-to-devops-project/select-language.png)

2. Välj **enkel IoT** som Application Framework och välj sedan **Nästa**.

   ![Välj enkelt IoT-ramverk](media/how-to-devops-project/select-iot.png)

3. Välj **IoT Edge** som den Azure-tjänst som distribuerar ditt program och välj sedan **Nästa**.

   ![Välj IoT Edge tjänst](media/how-to-devops-project/select-iot-edge.png)

4. Skapa en ny kostnadsfri Azure DevOps-organisation eller använd en befintlig organisation.

   1. Ange ett namn för projektet.

   2. Välj din Azure DevOps-organisation. Om du inte har en befintlig organisation väljer du **ytterligare inställningar** för att skapa en ny.

   3. Välj din Azure-prenumeration.

   4. Använd IoT Hub namn som skapats av ditt projekt namn eller ange ditt eget.

   5. Acceptera standard platsen eller Välj ett nära dig.

   6. Välj **ytterligare inställningar** för att konfigurera de Azure-resurser som DevOps Projects skapar för din räkning.

   7. Slutför skapandet av projektet genom att välja **klart** .

   ![Namn och skapa program](media/how-to-devops-project/select-devops.png)

Efter några minuter visas DevOps Projects-instrumentpanelen i Azure-portalen. Välj ditt projekt namn om du vill se förloppet. Du kan behöva uppdatera sidan. Ett exempel på ett IoT Edge program har kon figurer ATS i en lagrings plats i din Azure DevOps-organisation, en version körs och programmet distribueras till IoT Edges enheten. Den här instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och ditt program i Azure.

   ![Visa program i Azure Portal](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps Projects skapat en git-lagringsplats för ditt projekt i Azure datadatabasers. I det här avsnittet visar du lagrings platsen och gör kod ändringar i programmet.

1. Om du vill navigera till lagrings platsen som skapats för projektet väljer du **Arkiv** i menyn för instrument panelen för projektet.  

   ![Visa lagrings plats som skapats i Azure databaser](./media/how-to-devops-project/view-repositories.png)

2. Följande steg vägleder dig genom att använda webbläsaren för att göra kod ändringar. Om du vill klona lagrings platsen lokalt väljer du **klona** i det övre högra hörnet i fönstret. Använd den tillhandahållna URL: en för att klona git-lagringsplatsen i Visual Studio Code eller det föredragna utvecklingsverktyg.

3. Databasen innehåller redan kod för en modul med namnet **FilterModule** baserat på det programspråk som du valde i skapande processen. Öppna filen **modules/FilterModule/module. JSON** .

   ![Öppna module. JSON-fil i Azure databaser](./media/how-to-devops-project/open-module-json.png)

4. Observera att den här filen använder [Azure DevOps build-variabler](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) i **versions** parametern. Den här konfigurationen säkerställer att en ny version av modulen skapas varje gång en ny version körs.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

I föregående avsnitt har Azure DevOps Projects automatiskt konfigurerat en fullständigt CI/CD-pipeline för ditt IoT Edge-program. Nu ska du utforska och anpassa pipelinen efter behov. Använd följande steg för att bekanta dig med pipeline för Azure DevOps build och release.

1. Om du vill visa Bygg pipelines i DevOps-projektet väljer du **Bygg pipelines** i menyn för instrument panelen för projektet. Länken öppnar en webbläsarflik och Azure DevOps-bygg-pipelinen för det nya projektet.

   ![Visa Bygg pipelines i Azure-pipeline](./media/how-to-devops-project/view-build-pipelines.png)

2. Välj **Redigera**.

    ![Redigera Bygg pipeline](media/how-to-devops-project/click-edit-button.png)

3. I panelen som öppnas kan du undersöka de uppgifter som inträffar när din build-pipeline körs. Bygg pipelinen utför olika uppgifter, till exempel hämtning av källor från git-lagringsplatsen, skapa IoT Edge module-avbildningar, skicka IoT Edge moduler till ett behållar register och publicera utdata som används för distributioner. Mer information om Azure IoT Edge uppgifter i Azure DevOps finns i [Konfigurera Azure-pipelines för kontinuerlig integrering](how-to-ci-cd.md#configure-continuous-integration).

4. Välj **pipeline** -rubriken överst i skapa pipeline för att öppna pipeline-informationen. Ändra namnet på din bygg-pipeline till något mer beskrivande.

   ![Redigera pipeline-information](./media/how-to-devops-project/edit-build-pipeline.png)

5. Välj **spara & kö**och välj sedan **Spara**.

6. Välj **utlösare** från menyn Bygg pipeline. DevOps Projects skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

7. Välj **Kvarhållning**. Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

8. Välj **Historik**. Historik panelen innehåller en gransknings historik för de senaste ändringarna i versionen. Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

9. När du är klar med att utforska build-pipeline navigerar du till motsvarande versions pipeline. Välj **versioner** under **pipelines**och välj sedan **Redigera** för att visa information om pipelinen.

    ![Visa versions pipeline](media/how-to-devops-project/release-pipeline.png)

10. Under **Artefakter** väljer du **Släpp**. Källan som den här artefakten bevakar är resultatet av den build-pipeline som du undersökte i föregående steg.

11. Bredvid **Drop** -ikonen väljer du **utlösaren för kontinuerlig distribution** som ser ut som en blixt. Den här versionen av pipelinen har aktiverat utlösaren, som kör en distribution varje gång det finns en ny versions artefakt tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.  

12. I menyn för din versions pipeline väljer du **aktiviteter** och väljer sedan **dev** -fasen i list rutan. DevOps Projects skapade en versions fas för dig som skapar en IoT-hubb, skapar en IoT Edge-enhet i hubben, distribuerar exempel modulen från build-pipeline och etablerar en virtuell dator som ska köras som din IoT Edge enhet. Mer information om Azure IoT Edge uppgifter för CD finns i [Konfigurera Azure-pipelines för kontinuerlig distribution](how-to-ci-cd.md#configure-continuous-deployment).

    ![Visa kontinuerliga distributions uppgifter](media/how-to-devops-project/dev-release.png)

13. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

14. Välj namnet på en version om du vill visa mer information om det.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om aktiviteterna för Azure IoT Edge på Azure DevOps i [kontinuerlig integrering och kontinuerlig distribution till Azure IoT Edge](how-to-ci-cd.md)
* Förstå IoT Edge distribution i [förstå IoT Edge distributioner för enskilda enheter eller i stor skala](module-deployment-monitoring.md)
* Gå igenom stegen för att skapa, uppdatera eller ta bort en distribution i [distribuera och övervaka IoT Edge moduler i stor skala](how-to-deploy-at-scale.md).
