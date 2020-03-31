---
title: Distribuera en webbapp med en mall - Azure Cosmos DB
description: Lär dig hur du distribuerar ett Azure Cosmos DB-konto, Azure App Service Web Apps och ett exempelwebbprogram med hjälp av en Azure Resource Manager-mall.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128367"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Distribuera Azure Cosmos DB- och Azure App Service Web Apps med hjälp av en Azure Resource Manager-mall
Den här självstudien visar hur du använder en Azure Resource Manager-mall för att distribuera och integrera [Microsoft Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/) [Azure App Service-webbapp](https://go.microsoft.com/fwlink/?LinkId=529714) och ett exempelwebbprogram.

Med Azure Resource Manager-mallar kan du enkelt automatisera distributionen och konfigurationen av dina Azure-resurser.  Den här självstudien visar hur du distribuerar ett webbprogram och automatiskt konfigurerar Azure Cosmos DB-kontoanslutningsinformation.

När du har slutfört den här självstudien kan du svara på följande frågor:  

* Hur kan jag använda en Azure Resource Manager-mall för att distribuera och integrera ett Azure Cosmos DB-konto och en webbapp i Azure App Service?
* Hur kan jag använda en Azure Resource Manager-mall för att distribuera och integrera ett Azure Cosmos DB-konto, en webbapp i App Service Web Apps och ett webdeploy-program?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Krav
> [!TIP]
> Även om den här självstudien inte förutsätter tidigare erfarenhet av Azure Resource Manager-mallar eller JSON, om du vill ändra de refererade mallarna eller distributionsalternativen, krävs kunskap om vart och ett av dessa områden.
> 
> 

Innan du följer instruktionerna i den här självstudien kontrollerar du att du har en Azure-prenumeration. Azure är en prenumerationsbaserad plattform.  Mer information om hur du skaffar en prenumeration finns i [Köpalternativ,](https://azure.microsoft.com/pricing/purchase-options/) [Medlemserbjudanden](https://azure.microsoft.com/pricing/member-offers/)eller [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Steg 1: Hämta mallfilerna
Låt oss börja med att ladda ner mallfiler som den här självstudien kräver.

1. Ladda ned [skapa ett Azure Cosmos DB-konto, Webbappar och distribuera en exempelmall](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) för demoprogram till en lokal mapp (till exempel C:\Azure Cosmos DBTemplates). Den här mallen distribuerar ett Azure Cosmos DB-konto, en App Service-webbapp och ett webbprogram.  Det konfigurerar också automatiskt webbprogrammet för att ansluta till Azure Cosmos DB-kontot.
2. Hämta [exempelmallen Skapa ett Azure Cosmos DB-konto och Webbappar](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) till en lokal mapp (till exempel C:\Azure Cosmos DBTemplates). Den här mallen distribuerar ett Azure Cosmos DB-konto, en App Service-webbapp och ändrar webbplatsens programinställningar för att enkelt visa Azure Cosmos DB-anslutningsinformation, men innehåller inte ett webbprogram.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Steg 2: Distribuera Azure Cosmos DB-konto, App Service-webbapp och exempel på demoprogram
Nu ska vi distribuera din första mall.

> [!TIP]
> Mallen verifierar inte att webbappnamnet och Azure Cosmos DB-kontonamnet som anges i följande mall är a) giltiga och b) tillgängliga.  Vi rekommenderar starkt att du verifierar tillgängligheten för de namn som du planerar att ange innan du skickar distributionen.
> 
> 

1. Logga in på [Azure Portal,](https://portal.azure.com)klicka på Ny och sök efter "Malldistribution".
    ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment1.png)
2. Markera distributionsobjektet för mall och klicka på **Skapa** ![skärmbild av distributionsgränssnittet för mall](./media/create-website/TemplateDeployment2.png)
3. Klicka på **Redigera mall,** klistra in innehållet i mallfilen DocDBWebsiteTodo.json och klicka på **Spara**.
   ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment3.png)
4. Klicka på **Redigera parametrar**, ange värden för var och en av de obligatoriska parametrarna och klicka på **OK**.  Parametrarna är följande:
   
   1. SITENAME: Anger apptjänstens webbappnamn och används för att konstruera webbadressen som du använder för att komma åt webbappen (till exempel om du `mydemodocdbwebapp.azurewebsites.net`anger "mydemodocdbwebapp", är webbadressen som du öppnar webbappen med ).
   2. HOSTINGPLANNAME: Anger namnet på App Service hosting plan för att skapa.
   3. PLATS: Anger den Azure-plats där azure cosmos DB- och webbappresurserna kan skapas.
   4. DATABASACCOUNTNAME: Anger namnet på Azure Cosmos DB-kontot som ska skapas.   
      
      ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resursgrupp eller ange ett namn för att skapa en ny resursgrupp och välj en plats för resursgruppen.

    ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska termer**, **Inköp**och klicka sedan på **Skapa** för att påbörja distributionen.  Välj **Fäst på instrumentpanelen** så att den resulterande distributionen är väl synlig på startsidan för Azure-portalen.
   ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas fönstret Resursgrupp.
   ![Skärmbild av resursgruppsfönstret](./media/create-website/TemplateDeployment7.png)  
8. Om du vill använda programmet navigerar du till webbapp-URL:en (i exemplet ovan skulle url:en vara `http://mydemodocdbwebapp.azurewebsites.net`).  Följande webbprogram visas:
   
   ![Exempel på Todo-program](./media/create-website/image2.png)
9. Gå vidare och skapa ett par uppgifter i webbappen och gå sedan tillbaka till fönstret Resursgrupp i Azure-portalen. Klicka på Azure Cosmos DB-kontoresursen i listan Resurser och klicka sedan på **Data Explorer**.
10. Kör standardfrågan "VÄLJ * FRÅN c" och kontrollera resultaten.  Observera att frågan har hämtat JSON-representationen av de todoobjekt som du skapade i steg 7 ovan.  Experimentera gärna med frågor; Försök till exempel att köra SELECT * FROM c WHERE c.isComplete = true to return all todo-objekt som har markerats som slutförda.
11. Utforska gärna Azure Cosmos DB-portalupplevelsen eller ändra exempelprogrammet Todo.  När du är klar ska vi distribuera en annan mall.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Steg 3: Distribuera exempel på dokumentkonto och webbapp
Nu ska vi distribuera din andra mall.  Den här mallen är användbar för att visa hur du kan injicera Azure Cosmos DB-anslutningsinformation, till exempel kontoslutpunkt och huvudnyckel i en webbapp som programinställningar eller som en anpassad anslutningssträng. Du kanske till exempel har ett eget webbprogram som du vill distribuera med ett Azure Cosmos DB-konto och har anslutningsinformationen automatiskt ifylld under distributionen.

> [!TIP]
> Mallen verifierar inte att webbappnamnet och Azure Cosmos DB-kontonamnet nedan är a) giltiga och b) tillgängliga.  Vi rekommenderar starkt att du verifierar tillgängligheten för de namn som du planerar att ange innan du skickar distributionen.
> 
> 

1. Klicka på Nytt i [Azure Portal](https://portal.azure.com)och sök efter "Malldistribution".
    ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment1.png)
2. Markera distributionsobjektet för mall och klicka på **Skapa** ![skärmbild av distributionsgränssnittet för mall](./media/create-website/TemplateDeployment2.png)
3. Klicka på **Redigera mall,** klistra in innehållet i mallfilen DocDBWebSite.json och klicka på **Spara**.
   ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment3.png)
4. Klicka på **Redigera parametrar**, ange värden för var och en av de obligatoriska parametrarna och klicka på **OK**.  Parametrarna är följande:
   
   1. SITENAME: Anger apptjänstens webbappnamn och används för att konstruera webbadressen som du använder för att komma åt webbappen (till exempel om du anger "mydemodocdbwebapp", är webbadressen som du öppnar webbappen för mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Anger namnet på App Service hosting plan för att skapa.
   3. PLATS: Anger den Azure-plats där azure cosmos DB- och webbappresurserna kan skapas.
   4. DATABASACCOUNTNAME: Anger namnet på Azure Cosmos DB-kontot som ska skapas.   
      
      ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resursgrupp eller ange ett namn för att skapa en ny resursgrupp och välj en plats för resursgruppen.

    ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska termer**, **Inköp**och klicka sedan på **Skapa** för att påbörja distributionen.  Välj **Fäst på instrumentpanelen** så att den resulterande distributionen är väl synlig på startsidan för Azure-portalen.
   ![Skärmbild av användargränssnittet för malldistribution](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas fönstret Resursgrupp.
   ![Skärmbild av resursgruppsfönstret](./media/create-website/TemplateDeployment7.png)  
8. Klicka på Web App-resursen i listan Resurser och klicka sedan på Skärmbild av **programinställningar** ![skärmbild av resursgruppen](./media/create-website/TemplateDeployment9.png)  
9. Observera hur det finns programinställningar för Azure Cosmos DB-slutpunkten och var och en av Azure Cosmos DB-huvudnycklarna.

    ![Skärmbild av programinställningar](./media/create-website/TemplateDeployment10.png)  
10. Fortsätt gärna utforska Azure Portal eller följ ett av våra Azure Cosmos [DB-exempel](https://go.microsoft.com/fwlink/?LinkID=402386) för att skapa ditt eget Azure Cosmos DB-program.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Nästa steg
Grattis! Du har distribuerat Azure Cosmos DB, App Service-webbapp och ett exempelwebbprogram med Azure Resource Manager-mallar.

* Om du vill veta mer om Azure Cosmos DB klickar du [här](https://azure.microsoft.com/services/cosmos-db/).
* Om du vill veta mer om Azure App Service Web apps, klicka [här](https://go.microsoft.com/fwlink/?LinkId=325362).
* Om du vill veta mer om Azure Resource Manager-mallar klickar du [här](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Nyheter
* En guide till ändringen från webbplatser till App Service finns i: [Azure App Service och dess inverkan på befintliga Azure-tjänster](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

