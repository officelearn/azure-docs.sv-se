---
title: Distribuera en webbapp med en mall – Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du distribuerar ett Azure Cosmos DB-konto, Azure App Service Web Apps och ett exempelwebbprogram med en Azure Resource Manager-mall.
services: cosmos-db, app-service\web
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 29a2335a3a4077866b71e4303c240ad8352371ba
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243785"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Distribuera Azure Cosmos DB och Azure App Service Web Apps med en Azure Resource Manager-mall
Den här självstudiekursen visar hur du använder en Azure Resource Manager-mall för att distribuera och integrera [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), en [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) webbapp och ett exempelwebbprogram.

Med Azure Resource Manager-mallar kan automatisera du enkelt distributionen och konfigurationen av dina Azure-resurser.  Den här självstudiekursen visar hur du distribuerar ett webbprogram och automatiskt konfigurera anslutningsinformation för Azure Cosmos DB-konto.

När du har slutfört den här självstudiekursen kommer du att kunna besvara följande frågor:  

* Hur kan jag använda en Azure Resource Manager-mall för att distribuera och integrera ett Azure Cosmos DB-konto och en webbapp i Azure App Service?
* Hur kan jag använda en Azure Resource Manager-mall för att distribuera och integrera ett Azure Cosmos DB-konto, en webbapp i App Service Web Apps och Webdeploy-program?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Förutsättningar
> [!TIP]
> Även om den här självstudien inte förutsätter tidigare erfarenhet av Azure Resource Manager-mallar eller JSON, är om du vill ändra de refererade mallar eller distributionsalternativ, sedan kunskaper om var och en av dessa områden obligatoriskt.
> 
> 

Innan du följer anvisningarna i den här självstudien, kontrollera att du har den en Azure-prenumeration. Azure är en prenumeration-baserad plattform.  Läs mer om hur du skaffar en prenumeration, [köpalternativ](https://azure.microsoft.com/pricing/purchase-options/), [Medlemserbjudanden](https://azure.microsoft.com/pricing/member-offers/), eller [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Steg 1: Hämta mallfilerna
Låt oss börja genom att ladda ned mallfiler som kräver de här självstudierna.

1. Ladda ned den [skapa ett Azure Cosmos DB-konto, Web Apps och distribuera en demo-programexemplet](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) mallen till en lokal mapp (till exempel C:\Azure Cosmos DBTemplates). Den här mallen distribuerar ett Azure Cosmos DB-konto, en App Service webbapp och ett webbprogram.  Den konfigurerar också automatiskt webbprogrammet för att ansluta till Azure Cosmos DB-kontot.
2. Ladda ned den [skapa ett Azure Cosmos DB-konto och Web Apps exempel](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) mallen till en lokal mapp (till exempel C:\Azure Cosmos DBTemplates). Den här mallen distribuerar ett Azure Cosmos DB-konto, en App Service-webbapp och ändrar platsens programinställningar att enkelt surface Azure Cosmos DB-anslutningsinformationen, men innehåller inte ett webbprogram.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Steg 2: Distribuera Azure Cosmos DB-konto, App Service-webbappen och demo-programexemplet
Nu ska vi distribuera din första mall.

> [!TIP]
> Mallen inte att verifiera att namnet på webbappen och Azure Cosmos DB-kontonamn som anges i följande mall är a) giltiga och (b) tillgängliga.  Vi rekommenderar starkt att du kontrollerar tillgängligheten för de som du tänker ange innan du skickar distributionen.
> 
> 

1. Logga in på den [Azure-portalen](https://portal.azure.com), klickar du på nytt och söka efter ”malldistributionen”.
    ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment1.png)
2. Välj objektet som mall för distribution och klickar på **skapa** ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment2.png)
3. Klicka på **redigera mallen**, klistra in innehållet i mallfilen DocDBWebsiteTodo.json och på **spara**.
   ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment3.png)
4. Klicka på **redigera parametrar**, ange värden för var och en av de obligatoriska parametrarna och på **OK**.  Parametrarna är följande:
   
   1. WEBBPLATSNAMN: Anger namnet på App Service webbappen och används för att konstruera den URL som du använder för att komma åt webbappen (till exempel om du anger ”mydemodocdbwebapp” och sedan den URL som du komma åt webbappen är mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Anger namnet på App Service-värdplanen att skapa.
   3. PLATS: Anger den Azure-plats där du kan skapa Azure Cosmos DB och web appresurser.
   4. DATABASEACCOUNTNAME: Anger namnet på Azure Cosmos DB-konto för att skapa.   
      
      ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resursgrupp eller ange ett namn för att göra en ny resursgrupp och välja en plats för resursgruppen.

    ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska villkor**, **köp**, och klicka sedan på **skapa** starta distributionen.  Välj **fäst på instrumentpanelen** så den resulterande distributionen är väl synlig på startsidan Azure portal.
   ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas fönstret Resource group.
   ![Skärmbild av fönstret resource group](./media/create-website/TemplateDeployment7.png)  
8. Om du vill använda programmet går du till webbappens URL (i exemplet ovan är URL: en skulle vara http://mydemodocdbwebapp.azurewebsites.net).  Webbprogrammet följande visas:
   
   ![Exempelprogrammet för att göra](./media/create-website/image2.png)
9. Gå vidare och skapa några uppgifter i webbapp och återgå sedan till fönstret resurs grupp i Azure-portalen. Klicka på resursen för Azure Cosmos DB-konto i listan över resurser och klickar sedan på **Datautforskaren**.
10. Kör standardfrågan ”, Välj * från c” och granska resultatet.  Observera att frågan har hämtat JSON-representation av de objekt du skapade i steg 7 ovan.  Passa på att experimentera med frågor. Exempelvis kan försöka att köra SELECT * FROM c WHERE c.isComplete = true för att returnera alla att göra-objekt som har markerats som slutförd.
11. Passa på att utforska Azure Cosmos DB-portaler eller ändra exempelprogrammet för att göra.  När du är redo kan vi distribuera en annan mall.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Steg 3: Distribuera exemplet för dokument-konto och web app
Nu ska vi distribuera andra mallen.  Den här mallen är användbar för att visa hur du kan mata in Azure Cosmos DB-anslutningsinformationen som slutpunkten-konto och huvudnyckeln i en webbapp som programinställningar eller som en anpassad anslutningssträng. Till exempel kanske har du egna webbprogram som du vill distribuera med ett Azure Cosmos DB-konto och få den information som fylls automatiskt under distributionen.

> [!TIP]
> Mallen inte att verifiera att namnet på webbappen och Azure Cosmos DB-kontonamn som anges nedan är a) giltiga och (b) tillgängliga.  Vi rekommenderar starkt att du kontrollerar tillgängligheten för de som du tänker ange innan du skickar distributionen.
> 
> 

1. I den [Azure-portalen](https://portal.azure.com), klickar du på nytt och söka efter ”malldistributionen”.
    ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment1.png)
2. Välj objektet som mall för distribution och klickar på **skapa** ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment2.png)
3. Klicka på **redigera mallen**, klistra in innehållet i mallfilen DocDBWebSite.json och på **spara**.
   ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment3.png)
4. Klicka på **redigera parametrar**, ange värden för var och en av de obligatoriska parametrarna och på **OK**.  Parametrarna är följande:
   
   1. WEBBPLATSNAMN: Anger namnet på App Service webbappen och används för att konstruera den URL som du använder för att komma åt webbappen (till exempel om du anger ”mydemodocdbwebapp” och sedan den URL som du komma åt webbappen är mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Anger namnet på App Service-värdplanen att skapa.
   3. PLATS: Anger den Azure-plats där du kan skapa Azure Cosmos DB och web appresurser.
   4. DATABASEACCOUNTNAME: Anger namnet på Azure Cosmos DB-konto för att skapa.   
      
      ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resursgrupp eller ange ett namn för att göra en ny resursgrupp och välja en plats för resursgruppen.

    ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska villkor**, **köp**, och klicka sedan på **skapa** starta distributionen.  Välj **fäst på instrumentpanelen** så den resulterande distributionen är väl synlig på startsidan Azure portal.
   ![Skärmbild av malldistributionen UI](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas fönstret Resource group.
   ![Skärmbild av fönstret resource group](./media/create-website/TemplateDeployment7.png)  
8. Klicka på Web App-resurs i listan över resurser och klickar sedan på **programinställningar** ![Skärmbild av resursgruppen.](./media/create-website/TemplateDeployment9.png)  
9. Observera hur programinställningar som är tillgänglig för Azure Cosmos DB-slutpunkt och var och en av Azure Cosmos DB-huvudnycklar.

    ![Skärmbild av programinställningar](./media/create-website/TemplateDeployment10.png)  
10. Passa på att Fortsätt att utforska Azure Portal eller följa en av våra Azure Cosmos DB [exempel](https://go.microsoft.com/fwlink/?LinkID=402386) att skapa din egen Azure Cosmos DB-program.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Nästa steg
Grattis! Du har distribuerat Azure Cosmos DB, App Service webbapp och ett exempelwebbprogram med hjälp av Azure Resource Manager-mallar.

* Om du vill veta mer om Azure Cosmos DB, klickar du på [här](http://azure.com/docdb).
* Om du vill veta mer om Azure App Service Web apps, klickar du på [här](https://go.microsoft.com/fwlink/?LinkId=325362).
* Om du vill veta mer om Azure Resource Manager-mallar, klickar du på [här](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Nyheter
* En guide till övergången från Webbplatser till App Service finns i: [Azure App Service och dess påverkan på befintliga Azure-tjänster](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 

