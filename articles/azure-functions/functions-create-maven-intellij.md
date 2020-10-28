---
title: Skapa en Azure-funktion med Java och IntelliJ
description: Lär dig hur du skapar och publicerar en enkel HTTP-utlöst, Server lös app på Azure med Java och IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 149d0ae99975628239f8b08f3987947a99e01cbb
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893241"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Skapa din första Azure-funktion med Java och IntelliJ

Den här artikeln:
- Så här skapar du ett [Server](https://azure.microsoft.com/overview/serverless-computing/) lös funktions projekt med IntelliJ idé
- Steg för att testa och felsöka funktionen i Integrated Development Environment (IDE) på din egen dator
- Instruktioner för att distribuera-funktions projektet till Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Om du vill utveckla en funktion med Java och IntelliJ installerar du följande program vara:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Ett [Java Development Kit (JDK) för Azure som stöds](/azure/developer/java/fundamentals/java-jdk-long-term-support) för Java 8
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition eller Community Edition installerat
+ [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
+ Senaste [funktionens kärn verktyg](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installation och inloggning

1. Välj **Plugins** (Plugin-program) i IntelliJ IDEA-dialogrutan Settings/Preferences (Inställningar, Ctrl+Alt+S). Leta sedan reda på **Azure Toolkit for IntelliJ** på **Marketplace** och klicka på **Install** (Installera). När installationen är färdig klickar du på **Restart** (Starta om) för att aktivera plugin-programmet. 

    ![Plugin-programmet Azure Toolkit for IntelliJ på Marketplace][marketplace]

2. Logga in på Azure-kontot genom att öppna **Azure Explorer** i sidofältet och sedan klicka på ikonen **Azure Sign In** (Azure-inloggning) i fältet högst upp (eller på IDEA-menyn **Tools/Azure/Azure Sign in** (Verktyg/Azure/Azure-inloggning)).
    ![Kommandot för Azure-inloggning i IntelliJ][intellij-azure-login]

3. I fönstret **Azure Sign In** (Azure-inloggning) väljer du **Device Login** (Enhetsinloggning) och klickar sedan på **Sign in** (Logga in) ( [fler inloggningsalternativ](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Azure-inloggningsfönstret med enhetsinloggning markerat][intellij-azure-popup]

4. Klicka på **Copy&Open** (Kopiera och öppna) i dialogrutan **Azure Device Login** (Azure-enhetsinloggning).

   ![Dialogrutan för Azure-inloggning][intellij-azure-copycode]

5. Klistra in enhetskoden (som kopierades när du klickade på **Copy&Open** (Kopiera och öppna) i det senaste steget) i webbläsaren och klicka sedan på **Next** (Nästa).

   ![Webbläsare med enhetsinloggning][intellij-azure-link-ms-account]

6. I dialogrutan **Select Subscriptions** (Välj prenumerationer) väljer du de prenumerationer du vill använda och klickar sedan på **OK** .

   ![Dialogrutan Select Subscriptions (Välj prenumerationer)][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Skapa ditt lokala projekt

I det här avsnittet använder du Azure Toolkit for IntelliJ för att skapa ett lokalt Azure Functions-projekt. Senare i den här artikeln ska du publicera funktions koden till Azure. 

1. Öppna välkomst dialog rutan IntelliJ, Välj *Skapa nytt projekt* för att öppna guiden Nytt projekt, Välj *Azure Functions* .

    ![Skapa Functions-projekt](media/functions-create-first-java-intellij/create-functions-project.png)

1. Välj *http-utlösare* och klicka sedan på *Nästa* och följ guiden för att gå igenom alla konfigurationer på följande sidor. bekräfta din projekt plats och klicka sedan på *Slutför* . Intellj-idén kommer sedan att öppna det nya projektet.

    ![Skapa funktioner projekt Slutför](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Kör Funktionsapp lokalt

1. Navigera till `src/main/java/org/example/functions/HttpTriggerFunction.java` för att se koden som genereras. Bredvid rad *17* ser du att det finns en grön *körnings* knapp, klickar på den och väljer *Kör "Azure-Function-tent..."* , så ser du att din funktions App körs lokalt med några få loggar.

    ![Lokalt kör funktions projekt](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Funktioner för lokal körnings funktion](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. Du kan prova funktionen genom att öppna den utskrivna slut punkten från webbläsaren, t `http://localhost:7071/api/HttpTrigger-Java?name=Azure` . ex..

    ![Test resultat för lokala körnings funktioner](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Loggen skrivs också ut i din idé. du kan nu stoppa funktionen genom att klicka på *stopp* knappen.

    ![Test logg för lokal körnings funktion](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Felsöka Funktionsapp lokalt

1. Nu ska vi försöka felsöka Funktionsapp lokalt, klicka på knappen *Felsök* i verktygsfältet (om du inte ser det klickar du på *Visa > utseende-> verktygsfältet* för att aktivera verktygsfältet).

    ![Knappen lokal fel söknings funktion](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Klicka på rad *20* i filen om du `src/main/java/org/example/functions/HttpTriggerFunction.java` vill lägga till en Bryt punkt, komma åt slut punkten `http://localhost:7071/api/HttpTrigger-Java?name=Azure` igen. du hittar Bryt punkten. du kan prova fler fel söknings funktioner som *steg* , *titta* , *utvärdering* . Stoppa felsökningssessionen genom att klicka på stopp knappen.

    ![Lokal fel söknings funktion Bryt](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Distribuera Funktionsapp till Azure

1. Högerklicka på projektet i IntelliJ Project Explorer och välj *Azure-> distribuera till Azure Functions*

    ![Distribuera funktioner till Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Om du inte har några Funktionsapp än klickar du på *ingen tillgänglig funktion, klicka för att skapa en ny* .

    ![Distribuera funktioner till Azure Create app](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Skriv in namnet på appens funktion och välj rätt prenumeration/plattform/resurs grupp/App Service plan. du kan också skapa resurs grupp/App Service plan här. Behåll sedan appinställningar oförändrade, klicka på *OK* och vänta några minuter tills den nya funktionen har skapats. När du har *skapat nya Funktionsapp...* förlopps indikatorn försvinner.

    ![Distribuera funktioner till guiden Skapa appar i Azure](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Välj den Function-app som du vill distribuera till. (den nya Function-appen som du nyss skapade väljs automatiskt). Klicka på *Kör* för att distribuera dina funktioner.

    ![Skärm bild som visar dialog rutan Distribuera Azure Functions.](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Distribuera funktioner till Azure-loggen](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Hantera Azure Functions från idé

1. Du kan hantera dina funktioner med *Azure Explorer* i din idé, klicka på *Funktionsapp* så visas alla dina funktioner här.

    ![Visa funktioner i Utforskaren](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Klicka för att välja en av funktionerna och högerklicka och välj *Visa egenskaper* för att öppna informations sidan. 

    ![Visa funktions egenskaper](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Högerklicka på funktionen *HttpTrigger-Java* och välj *trigger-funktion* , så ser du att webbläsaren öppnas med Utlösar-URL: en.

    ![Skärm bild som visar en webbläsare med U R L.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Lägg till fler funktioner i projektet

1. Högerklicka på paketet *org. exempel. Functions* och välj *New-> Azure Function-klass* . 

    ![Lägg till funktioner till projekt posten](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Fyll i klass namnet *HttpTest* och välj *HttpTrigger* i guiden skapa funktions klass. Klicka på *OK* för att skapa. på det här sättet kan du skapa nya funktioner som du vill.

    ![Skärm bild som visar dialog rutan skapa funktions klass.](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Lägga till funktioner i projektets utdata](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Rensar funktioner

1. Ta bort Azure Functions i Azure Explorer
      
      ![Skärm bild som visar ta bort markerade från en snabb meny.](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java Functions-projekt med en HTTP-utlöst funktion, kört den på din lokala dator och distribuerat den till Azure. Nu kan du utöka din funktion efter...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage utgående bindning för kö](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png