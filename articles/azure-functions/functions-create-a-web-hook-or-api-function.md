---
title: Skapa en webhook- eller API Azure-funktion | Microsoft Docs
description: "Använd Azure Functions för att skapa en funktion utan server som startas av ett WebHook- eller API-anrop."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 36ef34b8-3729-4940-86d2-cb8e176fcc06
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: glenga
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: 3e12b8c988b8971574352e976ad88e2e47f47660
ms.lasthandoff: 03/16/2017


---
# <a name="create-a-webhook-or-api-azure-function"></a>Skapa en webhook- eller en API-Azure-funktion
Azure Functions är en händelsedriven beräkna-på-begäran-upplevelse som du kan använda för att skapa schemalagda eller utlösta kodenheter som implementeras i ett flertal olika programmeringsspråk. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

I det här ämnet visas hur du skapar en JavaScript-funktion som anropas av en GitHub-webhook. Den nya funktionen skapas baserat på en fördefinierad mall i Azure Functions-portalen. Du kan också titta på en kort video om du vill se hur dessa steg utförs i portalen.

Du kan också använda de allmänna stegen i den här självstudien för att skapa en funktion i C# eller F# istället för JavaScript. 

## <a name="watch-the-video"></a>Titta på videon
Följande videoklipp visar hur du utför de grundläggande stegen i den här självstudiekursen 

>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-a-Web-Hook-or-API-Azure-Function/player]
>
>

## <a name="prerequisites"></a>Krav

För att kunna genomföra den här kursen behöver du följande:

+ Ett aktivt Azure-konto. Om du inte redan har ett konto kan du [registrera dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).  
 Du kan också använda [Prova Functions](https://functions.azure.com/try) för att se klart den här självstudien utan något Azure-konto.
+ Ett GitHub-konto. Du kan [registrera dig för ett kostnadsfritt GitHub-konto](https://github.com/join) om du inte redan har ett. 

## <a name="create-a-webhook-triggered-function-from-the-template"></a>Skapa en webhook-utlöst funktion från mallen
En funktionsapp är värd för körningen av dina funktioner i Azure. 

1. Gå till [Azure Functions-portalen](https://functions.azure.com/signin) och logga in med ditt Azure-konto.

2. Om du har en befintlig funktionsapp som du kan använda, kan du välja den från **Dina funktionsappar** och sedan klicka på **Öppna**. För att skapa en funktionsapp skriver du in ett unikt **namn** för din nya funktionsapp eller godkänner det genererade namnet, väljer **Region** och klickar på **Skapa + Kom igång**. 

3. I din funktionsapp klickar du på **+ Ny funktion** > **GitHub-webhook – JavaScript** > **Skapa**. Då skapas en funktion med ett standardnamn som baseras på den angivna mallen. Du kan också skapa en C#- eller F#-funktion.
   
    ![Skapa en webhook-utlöst GitHub-funktion](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 

4. På **Utveckla** kan du se exempelfunktionen express.js i fönstret **Kod**. Den här funktionen tar emot en GitHub-begäran från en ärendekommentar-webhook, loggar ärendetexten och skickar ett svar till webhooken som `New GitHub comment: <Your issue comment text>`.

    ![Granska funktionskoden](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

1. Kopiera och spara värdena för **Funktions-URL** och **GitHub-hemlighet**. Du kommer att använda dessa värden i nästa avsnitt för att konfigurera webhooken i in GitHub. 

2. Klicka på **Test**, notera den fördefinierade JSON-brödtexten i en ärendekommentar i **begärandetexten** och klicka sedan på **Kör**. 

    ![Testa funktionen för webhook i portalen](./media/functions-create-a-web-hook-or-api-function/functions-test-webhook-in-portal.png)
   
    > [!NOTE]
    > Du kan alltid testa en ny mallbaserad funktion direkt på fliken **Utveckla** genom att ange förväntad JSON-brödtextdata och klicka på **Kör**-knappen. I det här fallet har mallen en fördefinierad brödtext för en ärendekommentar. 

Därefter skapar du den faktiska webhooken i GitHub-databasen.

## <a name="configure-the-webhook"></a>Konfigurera webhooken
1. Navigera till en databas du äger i GitHub. Du kan också använda alla databaser som du har förgrenat.
 
2. Klicka på **Inställningar** > **Webhooks och tjänster** > **Lägg till webhook**.
   
    ![Lägga till en GitHub-webhook](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   

3. Klistra in funktionens URL-adress och hemlighet till **Payload URL** (URL för nyttolast) och **Hemlighet** och välj **application/json** som **Innehållstyp**.

4. Klicka på **Let me select individual events** (Låt mig välja individuella händelser), välj **Ärendekommentar** och klicka på **Add webhook** (Lägg till webhook).
   
    ![Ställa in webhooksadressen och hemligheten](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

Nu har GitHub-webhooken konfigurerats för att utlösa din funktion när en ny ärendekommentar läggs till.  
Nu är det dags att testa processen.

## <a name="test-the-function"></a>Testa funktionen
1. I din GitHub-repo öppnar du fliken **Problem** i ett nytt webbläsarfönster.

2. I det nya fönstret klickar du på **Nytt ärende**, skriver en titel och klickar på **Submit new issue** (Skicka nytt ärende). Du kan även öppna ett befintligt ärende.

2. Skriv en kommentar i ärendet och klicka på **Kommentar**. 

3. I det andra GitHub-fönstret klickar du på **Redigera** bredvid din nya webhook, bläddra ned till **Senaste leveranser** och verifiera att en webhook-begäran har skickats och att brödtexten i svaret är `New GitHub comment: <Your issue comment text>`.

3. Tillbaka i Functions-portalen rullar du ned till loggarna och ser att funktionen har utlösts och att värdet `New GitHub comment: <Your issue comment text>` skrivs till direktuppspelningsloggarna.

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Functions finns i dessa ämnen.

* [Azure Functions, info för utvecklare](functions-reference.md)  
  Programmerarreferens för kodningsfunktioner.
* [Testa Azure Functions](functions-test-a-function.md)  
  Beskriver olika verktyg och tekniker för att testa funktioner.
* [Så här skalar du Azure Functions](functions-scale.md)  
  Beskriver tillgängliga serviceplaner för Azure Functions, inklusive värdplanen för förbrukning, och hur du väljer rätt plan.  

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]


