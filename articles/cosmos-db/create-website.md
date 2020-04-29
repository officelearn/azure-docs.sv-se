---
title: Distribuera en webbapp med en mall – Azure Cosmos DB
description: Lär dig hur du distribuerar ett Azure Cosmos DB-konto, Azure App Service Web Apps och ett exempel webb program med hjälp av en Azure Resource Manager-mall.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: sngun
ms.openlocfilehash: 2306dbe234e171ac613c33458df1990b767637df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128367"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Distribuera Azure Cosmos DB och Azure App Service Web Apps med en Azure Resource Manager mall
Den här självstudien visar hur du använder en Azure Resource Manager mall för att distribuera och integrera [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) webbapp och ett exempel webb program.

Med hjälp av Azure Resource Manager mallar kan du enkelt automatisera distributionen och konfigurationen av dina Azure-resurser.  Den här självstudien visar hur du distribuerar ett webb program och automatiskt konfigurerar Azure Cosmos DB information om konto anslutning.

När du har slutfört den här självstudien kommer du att kunna besvara följande frågor:  

* Hur kan jag använda en Azure Resource Manager mall för att distribuera och integrera ett Azure Cosmos DB konto och en webbapp i Azure App Service?
* Hur kan jag använda en Azure Resource Manager-mall för att distribuera och integrera ett Azure Cosmos DB-konto, en webbapp i App Service Web Apps och ett WebDeploy-program?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>Krav
> [!TIP]
> Även om den här självstudien inte antar tidigare erfarenhet med Azure Resource Manager mallar eller JSON, bör du ändra de refererade mallarna eller distributions alternativen och sedan krävs kunskap om var och en av dessa områden.
> 
> 

Se till att du har en Azure-prenumeration innan du följer anvisningarna i den här självstudien. Azure är en prenumerations-baserad plattform.  Mer information om hur du skaffar en prenumeration finns i [köp alternativ](https://azure.microsoft.com/pricing/purchase-options/), [medlems erbjudanden](https://azure.microsoft.com/pricing/member-offers/)eller [kostnads fri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-download-the-template-files"></a><a id="CreateDB"></a>Steg 1: Ladda ned mallfilerna
Vi börjar med att hämta mallfilerna som krävs av den här självstudien.

1. Hämta en exempel mall för att [skapa ett Azure Cosmos DB konto, Web Apps och distribuera ett demonstrations program](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) till en lokal mapp (till exempel C:\Azure Cosmos DBTemplates). Den här mallen distribuerar ett Azure Cosmos DB-konto, en App Service webbapp och ett webb program.  Den konfigurerar också automatiskt webb programmet så att det ansluter till Azure Cosmos DB kontot.
2. Ladda ned [exempel mal len skapa ett Azure Cosmos DB-konto och Web Apps](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) till en lokal mapp (till exempel C:\Azure Cosmos DBTemplates). Den här mallen distribuerar ett Azure Cosmos DB-konto, en App Service webbapp och ändrar platsens program inställningar för att enkelt placera Azure Cosmos DB anslutnings information, men innehåller inte något webb program.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>Steg 2: Distribuera Azure Cosmos DB-kontot, App Service webbapp och exempel på demo program
Nu ska vi distribuera din första mall.

> [!TIP]
> Mallen verifierar inte att namnet på webbappen och Azure Cosmos DB konto namnet som angavs i följande mall är a) giltigt och b) tillgängligt.  Vi rekommenderar starkt att du kontrollerar tillgängligheten för de namn du planerar att tillhandahålla innan du skickar distributionen.
> 
> 

1. Logga in på [Azure-portalen](https://portal.azure.com), klicka på ny och Sök efter "malldistribution".
    ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment1.png)
2. Välj malldistribution objekt och klicka på **skapa** ![skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment2.png)
3. Klicka på **Redigera mall**, klistra in innehållet i DocDBWebsiteTodo. JSON-mallfilen och klicka på **Spara**.
   ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment3.png)
4. Klicka på **Redigera parametrar**, ange värden för var och en av de obligatoriska parametrarna och klicka på **OK**.  Parametrarna är följande:
   
   1. WEBBPLATS namn: anger App Service webbappens namn och används för att skapa den URL som du använder för att få åtkomst till webbappen (till exempel om du anger "mydemodocdbwebapp" och sedan den URL som du använder för att komma `mydemodocdbwebapp.azurewebsites.net`åt webbappen).
   2. HOSTINGPLANNAME: anger namnet på App Service värd plan som ska skapas.
   3. PLATS: anger den Azure-plats där du vill skapa Azure Cosmos DB-och webb programs resurser.
   4. DATABASEACCOUNTNAME: anger namnet på det Azure Cosmos DB konto som ska skapas.   
      
      ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resurs grupp eller ange ett namn för att skapa en ny resurs grupp och välj en plats för resurs gruppen.

    ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska villkor**, **köp**och klicka sedan på **skapa** för att starta distributionen.  Välj **Fäst på instrument panelen** så att distributionen enkelt kan visas på din Azure Portal start sida.
   ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas fönstret resurs grupp.
   ![Skärm bild av resurs grupps fönstret](./media/create-website/TemplateDeployment7.png)  
8. Om du vill använda programmet navigerar du till webbappens webb adress (i exemplet ovan är URL: `http://mydemodocdbwebapp.azurewebsites.net`en).  Följande webb program kommer att visas:
   
   ![Exempel på att göra programmet](./media/create-website/image2.png)
9. Gå vidare och skapa ett par uppgifter i webbappen och gå sedan tillbaka till rutan resurs grupp i Azure Portal. Klicka på resursen för Azure Cosmos DB konto i listan resurser och klicka sedan på **datautforskaren**.
10. Kör standard frågan "SELECT * FROM c" och granska resultaten.  Observera att frågan har hämtat JSON-representationen av att göra-objekt som du skapade i steg 7 ovan.  Experimentera gärna med frågor. Prova till exempel att köra SELECT * från c där c. isComplete = True för att returnera alla objekt som har marker ATS som slutförda.
11. Lär dig mer om att utforska Azure Cosmos DB Portal upplevelsen eller ändra exempel programmet.  När du är klar ska vi distribuera en annan mall.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>Steg 3: Distribuera ett exempel på ett dokument konto och ett webbapp
Nu ska vi distribuera din andra mall.  Den här mallen är användbar för att visa hur du kan mata in Azure Cosmos DB anslutnings information, till exempel konto slut punkt och huvud nyckel i en webbapp som program inställningar eller som en anpassad anslutnings sträng. Du kanske t. ex. har ett eget webb program som du vill distribuera med ett Azure Cosmos DB-konto och att anslutnings informationen fylls i automatiskt under distributionen.

> [!TIP]
> Mallen verifierar inte att namnet på den webbapp och det Azure Cosmos DB konto namn som anges nedan är a) giltiga och b) tillgängliga.  Vi rekommenderar starkt att du kontrollerar tillgängligheten för de namn du planerar att tillhandahålla innan du skickar distributionen.
> 
> 

1. I [Azure-portalen](https://portal.azure.com)klickar du på ny och söker efter "malldistribution".
    ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment1.png)
2. Välj malldistribution objekt och klicka på **skapa** ![skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment2.png)
3. Klicka på **Redigera mall**, klistra in innehållet i DocDBWebSite. JSON-mallfilen och klicka på **Spara**.
   ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment3.png)
4. Klicka på **Redigera parametrar**, ange värden för var och en av de obligatoriska parametrarna och klicka på **OK**.  Parametrarna är följande:
   
   1. WEBBPLATS namn: anger App Service webbappens namn och används för att skapa den URL som du ska använda för att få åtkomst till webbappen (till exempel om du anger "mydemodocdbwebapp" och sedan den URL som du använder för att komma åt webbappen är mydemodocdbwebapp.azurewebsites.net).
   2. HOSTINGPLANNAME: anger namnet på App Service värd plan som ska skapas.
   3. PLATS: anger den Azure-plats där du vill skapa Azure Cosmos DB-och webb programs resurser.
   4. DATABASEACCOUNTNAME: anger namnet på det Azure Cosmos DB konto som ska skapas.   
      
      ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment4.png)
5. Välj en befintlig resurs grupp eller ange ett namn för att skapa en ny resurs grupp och välj en plats för resurs gruppen.

    ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment5.png)
6. Klicka på **Granska juridiska villkor**, **köp**och klicka sedan på **skapa** för att starta distributionen.  Välj **Fäst på instrument panelen** så att distributionen enkelt kan visas på din Azure Portal start sida.
   ![Skärm bild av användar gränssnittet för mall distribution](./media/create-website/TemplateDeployment6.png)
7. När distributionen är klar öppnas fönstret resurs grupp.
   ![Skärm bild av resurs grupps fönstret](./media/create-website/TemplateDeployment7.png)  
8. Klicka på resursen för webb program i listan resurser och klicka sedan på bild skärm bild för **program inställningar** ![i resurs gruppen](./media/create-website/TemplateDeployment9.png)  
9. Observera att det finns program inställningar för Azure Cosmos DB slut punkten och var och en av de Azure Cosmos DB huvud nycklarna.

    ![Skärm bild av program inställningar](./media/create-website/TemplateDeployment10.png)  
10. Du kan fortsätta att utforska Azure Portal eller följa ett av våra Azure Cosmos DB [exempel](https://go.microsoft.com/fwlink/?LinkID=402386) för att skapa ett eget Azure Cosmos DB-program.

<a name="NextSteps"></a>

## <a name="next-steps"></a>Nästa steg
Grattis! Du har distribuerat Azure Cosmos DB, App Service webbapp och ett exempel webb program med hjälp av Azure Resource Manager mallar.

* Klicka [här](https://azure.microsoft.com/services/cosmos-db/)om du vill veta mer om Azure Cosmos dB.
* Klicka [här](https://go.microsoft.com/fwlink/?LinkId=325362)om du vill veta mer om Azure App Service Web Apps.
* Klicka [här](https://msdn.microsoft.com/library/azure/dn790549.aspx)om du vill veta mer om Azure Resource Manager mallar.

## <a name="whats-changed"></a>Nyheter
* En guide för ändringen från webbplatser till App Service finns i: [Azure App service och dess påverkan på befintliga Azure-tjänster](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Om du vill komma igång med Azure Apptjänst innan du registrerar dig för ett Azure-konto kan du gå till [Prova Apptjänst](https://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i Apptjänst. Inget kreditkort krävs, och du gör inga åtaganden.
> 
> 

