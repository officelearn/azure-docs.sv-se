<properties
   pageTitle="Skapa en webhook- eller API Azure-funktion | Microsoft Azure"
   description="Använd Azure-funktioner för att skapa en funktion som startas av ett WebHook- eller API-anrop."
   services="azure-functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/30/2016"
   ms.author="glenga"/>
   

# Skapa en webhook- eller en API-Azure-funktion

Azure Functions är en händelsedriven beräkna-på-begäran-upplevelse med vilken du kan skapa schemalagda eller utlösta kodenheter som implementeras i ett flertal olika programmeringsspråk. Läs mer om Azure Functions i [Översikt över Azure Functions](functions-overview.md).

I det här ämnet visas hur du skapar en ny Node.js-funktion som anropas av en GitHub-webhook. Den nya funktionen skapas baserat på en fördefinierad mall i Azure Functions-portalen. Du kan också titta på en kort video om du vill se hur dessa steg utförs i portalen.

## Titta på videon

Följande videoklipp visar hur du utför de grundläggande stegen i de här självstudierna. 

[AZURE.VIDEO create-a-web-hook-or-api-azure-function]

##Skapa en webhook-utlöst funktion från mallen

En funktionsapp är värd för körningen av dina funktioner i Azure. Innan du kan skapa en funktion måste du ha ett aktivt Azure-konto. Om du inte redan har ett Azure-konto, [finns kostnadsfria konton tillgängliga](https://azure.microsoft.com/free/). 

1. Gå till [Azure Functions-portalen](https://functions.azure.com/signin) och logga in med ditt Azure-konto.

2. Om du har en befintlig funktionsapp som du kan använda, kan du välja den från **Dina funktionsappar** och sedan klicka på **Öppna**. För att skapa en ny funktionsapp, skriver du in ett unikt **namn** för din nya funktionsapp eller accepterar det genererade namnet, väljer **Region** och klickar på **Skapa + Kom igång**. 

3. I din funktionsapp klickar du på **+ Ny funktion** > **GitHub-webhook – Nod** > **Skapa**. Detta skapar en funktion med ett standardnamn som baseras på den angivna mallen. 

    ![Skapa en ny webhook-funktion för GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook.png) 

4. På **Utveckla** kan du se exempelfunktionen express.js i fönstret **Kod**. Den här funktionen tar emot en GitHub-begäran från en ärendekommentar-webhook, loggar ärendetexten och skickar ett svar till webhooken som `New GitHub comment: <Your issue comment text>`.


    ![Skapa en ny webhook-funktion för GitHub](./media/functions-create-a-web-hook-or-api-function/functions-new-webhook-in-portal.png) 

5. Kopiera värdena för **Funktions-URL** och **GitHub-hemlighet**. Du behöver dessa när du skapar webhooken i GitHub. 

6. Rulla ned till **Kör**, notera den fördefinierade JSON-brödtexten i en ärendekommentar i begärandetexten och klicka sedan på **Kör**. 
 
    Du kan alltid testa en ny mallbaserad funktion direkt på fliken **Utveckla** genom att ange förväntad JSON-brödtextdata och klicka på **Kör**-knappen. I det här fallet har mallen en fördefinierad brödtext för en ärendekommentar. 
 
Därefter skapar du den faktiska webhooken i GitHub-databasen.

##Konfigurera webhooken

1. I GitHub går du till en av dina databaser; detta inkluderar alla databaser som du har förgrenat.
 
2. Klicka på **Inställningar** > **Webhooks och tjänster** > **Lägg till webhook**.

    ![Skapa en ny webhook-funktion för GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-2.png)   

3. Klistra in din funktions URL och hemlighet i **Nyttolast-URL** och **Hemlighet**, klicka sedan på **Låt mig välja enskilda händelser**, välj **Ärendekommentar** och klicka på **Lägg till webhook**.

    ![Skapa en ny webhook-funktion för GitHub](./media/functions-create-a-web-hook-or-api-function/functions-create-new-github-webhook-3.png) 

Nu har GitHub-webhooken konfigurerats för att utlösa din funktion när en ny ärendekommentar läggs till.  
Nu är det dags att testa processen.

##Testa funktionen

1. I GitHub-databasen öppnar du fliken **Ärenden** i ett nytt webbläsarfönster, klickar på **Nytt ärende**, skriver in ett namn och klickar sedan på **Skicka nytt ärende**. Du kan även öppna ett befintligt ärende.

2. Skriv en kommentar i ärendet och klicka på **Kommentar**. Nu kan du gå tillbaka till din nya webhook i GitHub och under **Senaste leveranser** visa att en webhook-begäran har skickats och att brödtexten i svaret är `New GitHub comment: <Your issue comment text>`.

3. Tillbaka i Functions-portalen rullar du ned till loggarna och ser att funktionen har utlösts och att värdet `New GitHub comment: <Your issue comment text>` skrivs till direktuppspelningsloggarna.


##Nästa steg

Mer information om Azure Functions finns i dessa ämnen.

+ [Azure Functions, info för utvecklare](functions-reference.md)  
Programmerarreferens för kodningsfunktioner.
+ [Testa Azure Functions](functions-test-a-function.md)  
Beskriver olika verktyg och tekniker för att testa funktioner.
+ [Så här skalar du Azure Functions](functions-scale.md)  
Beskriver tillgängliga serviceplaner för Azure Functions, inklusive den dynamiska serviceplanen, och hur du väljer rätt plan.  


[AZURE.INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]



<!--HONumber=Sep16_HO4-->


