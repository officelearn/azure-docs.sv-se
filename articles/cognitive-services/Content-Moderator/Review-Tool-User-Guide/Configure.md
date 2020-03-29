---
title: Konfigurera inställningar för granskningsverktyg - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Använd granskningsverktyget för att konfigurera eller hämta ditt team, taggar, kopplingar, arbetsflöden och autentiseringsuppgifter för Innehållsmoderator.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 2d685683bdc359b31a5a6c550c19e8c0d858f12a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220131"
---
# <a name="configure-the-review-tool"></a>Konfigurera granskningsverktyget

[Granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) har flera viktiga funktioner som du kan komma åt via **menyn Inställningar** på instrumentpanelen.

![Menyn Innehållsmoderatorgranskning har också inställningsmenyn](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Hantera team och undergrupper

På fliken **Team** kan du hantera&mdash;dina team- och underteamsgrupper av användare som kan meddelas när vissa [mänskliga granskningar](../review-api.md#reviews) startas. Du kan bara ha ett team (som du skapar när du registrerar dig med granskningsverktyget), men du kan skapa flera undergrupper. Gruppadministratören kan bjuda in medlemmar, ange sina behörigheter och tilldela dem till olika underteam.

![Granska inställningar för verktygsteam](images/settings-2-team.png)

Underteam är användbara för att skapa eskaleringsteam eller team som är dedikerade till att granska specifika kategorier av innehåll. Du kan till exempel skicka barnförbjudet innehåll till ett separat team för vidare granskning.

I det här avsnittet beskrivs hur du skapar underteam och snabbt tilldelar recensioner i farten. Du kan dock använda arbetsflöden för att tilldela [granskningar](workflows.md) baserat på specifika kriterier.

### <a name="create-a-subteam"></a>Skapa ett underteam

Gå till avsnittet **Undergrupper** och klicka på **Lägg till underteam**. Ange undergruppsnamnet i dialogrutan och klicka på **Spara**.

![Namn på underteam](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Bjud in lagkamrater

Du kan inte tilldela någon till ett underteam om de inte redan är medlemmar i standardteamet, så du måste lägga till granskare i standardteamet först. Klicka på **Bjud in** på fliken **Team.**

![Bjuda in användare](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Tilldela lagkamrater till underteam

Klicka på knappen **Lägg till medlem** om du vill tilldela medlemmar från standardteamet till ett eller flera underlag. Du kan bara lägga till befintliga användare i ett underteam. Om du vill lägga till nya användare som inte finns med i granskningsverktyget kan du bjuda in dem genom att använda knappen Bjud in på sidan Gruppinställningar.

![Tilldela undergruppsmedlemmar](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Tilldela granskningar till underteam

När du har skapat dina undergrupper och tilldelat medlemmar kan du börja tilldela [innehållsgranskningar](../review-api.md#reviews) till dessa undergrupper. Detta görs från fliken **Granska** på webbplatsen.
Om du vill tilldela innehåll till ett underteam klickar du på ellipsen i det övre högra hörnet, väljer **Flytta till**och väljer ett underteam.

![Tilldela bildgranskning till underteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Växla mellan undergrupper

Om du är medlem i mer än ett underteam kan du växla mellan dessa undergrupper för att ändra vilka innehållsgranskningar som visas åt dig. På fliken **Granska** väljer du den nedrullningsbara menyn **Standard** och väljer **Välj undergrupp**. Du kan visa innehållsrecensioner för olika underlag, men bara de som du är medlem i.

![Växla mellan undergrupper](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Taggar

På fliken **Taggar** kan du definiera anpassade modereringstaggar utöver de två&mdash;standardtaggarna för moderering**ärförst** **(a)** och **isracy** (**r**). När du skapar en anpassad tagg blir den tillgänglig i recensioner tillsammans med standardtaggarna. Du kan ändra vilka taggar som visas i recensioner genom att ändra deras synlighetsinställningar.

![Taggar vy, inklusive kryssrutan "Är synlig"](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Skapa anpassade taggar

Om du vill skapa en ny tagg måste du ange en kort kod, ett namn och en beskrivning i respektive fält.

- **Kort kod:** Ange en kod med två bokstäver för taggen. Exempel: **cb**
- **Namn**: Ange ett kort och beskrivande taggnamn i gemener utan blanksteg. Exempel: **isbullying**.
- **Beskrivning**: (valfritt) Ange en beskrivning av vilken typ av innehåll som taggen riktar sig till. Exempel: **Skildringar eller förekomster av cybermobbning**.

Klicka på **Lägg till** om du vill lägga till en tagg och klicka på **Spara** när du har skapat taggar.

![Granskningsverktyg skapa en ny taggdialogruta](images/settings-3-tags.png)

### <a name="delete-tags"></a>Ta bort taggar

Du kan ta bort anpassade taggar genom att välja papperskorgen bredvid deras poster i listan Taggar, men du kan inte ta bort standardtaggarna.

## <a name="connectors"></a>Anslutningar

På fliken **Anslutningsappar** kan du hantera dina anslutningsappar, som är tjänstspecifika insticksprogram som kan bearbeta innehåll på olika sätt som en del av [innehållsarbetsflödena](../review-api.md#workflows).

Standardkopplingen när du skapar ett arbetsflöde är content moderator-kopplingen, som kan markera innehåll som **vuxet** eller **krant**, hitta svordomar och så vidare. Du kan dock använda andra kopplingar, listade här, så länge du har autentiseringsuppgifter för deras respektive tjänster [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview) (om du till exempel vill använda Face-kopplingen måste du skaffa en Face-prenumerationsnyckel).

[Granskningsverktyget](./human-in-the-loop.md) innehåller följande kopplingar:

- Känsla
- Ansikte
- PhotoDNA molntjänst
- Textanalys

### <a name="add-a-connector"></a>Lägga till en koppling

Om du vill lägga till en kopplingsapp (och göra den tillgänglig för användning i [innehållsarbetsflöden)](../review-api.md#workflows)väljer du lämplig **Connect-knapp.** I nästa dialogruta anger du din prenumerationsnyckel för den tjänsten. När du är klar ska den nya kopplingen visas högst upp på sidan.

![Inställningar för innehållsmoderatoranslutningar](images/settings-4-connectors.png)

## <a name="workflows"></a>Arbetsflöden

På **fliken arbetsflöden** kan du hantera [dina arbetsflöden](../review-api.md#workflows). Arbetsflöden är molnbaserade filter för innehåll och de arbetar med kopplingar för att sortera innehåll på olika sätt och vidta lämpliga åtgärder. Här kan du definiera, redigera och testa dina arbetsflöden. Se [Definiera och använda arbetsflöden](Workflows.md) för vägledning om hur du gör detta.

![Arbetsflödesinställningar för innehållsmoderator](images/settings-5-workflows.png)

## <a name="credentials"></a>Autentiseringsuppgifter

Fliken **Autentiseringsuppgifter** ger snabb åtkomst till din Content Moderator-prenumerationsnyckel, som du måste komma åt någon av modereringstjänsterna från ett REST-samtal eller klient-SDK.

![Autentiseringsuppgifter för innehållsmodererator](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Använda externa autentiseringsuppgifter för arbetsflöden

[Granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) genererar en kostnadsfri utvärderingsversion för Azure Content Moderator-tjänster när du registrerar dig, men du kan också konfigurera den för att använda en befintlig nyckel från ditt Azure-konto. Detta rekommenderas för storskaliga scenarier, eftersom kostnadsfria utvärderingsnycklar har strikta[användningsgränser](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)( Prissättning och gränser ).

Om du har skapat en [Content Moderator-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) i Azure navigerar du till den i Azure-portalen och väljer **bladet Nycklar.** Kopiera en av dina nycklar.

![Innehållsmoderatornycklar i Azure-portalen](images/credentials-azure-portal-keys.PNG)

Gå till fönstret **Arbetsflödesinställningar** på fliken **Autentiseringsuppgifter** för granskningsverktyget, välj **Redigera**och klistra in nyckeln i fältet **Ocp-Apim-Subscription-Key.** [Review tool](https://contentmoderator.cognitive.microsoft.com) Nu använder arbetsflöden som anropar modererings-API:erna din Azure-autentiseringsuppgifter.

> [!NOTE]
> De andra två fälten i fönstret **Arbetsflödesinställningar** är för anpassade term- och bildlistor. Läs anpassade [termer](../try-terms-list-api.md) eller [anpassade avbildningsguider](../try-image-list-api.md) om du vill veta mer om dessa.

### <a name="use-your-azure-account-with-the-review-apis"></a>Använda ditt Azure-konto med gransknings-API:erna

Om du vill använda din Azure-nyckel med gransknings-API:erna måste du hämta ditt resurs-ID. Gå till din Content Moderator-resurs i Azure-portalen och välj bladet **Egenskaper.** Kopiera resurs-ID-värdet och klistra in det i fältet **Vitlistade resurs-ID(er)** på fliken **Autentiseringsuppgifter för** granskningsverktyget.

![Resurs-ID för innehållsmoderator i Azure-portalen](images/credentials-azure-portal-resourceid.PNG)

Om du har angett din prenumerationsnyckel på båda ställena används inte utvärderingsnyckeln som medföljer ditt granskningsverktygskonto utan förblir tillgänglig.

## <a name="next-steps"></a>Nästa steg

Följ [snabbstarten för granskningsverktyget](../quick-start.md) för att börja använda granskningsverktyget i scenarier för innehållsmoderering.