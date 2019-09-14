---
title: Använda redigerings-och körnings nycklar – LUIS
titleSuffix: Azure Cognitive Services
description: Första gången du använder Language Understanding (LUIS) behöver du inte skapa någon redigerings nyckel. När du tänker publicera appen måste du använda din runtime-slutpunkt för att skapa och tilldela appen körnings nyckel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: diberry
ms.openlocfilehash: 1fb57a7c6cc694c56667d589eae39442ee9e82ac
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984386"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Använda resurs nycklar för redigering och körning

Redigerings-och körnings resurser ger autentisering till din LUIS-app och förutsägelse slut punkt.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

När du loggar in på LUIS-portalen kan du välja att fortsätta med:

* en kostnads fri [utvärderings nyckel](#trial-key) – som tillhandahåller redigering och några förutsägelse slut punkts frågor.
* en ny Azure LUIS Authoring-resurs – skapa en ny resurs. Detta är inte samma som en förutsägelse slut punkt resurs. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Logga in på LUIS-portalen och börja redigera

1. Logga in på [Luis-portalen](https://www.luis.ai) och godkänn användnings villkoren.
1. Starta LUIS-appen genom att välja vilken typ av LUIS redigerings nyckel som du vill använda: kostnads fri utvärderings nyckel eller ny LUIS redigerings nyckel för Azure. 

    ![Välj en typ av Language Understanding redigering av resurs](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. När du är färdig med din resurs urvals process [skapar du en ny app](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Utvärderings nyckel

Du har fått nyckeln för utvärderings versionen (starter). Den används som autentiseringsnyckel för att fråga efter körningen av förutsägelse slut punkten, upp till 1000 frågor per månad. 

Den visas både på sidan **användar inställningar** och på **hanterings > Azure-resurser** på Luis-portalen. 

När du är redo att publicera din förutsägelse slut punkt skapar du och tilldelar redigerings-och förutsägelse körnings nycklar för att ersätta start nyckel funktionen. 

## <a name="create-resources-in-the-azure-portal"></a>Skapa resurser i Azure Portal

1. Logga in på [Azure Portal](https://azure.microsoft.com/free/). 
1. Välj **+ Skapa en resurs**.
1. Skriv `Language understanding` i sökrutan.
1. Välj **Skapa** för att påbörja skapandeprocessen. 
1. Välj **båda** för att skapa en redigering och en körnings nyckel för förutsägelse slut punkt. 
1. Ange informationen som krävs för att skapa resursen och välj sedan **skapa** för att slutföra processen.

    ![Skapa en språk förståelse resurs](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Name|Syfte|
    |--|--|
    |Resursnamn| Ett anpassat namn som du väljer, används som en del av URL: en för dina redigerings-och förutsägelse slut punkts frågor.|
    |Prenumerationsnamn| den prenumeration som ska faktureras för resursen.|
    |Resource group| Ett namn på en anpassad resurs grupp som du väljer eller skapar. Med resurs grupper kan du gruppera Azure-resurser för åtkomst och hantering i samma region.|
    |Redigerings plats|Den region som är associerad med din modell.|
    |Redigera pris nivå|Pris nivån avgör den högsta transaktionen per sekund och månad.|
    |Körnings plats|Den region som är kopplad till den publicerade slut punkts körningen för förutsägelse.|
    |Pris nivå för körning|Pris nivån avgör den högsta transaktionen per sekund och månad.|

    När båda resurserna har skapats tilldelar du resurserna i LUIS-portalen.

## <a name="create-resources-in-azure-cli"></a>Skapa resurser i Azure CLI

Använd [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att skapa varje resurs individuellt. 

Resurs `kind`:

* Redigerings`LUIS.Authoring`
* Förutsägelse`LUIS` 

1. Logga in på Azure CLI:

    ```console
    az login
    ```

    Då öppnas en webbläsare så att du kan välja rätt konto och tillhandahålla autentisering.

1. Skapa en **Luis Authoring-resurs**, av `LUIS.Authoring`typen, `my-luis-authoring-resource` med namnet i `westus` den _befintliga_ resurs `my-resource-group` gruppen med namnet för regionen. 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Skapa en **resurs**av `LUIS`typen Luis förutsägelse-slutpunkt med namnet `my-luis-prediction-resource` i `westus` den _befintliga_ resurs gruppen med `my-resource-group` namnet för regionen. Ändra `F0` till`S0`om du vill ha ett högre data flöde än den kostnads fria nivån. Läs mer om [pris nivåer och data flöde](luis-boundaries.md#key-limits).

    ```console
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > De här nycklarna används **inte** av Luis-portalen förrän de har tilldelats i Luis-portalen på **Azure-resurserna för hanterings >** .

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Tilldela en Authoring-resurs i LUIS-portalen för alla appar

Du kan tilldela en redigerings resurs för en enskild app eller för alla appar i LUIS. Följande procedur tilldelar alla appar till en enda redigerings resurs.

1. Logga in på [Luis-portalen](https://www.luis.ai).
1. I det övre navigerings fältet längst till höger väljer du ditt användar konto och väljer sedan **Inställningar**.
1. På sidan **användar inställningar** väljer du **Lägg till Authoring-resurs** och väljer sedan en befintlig redigerings resurs. Välj **Spara**. 

## <a name="assign-a-resource-to-an-app"></a>Tilldela en resurs till en app

Du kan tilldela en enskild resurs, redigering eller förutsägelse slut punkt körning till en app med följande procedur.

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj sedan en app från listan **Mina appar** .
1. Gå till sidan för att **hantera > Azure-resurser** .

    ![Välj hanterings > Azure-resurser i LUIS-portalen för att tilldela en resurs till appen.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Välj fliken förutsägelse eller redigering av resurs och välj sedan knappen **Lägg till förutsägelse resurs** eller **Lägg till redigering resurs** . 
1. Välj fälten i formuläret för att hitta rätt resurs och välj sedan **Spara**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Tilldela körnings resurser utan att använda LUIS-portalen

I automatiserings syfte, till exempel en CI/CD-pipeline, kanske du vill automatisera tilldelningen av en LUIS runtime-resurs till en LUIS-app. Du måste utföra följande steg för att göra det:

1. Skaffa en Azure Resource Manager-token från detta [webbplats](https://resources.azure.com/api/token?plaintext=true). Denna token upphör så Använd den omedelbart. Begäran returnerar en Azure Resource Manager-token.

    ![Begär Azure Resource Manager-token och ta emot Azure Resource Manager token](./media/luis-manage-keys/get-arm-token.png)

1. Använd token för att begära LUIS runtime-resurser mellan prenumerationer, från [Get Luis Azure-konton API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)som ditt användar konto har åtkomst till. 

    Det här INLÄGGET API kräver följande inställningar:

    |Huvud|Värde|
    |--|--|
    |`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.| 
    |`Ocp-Apim-Subscription-Key`|Din redigerings nyckel.|

    Den här API: T returnerar en matris av JSON-objekt LUIS prenumerationer, inklusive prenumerations-ID, resursgrupp och resursnamn, returneras som kontonamn. Hitta ett objekt i matrisen med LUIS resursen ska tilldelas LUIS-app. 

1. Tilldela token till LUIS-resursen med den [tilldelar en LUIS azure-konton till ett program](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API. 

    Det här INLÄGGET API kräver följande inställningar:

    |Typ|Inställning|Värde|
    |--|--|--|
    |Huvud|`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.|
    |Huvud|`Ocp-Apim-Subscription-Key`|Din redigerings nyckel.|
    |Huvud|`Content-type`|`application/json`|
    |QueryString|`appid`|LUIS-app-ID. 
    |Innehåll||{”AzureSubscriptionId”: ”ddda2925-af7f-4b05-9ba1-2155c5fe8a8e”<br>”ResourceGroup”: ”resourcegroup-2”,<br>”AccountName”: ”luis-uswest-S0-2”}|

    När detta API lyckas returnerar 201 - skapade status. 

## <a name="unassign-resource"></a>Ta bort resurs

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj sedan en app från listan **Mina appar** .
1. Gå till sidan för att **hantera > Azure-resurser** .
1. Välj fliken förutsägelse eller redigering av resurs och välj sedan knappen **ta bort tilldelning av resurs** för resursen. 

När du tar bort tilldelningen av en resurs tas den inte bort från Azure. Det är bara avlänkas från LUIS. 

## <a name="reset-authoring-key"></a>Återställ redigering nyckel

**För [redigering av resurs migrerade](luis-migration-authoring.md) appar**: om redigerings nyckeln har komprometterats, återställer du nyckeln i Azure Portal på sidan **nycklar** för den redigerings resursen. 

**För appar som inte har migrerats än**: nyckeln återställs på alla dina appar i Luis-portalen. Om du redigerar dina appar via redigerings-API: erna måste du ändra värdet för OCP-APIM-Subscription-Key till den nya nyckeln.

## <a name="regenerate-azure-key"></a>Återskapa Azure-nyckel

Återskapa Azure-nycklarna från Azure Portal på sidan **nycklar** .

## <a name="delete-account"></a>Ta bort konto

Se [datalagring och borttagning av](luis-concept-data-storage.md#accounts) information om vilka data tas bort när du tar bort ditt konto.

## <a name="change-pricing-tier"></a>Ändra prisnivå

1.  I [Azure](https://portal.azure.com), hitta LUIS-prenumerationen. Välj prenumerationen LUIS.
    ![Hitta din LUIS-prenumeration](./media/luis-usage-tiers/find.png)
1.  Välj **pris nivå** för att se tillgängliga pris nivåer. 
    ![Visa prisnivåer](./media/luis-usage-tiers/subscription.png)
1.  Välj pris nivå och välj **Välj** för att spara ändringen. 
    ![Ändra LUIS betalning nivå](./media/luis-usage-tiers/plans.png)
1.  När prissättningsändringen är klar, kontrollerar den nya prisnivån i ett popup-fönster. 
    ![Kontrollera din betalning LUIS-nivå](./media/luis-usage-tiers/updated.png)
1. Kom ihåg att [tilldela den här slutpunktsnyckeln](#assign-a-resource-to-an-app) på den **publicera** sidan och använda det på alla endpoint-frågor. 

## <a name="viewing-azure-resource-metrics"></a>Visa Azures resurs mått

### <a name="viewing-azure-resource-summary-usage"></a>Visa användning av Azure-resurs Sammanfattning
Du kan visa information om LUIS användning i Azure. Den **översikt** visar senaste sammanfattningsinformation inklusive anrop och fel. Om du gör en LUIS-slutpunktsförfrågan, sedan omedelbart titta på den **översiktssidan**, vänta fem minuter för användningen visas.

![Visa sammanfattning av användning](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Anpassa användnings diagram för Azure-resursanvändning
Mått ger en mer detaljerad översikt över data.

![Standard-mått](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera din måttdiagram för lång tid och Måttyp. 

![Anpassade mått](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Totalt antal transaktioner tröskelvärde för avisering
Om du vill veta när du har nått ett visst transaktion tröskelvärde, till exempel 10 000 transaktioner, kan du skapa en avisering. 

![Standard-aviseringar](./media/luis-usage-tiers/alert-default.png)

Lägg till en måttavisering för den **Totalt antal anrop** mått för en viss tidsperiod. Lägg till e-postadresserna för alla personer som ska ta emot aviseringen. Lägg till webhooks för alla system som ska ta emot aviseringen. Du kan också köra en logikapp när aviseringen utlöses. 

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du använder versioner](luis-how-to-manage-versions.md) för att kontrol lera appens livs cykel.
* Förstå begreppen, inklusive [redigering av resurser](luis-concept-keys.md#authoring-key) och [deltagare](luis-concept-keys.md#contributions-from-other-authors) på den resursen.
* Lär dig [hur du skapar](luis-how-to-azure-subscription.md) redigerings-och körnings resurser
* Migrera till den nya [redigerings resursen](luis-migration-authoring.md) 
