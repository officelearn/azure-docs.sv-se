---
title: Så här använder du redigerings- och körningsnycklar - LUIS
description: När du först använder SPRÅKFÖRSTÅELSE (LUIS) behöver du inte skapa en redigeringsnyckel. När du tänker publicera appen och sedan använda slutpunkten för körning måste du skapa och tilldela körningsnyckeln till appen.
services: cognitive-services
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: ea2799e7b8bb18ad4a729a70ae1477cde9f97e95
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754342"
---
# <a name="create-luis-resources"></a>Skapa LUIS-resurser

Redigerings- och körningsresurser ger autentisering till LUIS-appen och slutpunkten för förutsägelse.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

När du loggar in på LUIS-portalen kan du välja att fortsätta med:

* en fri [testversion nyckel](#trial-key) - ger redigering och några förutsägelse slutpunkt frågor.
* en Azure [LUIS-redigeringsresurs.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Logga in på LUIS-portalen och börja skapa

1. Logga in på [LUIS-portalen](https://www.luis.ai) och godkänn användarvillkoren.
1. Börja luis-appen genom att välja vilken typ av LUIS-redigeringsnyckel du vill använda: kostnadsfri utvärderingsversionsnyckel eller ny Azure LUIS-redigeringsnyckel.

    ![Välj en typ av språk understanding-redigeringsresurs](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. När du är klar med resursvalsprocessen [skapar du en ny app](luis-how-to-start-new-app.md#create-new-app-in-luis).

## <a name="trial-key"></a>Testnyckel

Testnyckeln (startmotorn) tillhandahålls för dig. Den används som autentiseringsnyckel för att fråga förutsägelseslutpunktskörningen, upp till 1 000 frågor per månad.

Den visas på både sidan **Användarinställningar** och sidan **Hantera -> Azure-resurser** i LUIS-portalen.

När du är redo att publicera din förutsägelseslutpunkt [skapar](#create-luis-resources) och [tilldelar](#assign-a-resource-to-an-app) du tangenter för redigering och förutsägelsekörning, för att ersätta startnyckelfunktionen.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Skapa resurser i Azure CLI

Använd [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) för att skapa varje resurs individuellt.

Resurs `kind`:

* Författande:`LUIS.Authoring`
* Prognos:`LUIS`

1. Logga in på Azure CLI:

    ```azurecli
    az login
    ```

    Detta öppnar en webbläsare så att du kan välja rätt konto och tillhandahålla autentisering.

1. Skapa en **LUIS-redigeringsresurs** `LUIS.Authoring`, `my-luis-authoring-resource` av typen , med `westus` namnet i den _befintliga_ resursgruppen som heter `my-resource-group` för regionen.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Skapa en **LUIS-förutsägelseslutpunktsresurs**, av typen `LUIS`, med namnet `my-luis-prediction-resource` i den _befintliga_ resursgruppen som heter `my-resource-group` för `westus` regionen. Om du vill ha ett högre dataflöde än den kostnadsfria nivån ändrar du `F0` till `S0`. Läs mer om [prisnivåer och dataflöde](luis-boundaries.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > De här nycklarna används **inte** av LUIS-portalen förrän de har tilldelats i LUIS-portalen på **Hantera -> Azure-resurser**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Tilldela en redigeringsresurs i LUIS-portalen för alla appar

Du kan tilldela en redigeringsresurs för en enskild app eller för alla appar i LUIS. Följande procedur tilldelar alla appar till en enda redigeringsresurs.

1. Logga in på [LUIS-portalen](https://www.luis.ai).
1. Längst till höger längst upp i navigeringsfältet väljer du ditt användarkonto och väljer sedan **Inställningar**.
1. På sidan **Användarinställningar** väljer du **Lägg till redigeringsresurs** och väljer sedan en befintlig redigeringsresurs. Välj **Spara**.

## <a name="assign-a-resource-to-an-app"></a>Tilldela en resurs till en app

Du kan tilldela en enskild resurs, redigering eller förutsägelseslutpunktskörning, till en app med följande procedur.

1. Logga in på [LUIS-portalen](https://www.luis.ai)och välj sedan en app i listan **Mina appar.**
1. Navigera till sidan **Hantera -> Azure-resurser.**

    ![Välj hantera -> Azure-resurser i LUIS-portalen för att tilldela en resurs till appen.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Välj fliken Förutsägelse eller Redigering av resurser och välj sedan knappen **Lägg till förutsägelseresurs** eller **Lägg till redigeringsresurs.**
1. Markera fälten i formuläret för att hitta rätt resurs och välj sedan **Spara**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Tilldela körningsresurs utan att använda LUIS-portalen

För automatiseringsändamål, till exempel en CI/CD-pipeline, kanske du vill automatisera tilldelningen av en LUIS-körningsresurs till en LUIS-app. För att kunna göra det måste du utföra följande steg:

1. Hämta en Azure Resource Manager-token från den här [webbplatsen](https://resources.azure.com/api/token?plaintext=true). Den här token upphör att gälla så använd den omedelbart. Begäran returnerar en Azure Resource Manager-token.

    ![Begär Azure Resource Manager-token och ta emot Azure Resource Manager-token](./media/luis-manage-keys/get-arm-token.png)

1. Använd token för att begära LUIS-körningsresurserna över prenumerationer, från [API:et för Get LUIS azure-konton](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), som ditt användarkonto har åtkomst till.

    Detta POST API kräver följande inställningar:

    |Huvud|Värde|
    |--|--|
    |`Authorization`|Värdet på `Authorization` `Bearer {token}`är . Observera att tokenvärdet måste föregås `Bearer` av ordet och ett blanksteg.|
    |`Ocp-Apim-Subscription-Key`|Din redigeringsnyckel.|

    Det här API:et returnerar en matris med JSON-objekt för dina LUIS-prenumerationer, inklusive prenumerations-ID, resursgrupp och resursnamn, som returneras som kontonamn. Leta reda på det objekt i matrisen som är LUIS-resursen som ska tilldelas LUIS-appen.

1. Tilldela token till LUIS-resursen med [tilldela ett LUIS-azure-konto till ett](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) program-API.

    Detta POST API kräver följande inställningar:

    |Typ|Inställning|Värde|
    |--|--|--|
    |Huvud|`Authorization`|Värdet på `Authorization` `Bearer {token}`är . Observera att tokenvärdet måste föregås `Bearer` av ordet och ett blanksteg.|
    |Huvud|`Ocp-Apim-Subscription-Key`|Din redigeringsnyckel.|
    |Huvud|`Content-type`|`application/json`|
    |Querystring|`appid`|LUIS-app-ID.
    |Innehåll||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    När det här API:et lyckas returneras statusen 201 - skapad.

## <a name="unassign-resource"></a>Tilldela resurs som inte har tilldelats

1. Logga in på [LUIS-portalen](https://www.luis.ai)och välj sedan en app i listan **Mina appar.**
1. Navigera till sidan **Hantera -> Azure-resurser.**
1. Välj fliken Förutsägelse eller Redigering av resurser och välj sedan knappen **Tilldela bort resurs** för resursen.

När du avtilldelar en resurs tas den inte bort från Azure. Det är bara olänkat från LUIS.

## <a name="reset-authoring-key"></a>Återställa redigeringsnyckeln

**För [att skapa resursrendererade](luis-migration-authoring.md) appar:** Om redigeringsnyckeln komprometteras återställer du nyckeln i Azure-portalen på **sidan Nycklar** för den redigeringsresursen.

**För appar som inte har migrerat ännu:** nyckeln återställs på alla dina appar i LUIS-portalen. Om du skapar dina appar via de författande API:erna måste du ändra värdet på Ocp-Apim-Subscription-Key till den nya nyckeln.

## <a name="regenerate-azure-key"></a>Återskapa Azure-nyckel

Återskapa Azure-nycklarna från Azure-portalen på sidan **Nycklar.**

## <a name="delete-account"></a>Ta bort konto

Se [Lagring och borttagning](luis-concept-data-storage.md#accounts) av data för information om vilka data som tas bort när du tar bort ditt konto.

## <a name="change-pricing-tier"></a>Ändra prisnivå

1.  Hitta din LUIS-prenumeration i [Azure.](https://portal.azure.com) Välj LUIS-prenumerationen.
    ![Hitta din LUIS-prenumeration](./media/luis-usage-tiers/find.png)
1.  Välj **prisnivå** för att se tillgängliga prisnivåer.
    ![Visa prisnivåer](./media/luis-usage-tiers/subscription.png)
1.  Välj prisnivå och välj **Välj för** att spara ändringen.
    ![Ändra luis-betalningsnivån](./media/luis-usage-tiers/plans.png)
1.  När prisändringen är klar verifierar ett popup-fönster den nya prisnivån.
    ![Verifiera luis-betalningsnivån](./media/luis-usage-tiers/updated.png)
1. Kom ihåg att [tilldela den här slutpunktsnyckeln](#assign-a-resource-to-an-app) på sidan **Publicera** och använda den i alla slutpunktsfrågor.

## <a name="viewing-azure-resource-metrics"></a>Visa Azure-resursmått

### <a name="viewing-azure-resource-summary-usage"></a>Visa Azure-resurssammanfattningsanvändning
Du kan visa LUIS-användningsinformation i Azure. På sidan **Översikt** visas den senaste sammanfattningsinformationen, inklusive samtal och fel. Om du gör en LUIS-slutpunktsbegäran kan du omedelbart titta på **översiktssidan**, upp till fem minuter för att användningen ska visas.

![Visa sammanfattningsanvändning](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Anpassa Azure-resursanvändningsdiagram
Mått ger en mer detaljerad vy över data.

![Standardmått](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera måttdiagram för tidsperiod och måtttyp.

![Anpassade mått](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Tröskelavisering för totalt antal transaktioner
Om du vill veta när du har nått ett visst transaktionströskel, till exempel 10 000 transaktioner, kan du skapa en avisering.

![Standardaviseringar](./media/luis-usage-tiers/alert-default.png)

Lägg till en måttavisering för det **totala anropsmåttet** för en viss tidsperiod. Lägg till e-postadresser för alla personer som ska få aviseringen. Lägg till webhooks för alla system som ska ta emot aviseringen. Du kan också köra en logikapp när aviseringen utlöses.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [använder versioner](luis-how-to-manage-versions.md) för att styra appens livscykel.
* Förstå begreppen, inklusive [redigeringsresursen](luis-concept-keys.md#authoring-key) och [deltagarna](luis-concept-keys.md#contributions-from-other-authors) på den resursen.
* Lär dig hur du [skapar](luis-how-to-azure-subscription.md) redigerings- och körningsresurser
* Migrera till den nya [redigeringsresursen](luis-migration-authoring.md)
