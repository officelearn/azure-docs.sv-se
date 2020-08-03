---
title: Använda redigerings-och körnings nycklar – LUIS
description: Första gången du använder Language Understanding (LUIS) behöver du inte skapa någon redigerings nyckel. När du tänker publicera appen måste du använda din runtime-slutpunkt för att skapa och tilldela appen körnings nyckel.
services: cognitive-services
ms.topic: how-to
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6bd8cc807a393d6c8027f5990b9897d93f2b78d2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496907"
---
# <a name="create-luis-resources"></a>Skapa LUIS-resurser

Skriv-och körnings resurser för förutsägelse ger autentisering till din LUIS-app och förutsägelse slut punkt.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS-resurser

LUIS tillåter tre typer av Azure-resurser och en icke-Azure-resurs:

|Nyckel|Syfte|Kognitiv tjänst`kind`|Kognitiv tjänst`type`|
|--|--|--|--|
|Redigerings nyckel|Få åtkomst till och hantera data för program med redigering, utbildning, publicering och testning. Skapa en LUIS redigerings nyckel om du tänker program mässigt redigera LUIS-appar.<br><br>Syftet med `LUIS.Authoring` nyckeln är att du ska kunna:<br>* hantera Language Understanding appar och modeller program mässigt, inklusive utbildning och publicering<br> * kontrol lera behörigheterna till redigerings resursen genom att tilldela användare [rollen deltagare](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|Fråga förutsägelse nyckel| Slut punkts begär Anden för fråga. Skapa en LUIS-förutsägelse innan klient programmet begär förutsägelser utöver de 1 000-begäranden som tillhandahålls av start resursen. |`LUIS`|`Cognitive Services`|
|[Resurs nyckel för multi-service för kognitiva tjänster](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Slut punkts begär Anden för förfrågan som delas med LUIS och andra Cognitive Services som stöds.|`CognitiveServices`|`Cognitive Services`|
|Starter|Kostnads fri redigering (utan rollbaserad åtkomst kontroll) via LUIS-portalen eller API: er (inklusive SDK: er), kostnads fria 1 000 förutsägelse slut punkter per månad via en webbläsare, API eller SDK|-|Inte en Azure-resurs|

När processen för att skapa Azure-resurser är färdig [tilldelar du nyckeln](#assign-a-resource-to-an-app) till appen i Luis-portalen.

Det är viktigt att du skapar LUIS-appar i [regioner](luis-reference-regions.md#publishing-regions) där du vill publicera och fråga.

## <a name="resource-ownership"></a>Resurs ägarskap

En Azure-resurs, till exempel en LUIS, ägs av prenumerationen som innehåller resursen.

Om du vill överföra ägarskapet för en resurs kan du antingen:
* Överför [ägarskapet](../../cost-management-billing/manage/billing-subscription-transfer.md) för din prenumeration
* Exportera LUIS-appen som en fil och importera sedan appen på en annan prenumeration. Export är tillgängligt på sidan **Mina appar** i Luis-portalen.


## <a name="resource-limits"></a>Resursbegränsningar

### <a name="authoring-key-creation-limits"></a>Redigerings gränser för att skapa nycklar

Du kan skapa upp till 10 redigerings nycklar per region per prenumeration.

Se [nyckel gränser](luis-limits.md#key-limits) och [Azure-regioner](luis-reference-regions.md).

Publicerings regioner skiljer sig från redigerings områden. Se till att du skapar en app i den redigerings region som motsvarar den publicerings region som du vill att ditt klient program ska finnas i.

### <a name="key-usage-limit-errors"></a>Gräns fel för nyckel användning

Användnings gränser baseras på pris nivån.

Om du överskrider kvoten för transaktioner per sekund (TPS) får du ett HTTP 429-fel. Om du överskrider kvoten för transaktioner per månad (TPM) får du ett HTTP 403-fel.


### <a name="reset-authoring-key"></a>Återställ redigerings nyckel

För [redigering av resurs migrerade](luis-migration-authoring.md) appar: om redigerings nyckeln har komprometterats, återställer du nyckeln i Azure Portal på sidan **nycklar** för den redigerings resursen.

För appar som inte har migrerats än: nyckeln återställs på alla dina appar i LUIS-portalen. Om du redigerar dina appar via redigerings-API: erna måste du ändra värdet för OCP-APIM-Subscription-Key till den nya nyckeln.

### <a name="regenerate-azure-key"></a>Återskapa Azure-nyckel

Återskapa Azure-nycklarna från Azure Portal på sidan **nycklar** .


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Ägarskap för appar, åtkomst och säkerhet

En app definieras av sina Azure-resurser, vilket bestäms av ägarens prenumeration.

Du kan flytta LUIS-appen. Använd följande dokumentations resurser i Azure Portal eller Azure CLI:

* [Flytta appen mellan LUIS Authoring-resurser](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Flytta resurs till ny resurs grupp eller prenumeration](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Flytta en resurs inom samma prenumeration eller mellan prenumerationer](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Bidrag från andra författare

För [redigering av resurs migrerade](luis-migration-authoring.md) appar: _deltagare_ hanteras i Azure Portal för redigerings resursen med hjälp av **åtkomst kontroll (IAM)** sidan. Lär dig [hur du lägger till en användare](luis-how-to-collaborate.md)med hjälp av medarbetares e-postadress och _deltagar_ rollen.

För appar som inte har migrerats än: alla _medarbetare_ hanteras i Luis-portalen på sidan för att **Hantera > samarbets partners** .

### <a name="query-prediction-access-for-private-and-public-apps"></a>Fråga förutsägelse åtkomst för privata och offentliga appar

För en **privat** app är åtkomsten för fråga förutsägelse körning tillgänglig för ägare och deltagare. För en **offentlig** app är körnings åtkomst tillgänglig för alla som har sin egen Azure [-tjänst](../cognitive-services-apis-create-account.md) eller [Luis](#create-resources-in-the-azure-portal) runtime-resurs och har den offentliga appens ID.

För närvarande finns det inte någon katalog med offentliga appar.

### <a name="authoring-permissions-and-access"></a>Redigera behörigheter och åtkomst
Åtkomst till appen från [Luis](luis-reference-regions.md#luis-website) -portalen eller redigerings- [API: erna](https://go.microsoft.com/fwlink/?linkid=2092087) styrs av Azure Authoring-resursen.

Ägaren och alla deltagare har åtkomst för att redigera appen.

|Redigerings åtkomst inkluderar|Kommentarer|
|--|--|
|Lägga till eller ta bort slut punkts nycklar||
|Exporterar version||
|Exportera slut punkts loggar||
|Importerar version||
|Gör appen offentlig|När en app är offentlig kan alla med en redigerings-eller slut punkts nyckel fråga appen.|
|Ändra modell|
|Publicera|
|Granska slut punkts yttranden för [aktiv inlärning](luis-how-to-review-endpoint-utterances.md)|
|Träna|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Åtkomst till förutsägelse slut punkts körning

Åtkomst för att fråga förutsägelse slut punkten styrs av en inställning på sidan **program information** i avsnittet **Hantera** .

|[Privat slut punkt](#runtime-security-for-private-apps)|[Offentlig slutpunkt](#runtime-security-for-public-apps)|
|:--|:--|
|Tillgängligt för ägare och deltagare|Tillgängligt för ägare, deltagare och någon annan som känner till app-ID|

Du kan styra vem som ska se din LUIS körnings nyckel genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot-och LUIS redan skyddad. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API för Server sidan (till exempel en Azure- [funktion](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När API: et för Server sidan anropas och autentiseras och auktoriseringen verifieras, skicka anropet till LUIS. Även om den här strategin inte hindrar människan-in-the-middle-attacker, obfuscates din nyckel-och slut punkts-URL från dina användare, kan du spåra åtkomst och du kan lägga till slut punkts svars loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Körnings säkerhet för privata appar

En privat Apps körnings miljö är bara tillgänglig för följande:

|Nyckel och användare|Förklaring|
|--|--|
|Ägarens redigerings nyckel| Upp till 1000 slut punkts träffar|
|Redigerings nycklar för medarbetare/deltagare| Upp till 1000 slut punkts träffar|
|Alla nycklar som tilldelats LUIS av en författare eller medarbetare/deltagare|Baserat på nyckel användnings nivå|

### <a name="runtime-security-for-public-apps"></a>Körnings säkerhet för offentliga appar

När en app har kon figurer ATS som offentlig kan _alla_ giltiga Luis redigerings nycklar eller Luis-slutpunkt-nyckeln fråga din app, förutsatt att nyckeln inte har använt hela slut punkts kvoten.

En användare som inte är ägare eller deltagare, kan bara komma åt en offentlig Apps körnings miljö om det har fått app-ID. LUIS har inte någon offentlig _marknad_ eller annat sätt att söka efter en offentlig app.

En offentlig app publiceras i alla regioner så att en användare med en domänbaserad LUIS resurs nyckel kan komma åt appen i den region som är kopplad till resurs nyckeln.


### <a name="securing-the-query-prediction-endpoint"></a>Säkra slut punkten för fråga förutsägelse

Du kan styra vem som kan se slut punkts nyckeln för LUIS förutsägelse körning genom att anropa den i en server-till-Server-miljö. Om du använder LUIS från en robot är anslutningen mellan bot-och LUIS redan skyddad. Om du anropar LUIS-slutpunkten direkt bör du skapa ett API för Server sidan (till exempel en Azure- [funktion](https://azure.microsoft.com/services/functions/)) med kontrollerad åtkomst (till exempel [AAD](https://azure.microsoft.com/services/active-directory/)). När API: et för Server sidan anropas och autentisering och auktorisering verifieras, skicka anropet till LUIS. Även om den här strategin inte hindrar människan-in-the-middle-attacker, obfuscates din slut punkt från dina användare, så att du kan spåra åtkomst och du kan lägga till slut punkts svars loggning (till exempel [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Logga in på LUIS-portalen och börja redigera

1. Logga in på [Luis-portalen](https://www.luis.ai) och godkänn användnings villkoren.
1. Starta LUIS-appen genom att välja din Azure LUIS Authoring-nyckel.

   ![Välj en typ av Language Understanding redigering av resurs](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. När du är färdig med din resurs urvals process [skapar du en ny app](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Skapa resurser i Azure CLI

Använd [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att skapa varje resurs individuellt.

Resurs `kind` :

* Redigerings`LUIS.Authoring`
* Förutsägelse`LUIS`

1. Logga in på Azure CLI:

    ```azurecli
    az login
    ```

    Då öppnas en webbläsare så att du kan välja rätt konto och tillhandahålla autentisering.

1. Skapa en **Luis Authoring-resurs**, av typen `LUIS.Authoring` , med namnet `my-luis-authoring-resource` i den _befintliga_ resurs gruppen med namnet `my-resource-group` för `westus` regionen.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Skapa en **resurs av typen Luis förutsägelse-slutpunkt**med `LUIS` namnet `my-luis-prediction-resource` i den _befintliga_ resurs gruppen med namnet `my-resource-group` för `westus` regionen. Ändra till om du vill ha ett högre data flöde än den kostnads fria nivån `F0` `S0` . Läs mer om [pris nivåer och data flöde](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > De här nycklarna används **inte** av Luis-portalen förrän de har tilldelats i Luis-portalen på **Azure-resurserna för hanterings >**.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Tilldela resurs i LUIS-portalen

Du kan tilldela en redigerings resurs för en enskild app eller för alla appar i LUIS. Följande procedur tilldelar alla appar till en enda redigerings resurs.

1. Logga in på [Luis-portalen](https://www.luis.ai).
1. I det övre navigerings fältet längst till höger väljer du ditt användar konto och väljer sedan **Inställningar**.
1. På sidan **användar inställningar** väljer du **Lägg till Authoring-resurs** och väljer sedan en befintlig redigerings resurs. Välj **Spara**.

## <a name="assign-a-resource-to-an-app"></a>Tilldela en resurs till en app

Du kan tilldela en till en app med följande procedur.

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj sedan en app från listan **Mina appar** .
1. Gå till sidan för att **hantera > Azure-resurser** .

    ![Välj hanterings > Azure-resurser i LUIS-portalen för att tilldela en resurs till appen.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Välj fliken förutsägelse eller redigering av resurs och välj sedan knappen **Lägg till förutsägelse resurs** eller **Lägg till redigering resurs** .
1. Välj fälten i formuläret för att hitta rätt resurs och välj sedan **Spara**.

### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Tilldela körnings resurs för körning av fråga utan att använda LUIS-portalen

I automatiserings syfte, till exempel en CI/CD-pipeline, kanske du vill automatisera tilldelningen av en LUIS runtime-resurs till en LUIS-app. Du måste utföra följande steg för att göra det:

1. Hämta en Azure Resource Manager token från den här [webbplatsen](https://resources.azure.com/api/token?plaintext=true). Denna token upphör att gälla, så Använd den omedelbart. Begäran returnerar en Azure Resource Manager-token.

    ![Begär Azure Resource Manager-token och ta emot Azure Resource Manager token](./media/luis-manage-keys/get-arm-token.png)

1. Använd token för att begära LUIS runtime-resurser mellan prenumerationer, från [Get Luis Azure-konton API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)som ditt användar konto har åtkomst till.

    Detta POST-API kräver följande inställningar:

    |Sidhuvud|Värde|
    |--|--|
    |`Authorization`|Värdet för `Authorization` är `Bearer {token}` . Observera att token-värdet måste föregås av ordet `Bearer` och ett blank steg.|
    |`Ocp-Apim-Subscription-Key`|Din redigerings nyckel.|

    Detta API returnerar en matris med JSON-objekt för dina LUIS-prenumerationer, inklusive prenumerations-ID, resurs grupp och resurs namn, returnerat som konto namn. Hitta det objekt i matrisen som är den LUIS-resurs som ska tilldelas LUIS-appen.

1. Tilldela token till LUIS-resursen med [tilldela ett Luis Azure-konto till ett program](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) -API.

    Detta POST-API kräver följande inställningar:

    |Typ|Inställningen|Värde|
    |--|--|--|
    |Sidhuvud|`Authorization`|Värdet för `Authorization` är `Bearer {token}` . Observera att token-värdet måste föregås av ordet `Bearer` och ett blank steg.|
    |Sidhuvud|`Ocp-Apim-Subscription-Key`|Din redigerings nyckel.|
    |Sidhuvud|`Content-type`|`application/json`|
    |Mängden|`appid`|LUIS-app-ID.
    |Brödtext||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "ResourceGroup-2",<br>"AccountName": "Luis-USWest-S0-2"}|

    När det här API: et lyckas returneras statusen 201-skapat.

## <a name="unassign-resource"></a>Ta bort tilldelning av resurs

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj sedan en app från listan **Mina appar** .
1. Gå till sidan för att **hantera > Azure-resurser** .
1. Välj fliken förutsägelse eller redigering av resurs och välj sedan knappen **ta bort tilldelning av resurs** för resursen.

När du tar bort tilldelningen av en resurs tas den inte bort från Azure. Den länkas bara från LUIS.


## <a name="delete-account"></a>Ta bort konto

Se [data lagring och borttagning](luis-concept-data-storage.md#accounts) för information om vilka data som tas bort när du tar bort ditt konto.

## <a name="change-pricing-tier"></a>Ändra prisnivå

1.  Leta upp din LUIS-prenumeration i [Azure](https://portal.azure.com). Välj prenumerationen LUIS.
    ![Hitta din LUIS-prenumeration](./media/luis-usage-tiers/find.png)
1.  Välj **pris nivå** för att se tillgängliga pris nivåer.
    ![Visa pris nivåer](./media/luis-usage-tiers/subscription.png)
1.  Välj pris nivå och välj **Välj** för att spara ändringen.
    ![Ändra din betalnings nivå för LUIS](./media/luis-usage-tiers/plans.png)
1.  När pris ändringen är klar verifierar ett popup-fönster den nya pris nivån.
    ![Verifiera din LUIS-betalningsnivå](./media/luis-usage-tiers/updated.png)
1. Kom ihåg att [tilldela den här slut punkts nyckeln](#assign-a-resource-to-an-app) på **publicerings** sidan och Använd den i alla slut punkts frågor.

## <a name="viewing-azure-resource-metrics"></a>Visa Azures resurs mått

### <a name="viewing-azure-resource-summary-usage"></a>Visa användning av Azure-resurs Sammanfattning
Du kan visa LUIS användnings information i Azure. På sidan **Översikt** visas senaste sammanfattnings information, inklusive anrop och fel. Om du gör en LUIS kan du omedelbart titta på **sidan Översikt**, tillåta upp till fem minuter innan användningen visas.

![Visa sammanfattnings användning](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Anpassa användnings diagram för Azure-resursanvändning
Mått är en mer detaljerad vy i data.

![Standard mått](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera mått diagram för tids period och Måttyp.

![Anpassade mått](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Avisering om totalt antal transaktioner
Om du vill veta när du har nått ett visst transaktions tröskelvärde, till exempel 10 000 transaktioner, kan du skapa en avisering.

![Standard varningar](./media/luis-usage-tiers/alert-default.png)

Lägg till en mått avisering för det **totala anrops** måttet under en viss tids period. Lägg till e-postadresser till alla personer som ska ta emot aviseringen. Lägg till webhookar för alla system som ska ta emot aviseringen. Du kan också köra en Logic app när aviseringen utlöses.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du använder versioner](luis-how-to-manage-versions.md) för att kontrol lera appens livs cykel.
* Migrera till den nya [redigerings resursen](luis-migration-authoring.md)
