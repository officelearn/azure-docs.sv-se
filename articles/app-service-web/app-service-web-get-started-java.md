---
title: "Skapa din första Java-webbapp i Azure på fem minuter | Microsoft Docs"
description: "Distribuera en enkel Java-app och se hur enkelt det är att köra webbappar i App Service."
services: app-service\web
documentationcenter: 
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: cephalin;robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 2673a9c0d91510756a97b2dba3801d2925905c9a
ms.lasthandoff: 04/21/2017


---
# <a name="create-your-first-java-web-app-in-azure-in-five-minutes"></a>Skapa din första Java-webbapp i Azure på fem minuter

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Den här snabbstarten hjälper dig att distribuera din första Java-webbapp via [Azure App Service](../app-service/app-service-value-prop-what-is.md) på bara några minuter. När du har gått igenom kursen har du en enkel Java-baserad webbapp som körs i molnet.

![Bläddra till webbappen](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="before-you-begin"></a>Innan du börjar

Den här självstudiekursen visar hur du använder Eclipse IDE för Java EE-utvecklare för att skapa och distribuera en Java-webbapp till Azure. Om du inte redan har installerat Eclipse kan du hämta det kostnadsfritt från http://www.eclipse.org/.

I den här självstudiekursen används [Azure Toolkit för Eclipse](/azure/azure-toolkit-for-eclipse) för att underlätta publiceringen av Java-webbappar på Azure. Information om hur du installerar detta toolkit finns i [Installera Azure Toolkit för Eclipse](/azure/azure-toolkit-for-eclipse-installation).

> [!NOTE]
>
> Du kan också använda [IntelliJ IDEA](https://www.jetbrains.com/idea/) från JetBrains när du utför stegen i den här självstudiekursen. Ett par steg kanske kan se lite annorlunda ut för den utvecklingsmiljön, men det finns också ett [Azure Toolkit för IntelliJ](/azure/azure-toolkit-for-intellij) som underlättar publiceringen för detta utvecklingsverktyg.
>

Du behöver också en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-dynamic-web-project-in-eclipse"></a>Skapa ett dynamiskt webbprojekt i Eclipse

I Eclipse klickar du på **File** (Arkiv), på **New** (Nytt) och sedan på **Dynamic Web Project** (Dynamiskt webbprojekt).

![Nytt dynamiskt webbprojekt](./media/app-service-web-get-started-java/file-new-dynamic-web-project-menu.png)

När dialogrutan för dynamiskt webbprojekt visas ger du appen namnet **MyFirstJavaOnAzureWebApp** och klickar sedan på **Slutför**.
   
![Dialogrutan för dynamiskt webbprojekt](./media/app-service-web-get-started-java/new-dynamic-web-project-dialog-box.png)

> [!NOTE]
>
> Om du har en lokal körningsmiljö som [Apache Tomcat](https://tomcat.apache.org/) installerad kan du ange det i fältet **Target runtime** (Målkörningsmiljö).
>

När ditt dynamiska webbprojekt har skapats lägger du till en ny JSP-sida genom att expandera projektet i Project Explorer (Projektutforskaren), högerklicka på mappen **WebContent** (Webbinnehåll), klicka på **New** (Nytt) och sedan klicka på **JSP File** (JSP-fil).

![Ny JSP-fil (meny)](./media/app-service-web-get-started-java/new-jsp-file-menu.png)

När dialogrutan New JSP File (Ny JSP-fil) visas ger du filen namnet **index.jsp**, behåller den överordnade mappen som **MyFirstJavaOnAzureWebApp/WebContent** och klickar på **Next** (Nästa).

![Ny JSP-fil (dialogruta)](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-1.png)

På andra sidan i dialogrutan New JSP File (Ny JSP-fil) som visas ger du filen namnet **index.jsp**, behåller den överordnade mappen som **MyFirstJavaOnAzureWebApp/WebContent** och klickar på **Finish** (Slutför).

![Ny JSP-fil (dialogruta)](./media/app-service-web-get-started-java/new-jsp-file-dialog-box-page-2.png)

När din nya sida öppnas i Eclipse ersätter du det befintliga `<body></body>`-avsnittet med följande kod:

```jsp
<body>
<h1><% out.println("Java on Azure!"); %></h1>
</body>
```

Spara ändringarna på sidan.

![Redigera JSP-kod](./media/app-service-web-get-started-java/creating-index-jsp-page.png)

## <a name="publish-your-web-app-to-azure"></a>Publicera din webbapp till Azure

När du distribuerar webbappen till Azure använder du flera funktioner i Azure Toolkit för Eclipse.

Påbörja publiceringen med någon av följande metoder:

* Högerklicka på projektet i Eclipse **Project Explorer** (Projektutforskaren), klicka på **Azure**, och klicka sedan på **Publish as Azure Web App** (Publicera som Azure Web App).

   ![Publicera som Azure Web App (snabbmeny)](./media/app-service-web-get-started-java/publish-as-azure-web-app-context-menu.png)

* Klicka på ikonen **Publish** (Publicera) i verktygsfältet i Eclipse och klicka sedan på **Publish as Azure Web App** (Publicera som Azure Web App).

   ![Publicera som Azure Web App (nedrullningsbar meny)](./media/app-service-web-get-started-java/publish-as-azure-web-app-drop-down-menu.png)

Du uppmanas att logga in på ditt Azure-konto om du inte redan har gjort det. Så här loggar du in:

1. Det finns två olika alternativ för att logga in på kontot. I den här kursen väljer du **Interaktiv**.

   ![Dialogrutan för inloggning i Azure](./media/app-service-web-get-started-java/azure-signin-dialog-box.png)

1. Ange autentiseringsuppgifterna för Azure och klicka sedan på **Logga In**.

   ![Dialogruta i Azure](./media/app-service-web-get-started-java/azure-login-dialog-box.png)

1. Välj dina Azure-prenumerationer och klicka sedan på **Välj**.

   ![Dialogruta i Azure](./media/app-service-web-get-started-java/select-azure-subscriptions-dialog-box.png)

> [!NOTE]
>
> Detaljerade instruktioner för **Interaktiv** och **Automatisk** inloggning finns i artikeln [Azure Sign In Instructions for the Azure Toolkit for Eclipse](https://go.microsoft.com/fwlink/?linkid=846174) (Instruktioner för Azure-inloggning för Azure Toolkit för Eclipse).
>

När du har loggat in på Azure-kontot visas dialogrutan **Deploy Web App** (Distribuera webbapp). Inga apptjänster visas om det är första gången du publicerar en webbapp på Azure. Om så är fallet, eller om du vill skapa en ny apptjänst, blir nästa steg att skapa en ny apptjänst. Om du vill göra det klickar du på **Skapa**.

![Dialogrutan Distribuera webbapp](./media/app-service-web-get-started-java/deploy-web-app-dialog-box.png)

När dialogrutan **Skapa App Service** visas måste du börja med att ange följande information:

* Ett unikt namn för din webbapp, som blir DNS-adressen för din webbapp. **MyJavaWebApp** blir till exempel *myjavawebapp.azurewebsites.net*.

* Vilken webbehållare som ska användas för webbappen, till exempel **Senaste Tomcat 8.5**.

* Din Azure-prenumeration.

   ![Dialogrutan Skapa App Service](./media/app-service-web-get-started-java/create-app-service-dialog-box.png)

Om du inte har några befintliga App Service-planer eller om du vill skapa en ny tjänstplan måste du ange följande information:

* Ett unikt namn för din nya tjänstplan. Det här namnet visas när du publicerar webbappar i framtiden med Azure Toolkit och det visas i [Azure Portal](https://portal.azure.com) när du hanterar ditt konto.

* Den geografiska platsen där din tjänstplan ska skapas.

* Prisnivån för tjänstplanen.

   ![Skapa App Service-plan](./media/app-service-web-get-started-java/create-app-service-plan.png)

Klicka sedan på fliken **Resursgrupp**. Om du inte har några befintliga resursgrupper, eller om du vill skapa en ny resursgrupp, måste du ange ett unikt namn för den nya resursgruppen. Annars väljer du en befintlig resursgrupp på den nedrullningsbara menyn.

![Skapa App Service-plan](./media/app-service-web-get-started-java/create-app-service-resource-group.png)

Klicka slutligen på fliken **JDK**. Flera alternativ visas. Utvecklare kan ange JDK:er från tredje part eller anpassade JDK:er. Men i den här självstudiekursen ska du välja **Standard** och sedan klicka på **Skapa**.

![Skapa App Service-plan](./media/app-service-web-get-started-java/create-app-service-specify-jdk.png)

Azure Toolkit börjar skapa din nya apptjänst och under tiden visas en dialogruta med förloppet.

![Förloppsindikator för skapande av apptjänst](./media/app-service-web-get-started-java/create-app-service-progress-bar.png)

När din nya apptjänst har skapats avslutar du med att distribuera webbappen till roten för din nya webbplats. Om du till exempel har en apptjänst på *wingtiptoys.azurewebsites.net* och inte distribuerar webbappen till roten, distribueras webbappen med namnet **MyFirstJavaOnAzureWebApp** till *wingtiptoys.azurewebsites.net/MyFirstJavaOnAzureWebApp*.

![Distribuera webbapp till rot](./media/app-service-web-get-started-java/deploy-web-app-to-root.png)

När du är klar med alla föregående steg klickar du på **Deploy** (Distribuera) för att publicera din webbapp till Azure.

![Distribuera webbapp till Azure](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

Grattis! Din webbapp har distribuerats till Azure! Nu kan du förhandsgranska webbappen på Azure-webbplatsen:

![Bläddra till webbappen](./media/app-service-web-get-started-java/browse-web-app-1.png)

## <a name="updating-your-web-app"></a>Uppdatera din webbapp

När du har publicerat din webbapp till Azure är det enkelt att uppdatera webbappen. Följande steg visar hur du publicerar ändringar i din webbapp.

Börja med att ändra JSP-exempelkoden (som angavs tidigare) så att titeln ersätts med dagens datum:

```jsp
<%@ page
    language="java"
    contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"
    import="java.text.SimpleDateFormat"
    import="java.util.Date" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<% SimpleDateFormat date = new SimpleDateFormat("yyyy/MM/dd"); %>
<title><% out.println(date.format(new Date())); %></title>
</head>
<body>
<h1><% out.println("Java on Azure!"); %></h1>
</body>
</html>
```

![Uppdatera JSP-kod](./media/app-service-web-get-started-java/updating-index-jsp-page.png)

När du har sparat ändringarna på sidan högerklickar du på projektet i Eclipse **Project Explorer** (Projektutforskaren). Klicka sedan på **Azure** och på **Publish as Azure Web App** (Publicera som Azure Web App).

![Publicera en uppdaterad webbapp](./media/app-service-web-get-started-java/publish-updated-web-app-context-menu.png)

När dialogrutan **Deploy Web App** (Distribuera webbapp) visas din apptjänst. Allt du behöver göra för att uppdatera webbappen är att markera apptjänsten och klicka på **Deploy** (Distribuera) för att publicera dina ändringar.

![Distribuera webbapp till Azure](./media/app-service-web-get-started-java/deploy-web-app-to-azure.png)

> [!NOTE]
>
> Om du distribuerar webbappen till apptjänstens rot måste du kontrollera **Deploy to root** (Distribuera till rot) varje gång du publicerar ändringar.
>

När du har publicerat ändringarna ser du att sidans titel har ändrats till dagens datum i webbläsaren.

![Bläddra till webbappen](./media/app-service-web-get-started-java/browse-web-app-2.png)

## <a name="deleting-your-web-app"></a>Ta bort en webbapp

Du kan ta bort en webbapp via **Azure Explorer**, som är en del av Azure Toolkit. Om **Azure Explorer**-vyn inte är synlig i Eclipse kan du visa den på följande sätt:

1. Klicka på **Window** (Fönster), på **Show View** (Visa vy) och sedan på **Other** (Annat).

   ![Visa vy-menyn](./media/app-service-web-get-started-java/show-azure-explorer-view-1.png)

2. När dialogrutan **Show View** (Visa vy) visas väljer du **Azure Explorer** och klickar på **OK**.

   ![Dialogrutan Visa vy](./media/app-service-web-get-started-java/show-azure-explorer-view-2.png)

För att ta bort webbappen från Azure Explorer måste du expandera noden **Web Apps** (Webbappar) och sedan högerklicka på din webbapp och välja **Delete** (Ta bort).

![Ta bort webbapp](./media/app-service-web-get-started-java/delete-web-app-context-menu.png)

Klicka på **OK** när du ombeds bekräfta att du vill ta bort webbappen.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Toolkits för Java IDE:er finns i följande länkar:

* [Azure Toolkit för Eclipse (den här artikeln)](../azure-toolkit-for-eclipse.md)
  * [Nyheter i Azure Toolkit för Eclipse](../azure-toolkit-for-eclipse-whats-new.md)
  * [Installera Azure Toolkit för Eclipse](../azure-toolkit-for-eclipse-installation.md)
  * [Inloggningsinstruktioner för Azure Toolkit för Eclipse](https://go.microsoft.com/fwlink/?linkid=846174)
* [Azure Toolkit för IntelliJ](../azure-toolkit-for-intellij.md)
  * [Nyheter i Azure Toolkit för IntelliJ](../azure-toolkit-for-intellij-whats-new.md)
  * [Installera Azure Toolkit för IntelliJ](../azure-toolkit-for-intellij-installation.md)
  * [Inloggningsinstruktioner för Azure Toolkit för IntelliJ](https://go.microsoft.com/fwlink/?linkid=846179)

Mer information om hur du använder Azure med Java finns på [Azure Java Developer Center](https://azure.microsoft.com/develop/java/) och i [Java Tools för Visual Studio Team Services](https://java.visualstudio.com/).

