---
title: Konfigurera granskning verktygsinställningar - Content Moderator
titlesuffix: Azure Cognitive Services
description: Använd granskningsverktyget för att konfigurera eller hämta ditt team, taggar, anslutningar, arbetsflöden och autentiseringsuppgifter för Content Moderator.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: f88ccbabc925b651abbc06f571a9d4220ed8aeb2
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756526"
---
# <a name="configure-the-review-tool"></a>Konfigurera granskningsverktyget

Den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) har flera viktiga funktioner som du kommer åt via den **inställningar** menyn på instrumentpanelen.

![Content Moderator granskning för inställningsmenyn](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>Hantera team och undergrupper

Den **Team** fliken låter dig hantera ditt team och undergrupper&mdash;grupper av användare som kan bli meddelad när vissa [mänsklig granskning](../review-api.md#reviews) startas. Du kan bara ha ett team (som du skapar när du registrerar dig med verktyg för granskning), men du kan skapa flera undergrupper. Team-administratör kan bjuda in medlemmar, ange deras behörigheter och tilldela dem till olika undergrupper.

![Granska tool teaminställningar](images/settings-2-team.png)

Undergrupper är användbara för att skapa eskalering team eller team som är dedikerade för att granska specifika kategorier av innehåll. Du kan exempelvis skicka vuxet innehåll till ett separat team för vidare undersökning.

Det här avsnittet beskrivs hur du skapar undergrupper och snabbt tilldela granskningar i farten. Du kan dock använda [arbetsflöden](workflows.md) tilldela granskningar baserat på specifika villkor.

### <a name="create-a-subteam"></a>Skapa en undergrupper

Gå till den **undergrupper** och klicka **Lägg till undergrupper**. Ange namnet på din undergrupper i dialogrutan och klicka på **spara**.

![Undergrupper namn](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>Bjuda in gruppmedlemmar

Du kan inte tilldelas någon en undergrupper om de inte redan är medlem i standard-teamet, så du måste först lägga till granskare till standard-teamet. Klicka på **bjuda in** på den **Team** fliken.

![Bjud in användare](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>Tilldela gruppmedlemmar att subteam

Klicka på den **Lägg till medlem** knappen Tilldela medlemmar från din standard-grupp till en eller flera undergrupper. Du kan bara lägga till befintliga användare till en undergrupper. För att lägga till nya användare som inte ingår i Verktyg för granskning, att bjuda in dem med hjälp av knappen ”Bjud in” på sidan Inställningar för Team.

![Tilldela undergrupper medlemmar](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>Tilldela granskningar till undergrupper

När du har skapat din undergrupper och tilldelade medlemmar kan du börja tilldela innehåll [granskar](../review-api.md#reviews) till dessa undergrupper. Detta görs från den **granska** fliken.
Om du vill tilldela en undergrupper innehåll, klicka på ellipsen i det övre högra hörnet väljer **flytta till**, och välj en undergrupper.

![Tilldela bild granskning till subteam](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>Växla mellan undergrupper

Om du är medlem i fler än en undergrupper kan växla du mellan dessa undergrupper ändra vilka innehåll granskningar visas för dig. I den **granska** , Välj den nedrullningsbara menyn som heter **standard** och välj **Välj undergrupper**. Du kan visa innehåll granskningar för olika undergrupper, men endast de som din är medlem.

![Växla mellan undergrupper](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Taggar

Den **taggar** fliken kan du definiera anpassade moderering taggar utöver de två standardtaggar för moderering&mdash;**isadult** (**en**) och **isracy**  (**r**). När du skapar en anpassad tagg, blir den tillgänglig i granskningar tillsammans med standardtaggar. Du kan ändra vilka taggar som visas i granskningar genom att växla mellan sina synlighetsinställningar.

![Visa taggar, inklusive kryssrutorna ”är synlig”](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>Skapa anpassade taggar

Om du vill skapa en ny tagg, måste du ange ett kort kod, namn och beskrivning i respektive fält.

- **Kort kod**: Ange en kod för två bokstäver för din taggen. Exempel: **cb**
- **Namn**: Ange ett kort och beskrivande taggnamn med små bokstäver utan blanksteg. Exempel: **isbullying**.
- **Beskrivning av**: (valfritt) Ange en beskrivning av typ av innehåll som tagg-mål. Exempel: **Skildringar eller instanser av cyberhot bullying**.

Klicka på **Lägg till** att lägga till en tagg, och klicka på **spara** när du är klar med att skapa taggar.

![Granskningsverktyget skapa dialogrutan Ny tagg](images/settings-3-tags.png)

### <a name="delete-tags"></a>Ta bort taggar

Du kan ta bort anpassade taggar genom att välja ikonen Papperskorgen bredvid deras poster i listan taggar, men du kan inte ta bort standardtaggar.

## <a name="connectors"></a>Anslutningar

Den **Anslutningsappar** fliken låter dig hantera dina anslutningar som är tjänstspecifika plugin-program som kan bearbeta innehåll på olika sätt som en del av innehåll [arbetsflöden](../review-api.md#workflows).

Standard-anslutningen när du skapar ett arbetsflöde är Content Moderator-anslutningstjänsten, som kan markera innehåll som **vuxet** eller **vågat**, hitta svordomar och så vidare. Du kan dock använda andra anslutningsappar som anges här, så länge som du har autentiseringsuppgifter för sina respektive tjänster (om du vill använda Ansikts-API-kopplingen, till exempel du behöver hämta ett [Ansikts-API](https://docs.microsoft.com/azure/cognitive-services/face/overview) prenumerationsnyckel).

Den [granskningsverktyget](./human-in-the-loop.md) innehåller följande kopplingar:

- Känslo-API
- Ansikts-API
- PhotoDNA Cloud Service
- API för textanalys

### <a name="add-a-connector"></a>Lägg till en koppling

Att lägga till en anslutning (och gör den tillgänglig för användning i innehåll [arbetsflöden](../review-api.md#workflows)), väljer du lämplig **Connect** knappen. I nästa dialogruta, anger du din prenumerationsnyckel för tjänsten. När du är klar bör den nya anslutningsappen visas överst på sidan.

![Content Moderator kopplingar inställningar](images/settings-4-connectors.png)

## <a name="workflows"></a>Arbetsflöden

Den **arbetsflöden** fliken låter dig hantera dina [arbetsflöden](../review-api.md#workflows). Arbetsflöden är molnbaserad filter för innehåll och de fungerar med anslutningsappar för att sortera innehåll på olika sätt och vidta lämpliga åtgärder. Här kan du kan definiera, redigera och testa dina arbetsflöden. Se [definiera och Använd arbetsflöden](Workflows.md) anvisningar om hur du gör detta.

![Content Moderator arbetsflödesinställningarna](images/settings-5-workflows.png)

## <a name="credentials"></a>Autentiseringsuppgifter

Den **autentiseringsuppgifter** fliken ger snabb åtkomst till din prenumerationsnyckel för Content Moderator som du behöver för att få åtkomst till någon av de moderering tjänsterna från en REST-anrop eller klient-SDK.

![Content Moderator-autentiseringsuppgifter](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>Använda externa autentiseringsuppgifter för arbetsflöden

Den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com) genererar en kostnadsfri utvärderingsversion nyckel för Azure Content Moderator-tjänster när du registrerar dig, men du kan också konfigurera den att använda en befintlig nyckel från ditt Azure-konto. Detta rekommenderas för storskaliga scenarier som kostnadsfri utvärderingsversion nycklar har strikta användningsbegränsningar ([priser och begränsningar](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)).

Om du har skapat en [Content Moderator resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) i Azure, navigera till den i Azure-portalen och välj den **nycklar** bladet. Kopiera en av dina nycklar.

![Content Moderator-nycklar i Azure portal](images/credentials-azure-portal-keys.PNG)

I den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com)'s **autentiseringsuppgifter** fliken, gå till den **arbetsflödesinställningarna** väljer **redigera**, och klistra in din nyckel i **Ocp-Apim-Subscription-Key** fält. Arbetsflöden som anropar API: er för moderering kommer nu att använda dina Azure autentiseringsuppgifter.

> [!NOTE]
> De andra två fält i den **arbetsflödesinställningarna** fönstret är för anpassade listor för perioden och bild. Se den [anpassade villkor](../try-terms-list-api.md) eller [anpassade avbildningar](../try-image-list-api.md) riktlinjerna för att lära dig om dessa.

### <a name="use-your-azure-account-with-the-review-apis"></a>Använda ditt Azure-konto med granska API: er

Om du vill använda din Azure-nyckel med granska API: er behöver du hämta din resurs-ID. Gå till Content Moderator-resursen i Azure-portalen och välj den **egenskaper** bladet. Kopiera resurs-ID-värdet och klistra in den i den **godkänd resurs-ID(n)** i Verktyg för granskning **autentiseringsuppgifter** fliken.

![Content Moderator resurs-ID i Azure portal](images/credentials-azure-portal-resourceid.PNG)

Om du har angett din prenumerationsnyckel på båda platserna, används inte utvärderingsversion nyckeln som medföljer ditt granska verktyget konto men förblir tillgängliga.

## <a name="next-steps"></a>Nästa steg

Följ den [granska verktyget Snabbstart](../quick-start.md) att börja använda granskningsverktyget i innehållsmoderering scenarier.