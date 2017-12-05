---
title: Distribuera en webbapp med en mall - Azure Cosmos DB | Microsoft Docs
description: "Lär dig mer om att distribuera ett Azure DB som Cosmos-konto, Azure App Service Web Apps och ett exempelwebbprogram med hjälp av en Azure Resource Manager-mall."
services: cosmos-db, app-service\web
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 087d8786-1155-42c7-924b-0eaba5a8b3e0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 7ceb4bf97c29a18d6879af55615eea46037c51ce
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Distribuera Azure Cosmos DB och Azure App Service Web Apps med en Azure Resource Manager-mall
Den här kursen visar hur du använder en Azure Resource Manager-mall för att distribuera och integrera [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), en [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) webbapp och en exempelwebbapp.

Med Azure Resource Manager-mallar kan automatisera du enkelt distributionen och konfigurationen av Azure-resurser.  Den här kursen visar hur du distribuerar ett webbprogram och konfigurera automatiskt Azure Cosmos DB kontoinformation för anslutningen.

När du har slutfört den här självstudiekursen kommer du att kunna besvara följande frågor:  

* Hur kan jag använda en Azure Resource Manager-mall för att distribuera och integrera Azure DB som Cosmos-konto och en webbapp i Azure App Service?
* Hur kan jag använda en Azure Resource Manager-mall för att distribuera och integrera Azure DB som Cosmos-konto och en webbapp i App Service Web Apps Webdeploy-program?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Krav
> [!TIP]
> När den här kursen inte förutsätter tidigare erfarenhet av Azure Resource Manager-mallar eller JSON, kommer bör du vill ändra det refererade mallar eller distributionsalternativ sedan kunskap om dessa olika områden att krävas.
> 
> 

Se till att du har följande innan du följer anvisningarna i den här självstudiekursen:

* En Azure-prenumeration. Azure är en plattform som baseras på prenumerationer.  Mer information om hur du skaffar en prenumeration finns [köpalternativ](https://azure.microsoft.com/pricing/purchase-options/), [Medlemserbjudanden](https://azure.microsoft.com/pricing/member-offers/), eller [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a id="CreateDB"></a>Steg 1: Hämta mallfilerna
Låt oss börja genom att hämta mallfilerna som vi ska använda i den här kursen.

1. Hämta den [skapa ett Azure DB som Cosmos-konto, Web Apps och distribuerar ett exempel på program demo](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) mallen till en lokal mapp (t.ex. C:\Azure Cosmos DBTemplates). Den här mallen ska distribuera ett Azure DB som Cosmos-konto, en App Service webbapp och ett webbprogram.  Den konfigurerar också automatiskt webbprogram att ansluta till Azure DB som Cosmos-konto.
2. Hämta den [skapa ett Azure DB som Cosmos-konto och Web Apps exempel](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) mallen till en lokal mapp (t.ex. C:\Azure Cosmos DBTemplates). Den här mallen ska distribuera ett Azure DB som Cosmos-konto, en Apptjänst-webbapp och kommer att ändra platsens programinställningar att enkelt ytan Azure Cosmos DB-anslutningsinformationen, men innehåller inte ett webbprogram.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Steg 2: Distribuera Azure DB som Cosmos-konto, App Service web app och demo programmet exempel
Nu ska vi distribuera vårt första mallen.

> [!TIP]
> Mallen kan inte valideras att webbprogrammets namn och Azure DB som Cosmos-kontonamn som anges nedan är ett) giltiga och b) tillgängliga.  Vi rekommenderar att du kontrollerar tillgängligheten för de namn som du planerar att leverera innan distributionen.
> 
> 

1. Logga in på den [Azure Portal](https://portal.azure.com), klickar du på nytt och Sök efter ”malldistribution”.
    ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment1.png)
2. Markera objektet som mall för distribution och klickar på **skapa** ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment2.png)
3. Klicka på **redigera mallen**, klistra in innehållet i filen DocDBWebsiteTodo.json mallen och på **spara**.
   ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment3.png)
4. Klicka på **redigera parametrar**, ange värden för alla obligatoriska parametrar och på **OK**.  Parametrarna är följande:
   
   1. PLATSNAMN: Anger namnet App Service web app och används för att konstruera den URL som du använder för att komma åt webbappen (t.ex. Om du anger ”mydemodocdbwebapp” och sedan Webbadressen som du har behörighet till webbappen är mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Anger namnet på värd programtjänstplanen att skapa.
   3. PLATS: Anger den Azure-plats där du vill skapa Azure Cosmos DB och web app resurser.
   4. DATABASEACCOUNTNAME: Anger namnet på Azure DB som Cosmos-konto för att skapa.   
      
      ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resursgrupp eller ange ett namn för att skapa en ny resursgrupp och välja en plats för resursgruppen.

    ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska villkor**, **inköp**, och klicka sedan på **skapa** starta distributionen.  Välj **fäst på instrumentpanelen** så att den resulterande distributionen är synlig på startsidan Azure portal.
   ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas bladet för resursgruppen.
   ![Skärmbild av bladet för resursgruppen](./media/create-website/TemplateDeployment7.png)  
8. Om du vill använda programmet helt enkelt navigera till webbappens URL (i exemplet ovan URL: en skulle vara http://mydemodocdbwebapp.azurewebsites.net).  Webbprogrammet följande visas:
   
   ![Todo exempelprogrammet](./media/create-website/image2.png)
9. Gå vidare och skapa några uppgifter i webbapp och återgå sedan till bladet för resursgruppen i Azure-portalen. Azure Cosmos DB konto resurs i listan över resurser och klicka sedan på **Frågeutforskaren**.
    ![Skärmbild av sammanfattningen lins med webbprogram som är markerat](./media/create-website/TemplateDeployment8.png)  
10. Kör standardfrågan ”Välj * från c” och granska resultatet.  Observera att frågan har hämtats JSON-representation av todo-objekt som du skapade i steg 7 ovan.  Passa på att experimentera med frågor. exempelvis kör SELECT * från c WHERE c.isComplete = true för att returnera alla todo-objekt som har markerats som slutförd.
    
    ![Skärmbild av Frågeutforskaren och resultat blad som visar resultatet av frågan](./media/create-website/image5.png)
11. Du kan utforska Azure DB som Cosmos-portaler eller ändra Todo exempelprogrammet.  När du är klar kan vi distribuera en annan mall.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Steg 3: Distribuera provet för dokumentet konto och web app
Nu ska vi distribuera våra andra mallen.  Den här mallen är användbar för att visa hur du kan mata in Azure Cosmos DB anslutningsinformation, till exempel kontot slutpunkt och huvudnyckeln i en webbapp som programinställningar eller som en anpassad anslutningssträng. Till exempel har du kanske egna webbprogram som du vill distribuera med ett konto i Azure Cosmos DB och har anslutningsinformationen fylls automatiskt under distributionen.

> [!TIP]
> Mallen kan inte valideras att webbprogrammets namn och Azure DB som Cosmos-kontonamn som anges nedan är ett) giltiga och b) tillgängliga.  Vi rekommenderar att du kontrollerar tillgängligheten för de namn som du planerar att leverera innan distributionen.
> 
> 

1. I den [Azure Portal](https://portal.azure.com), klickar du på nytt och Sök efter ”malldistribution”.
    ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment1.png)
2. Markera objektet som mall för distribution och klickar på **skapa** ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment2.png)
3. Klicka på **redigera mallen**, klistra in innehållet i filen DocDBWebSite.json mallen och på **spara**.
   ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment3.png)
4. Klicka på **redigera parametrar**, ange värden för alla obligatoriska parametrar och på **OK**.  Parametrarna är följande:
   
   1. PLATSNAMN: Anger namnet App Service web app och används för att konstruera den URL som du använder för att komma åt webbappen (t.ex. Om du anger ”mydemodocdbwebapp” och sedan Webbadressen som du har behörighet till webbappen är mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: Anger namnet på värd programtjänstplanen att skapa.
   3. PLATS: Anger den Azure-plats där du vill skapa Azure Cosmos DB och web app resurser.
   4. DATABASEACCOUNTNAME: Anger namnet på Azure DB som Cosmos-konto för att skapa.   
      
      ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resursgrupp eller ange ett namn för att skapa en ny resursgrupp och välja en plats för resursgruppen.

    ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska villkor**, **inköp**, och klicka sedan på **skapa** starta distributionen.  Välj **fäst på instrumentpanelen** så att den resulterande distributionen är synlig på startsidan Azure portal.
   ![Skärmbild av malldistribution UI](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas bladet för resursgruppen.
   ![Skärmbild av bladet för resursgruppen](./media/create-website/TemplateDeployment7.png)  
8. Webbprogram-resurs i listan över resurser och klicka sedan på **programinställningar** ![Skärmbild av resursgruppen.](./media/create-website/TemplateDeployment9.png)  
9. Observera hur programinställningar finns i Azure DB som Cosmos-slutpunkten och var och en av huvudnycklar Azure Cosmos DB.

    ![Skärmbild av programinställningar](./media/create-website/TemplateDeployment10.png)  
10. Passa på att fortsätta utforska Azure-portalen eller följa en av våra Azure Cosmos DB [exempel](http://go.microsoft.com/fwlink/?LinkID=402386) att skapa Azure DB som Cosmos programmet.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Nästa steg
Grattis! Du har distribuerat Azure Cosmos DB App Service webbapp och ett exempelwebbprogram med hjälp av Azure Resource Manager-mallar.

* Om du vill veta mer om Azure Cosmos DB, klickar du på [här](http://azure.com/docdb).
* Mer information om Azure App Service Web apps klickar du på [här](http://go.microsoft.com/fwlink/?LinkId=325362).
* Om du vill veta mer om Azure Resource Manager-mallar, klickar du på [här](https://msdn.microsoft.com/library/azure/dn790549.aspx).

## <a name="whats-changed"></a>Nyheter
* En guide till övergången från Webbplatser till App Service finns i: [Azure App Service och dess påverkan på befintliga Azure-tjänster](http://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](http://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

