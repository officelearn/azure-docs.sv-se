---
title: Felsökning - QnA Maker
description: Den kurerade listan över de vanligaste frågorna om QnA Maker-tjänsten hjälper dig att använda tjänsten snabbare och bättre resultat.
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: diberry
ms.openlocfilehash: 310cfe0cc8c1f647c09b8e0efb435a1ab326ff62
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474958"
---
# <a name="troubleshooting-for-qna-maker"></a>Felsökning för QnA Maker

Den kurerade listan över de vanligaste frågorna om QnA Maker-tjänsten hjälper dig att använda tjänsten snabbare och bättre resultat.

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>Hantera förutsägelser

<details>
<summary><b>Hur kan jag förbättra dataflödesprestandan för frågeprognoser?</b></summary>

**Svar:** Prestandaproblem för dataflöde indikerar att du måste skala upp för både din apptjänst och din kognitiva sökning. Överväg att lägga till en replik i kognitiv sökning för att förbättra prestanda.

Läs mer om [prisnivåer](Concepts/azure-resources.md).
</details>

<details>
<summary><b>Så här skaffar du QnAMaker-tjänstens slutpunkt</b></summary>

**Svar:** QnAMaker-tjänstslutpunkten är användbar för felsökning när du kontaktar QnAMaker Support eller UserVoice. Slutpunkten är en URL i `https://your-resource-name.azurewebsites.net`det här formuläret: .

1. Gå till din QnAMaker-tjänst (resursgrupp) i [Azure-portalen](https://portal.azure.com)

    ![QnAMaker Azure-resursgrupp i Azure-portalen](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Välj den apptjänst som är associerad med QnA Maker-resursen. Vanligtvis är namnen desamma.

     ![Välj QnAMaker-apptjänst](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Slutpunkts-URL:en är tillgänglig i avsnittet Översikt

    ![Slutpunkten QnAMaker](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

## <a name="manage-the-knowledge-base"></a>Hantera kunskapsbasen

<details>
<summary><b>Jag har av misstag tagit bort en del av min QnA Maker, vad ska jag göra?</b></summary>

**Svar**: Ta inte bort någon av de Azure-tjänster som skapats tillsammans med QnA Maker-resursen, till exempel Sök eller Web App. Dessa är nödvändiga för att QnA Maker ska fungera, om du tar bort en, kommer QnA Maker sluta fungera korrekt.

Alla borttagningar är permanenta, inklusive fråge- och svarspar, filer, webbadresser, anpassade frågor och svar, kunskapsbaser eller Azure-resurser. Se till att du exporterar kunskapsbasen från sidan **Inställningar** innan du tar bort någon del av kunskapsbasen.

</details>

<details>
<summary><b>Varför extraherar inte mina webbadresser/filer frågesvarspar?</b></summary>

**Svar**: Det är möjligt att QnA Maker inte automatiskt kan extrahera visst fråge- och svarsinnehåll (QnA) från giltiga vanliga vanliga frågor. I sådana fall kan du klistra in QnA-innehållet i en TXT-fil och se om verktyget kan inta det. Alternativt kan du redaktionellt lägga till innehåll i din kunskapsbas via [QnA Maker-portalen](https://qnamaker.ai).

</details>

<details>
<summary><b>Hur stor kunskapsbas kan jag skapa?</b></summary>

**Svar**: Kunskapsbasens storlek beror på vilken SKU för Azure-sökning du väljer när du skapar QnA Maker-tjänsten. Läs [här](./Tutorials/choosing-capacity-qnamaker-deployment.md) för mer information.

</details>

<details>
<summary><b>Varför kan jag inte se något i listrutan när jag försöker skapa en ny kunskapsbas?</b></summary>

**Svar**: Du har inte skapat några QnA Maker-tjänster i Azure ännu. Läs [här](./How-To/set-up-qnamaker-service-azure.md) för att lära dig hur man gör det.

</details>

<details>
<summary><b>Hur delar jag en kunskapsbas med andra?</b></summary>

**Svar**: Delning fungerar på samma nivå som en QnA Maker-tjänst, det vill än alla kunskapsbaser i tjänsten kommer att delas. Läs [här](./How-To/collaborate-knowledge-base.md) hur du samarbetar på en kunskapsbas.

</details>

<details>
<summary><b>Kan du dela en kunskapsbas med en medarbetare som inte finns i samma AAD-klient, för att ändra en kunskapsbas?</b></summary>

**Svar**: Delning baseras på Azure-rollbaserad åtkomstkontroll (RBAC). Om du kan dela _en_ resurs i Azure med en annan användare kan du också dela QnA Maker.

</details>

<details>
<summary><b>Om du har en App Service Plan med 5 QnAMaker kunskapsbaser. Kan du tilldela läs-/skrivrättigheter till 5 olika användare så att var och en av dem kan komma åt endast 1 QnAMaker kunskapsbas?</b></summary>

**Svar**: Du kan dela en hel QnAMaker tjänst, inte enskilda kunskapsbaser.

</details>

<details>
<summary><b>Hur ändrar jag standardmeddelandet när ingen bra matchning hittas?</b></summary>

**Svar**: Standardmeddelandet är en del av inställningarna i apptjänsten.
- Gå till apptjänstresursen i Azure-portalen

![qnamaker appservice](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- Klicka på alternativet **Inställningar**

![qnamaker appservice inställningar](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- Ändra värdet för inställningen **DefaultAnswer**
- Starta om apptjänsten

![qnamaker appservice starta om](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>Varför extraheras inte min SharePoint-länk?</b></summary>

**Svar**: Se [Datakällplatser](./Concepts/knowledge-base.md#data-source-locations) för mer information.

</details>

<details>
<summary><b>De uppdateringar som jag gjorde i min kunskapsbas återspeglas inte på publicering. Varför inte?</b></summary>

**Svar**: Varje redigeringsåtgärd, oavsett om det är i en tabelluppdatering, test eller inställning, måste sparas innan den kan publiceras. Var noga med att klicka på **knappen Spara och träna** efter varje redigering.

</details>

<details>
<summary><b>Stöder kunskapsbasen omfattande data eller multimedia?</b></summary>

**Svar:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatisk extrahering av multimedia för filer och webbadresser

* URLS - begränsad HTML-till-Markdown konvertering kapacitet.
* Filer - stöds inte

#### <a name="answer-text-in-markdown"></a>Svara på text i markering
När QnA-uppsättningarna finns i kunskapsbasen kan du redigera ett svars markeringstext så att den innehåller länkar till media som är tillgängliga från offentliga webbadresser.


</details>

<details>
<summary><b>Stöder QnA Maker icke-engelska språk?</b></summary>

**Svar**: Se mer information om [språk som stöds](./Overview/languages-supported.md).

Om du har innehåll från flera språk måste du skapa en separat tjänst för varje språk.

</details>

## <a name="manage-service"></a>Hantera tjänst

<details>
<summary><b>När ska jag starta om min apptjänst?</b></summary>

**Svar**: Uppdatera apptjänsten när varningsikonen ligger bredvid versionsvärdet för kunskapsbasen i tabellen **Slutpunktstangenter** på [sidan](https://www.qnamaker.ai/UserSettings) **Användarinställningar** .

</details>

<details>
<summary><b>Jag har tagit bort min befintliga söktjänst. Hur kan jag åtgärda detta?</b></summary>

**Svar**: Om du tar bort ett Azure Cognitive Search-index är åtgärden slutgiltig och indexet kan inte återställas.

</details>

<details>
<summary><b>Jag har `testkb` tagit bort mitt index i min söktjänst. Hur kan jag åtgärda detta?</b></summary>

**Svar**: Dina gamla data kan inte återställas. Skapa en ny QnA Maker-resurs och skapa din kunskapsbas igen.

</details>

<details>
<summary><b>När ska jag uppdatera mina slutpunktsnycklar?</b></summary>

**Svar**: Uppdatera slutpunktstangenterna om du misstänker att de har komprometterats.

</details>

<details>
<summary><b>Kan jag använda samma Azure Cognitive Search-resurs för kunskapsbaser med flera språk?</b></summary>

**Svar:** Om du vill använda flera språk och flera kunskapsbaser måste användaren skapa en QnA Maker-resurs för varje språk. Detta skapar en separat Azure-söktjänst per språk. Om du blandar olika språkkunskapsbaser i en enda Azure-söktjänst försämras resultatens relevans.

</details>

<details>
<summary><b>Hur ändrar jag namnet på Azure Cognitive Search-resursen som används av QnA Maker?</b></summary>

**Svar**: Namnet på Azure Cognitive Search-resursen är QnA Maker-resursnamnet med några slumpmässiga bokstäver som läggs till i slutet. Detta gör det svårt att skilja mellan flera sökresurser för QnA Maker. Skapa en separat söktjänst (namnge den som du vill) och anslut den till din QnA-tjänst. Stegen liknar de steg du behöver göra för att [uppgradera en Azure-sökning](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service).

</details>

<details>
<summary><b>När QnA `Runtime core is not initialized,` Maker returnerar hur åtgärdar jag det?</b></summary>

**Svar**: Diskutrymmet för apptjänsten kan vara fullt. Åtgärder för att åtgärda diskutrymmet:

1. Välj din QnA Maker-apptjänst i [Azure-portalen](https://portal.azure.com)och stoppa sedan tjänsten.
1. När du fortfarande är på App-tjänsten väljer du **Utvecklingsverktyg**och sedan **avancerade verktyg**och går sedan **.** Då öppnas ett nytt webbläsarfönster.
1. Välj **Felsökningskonsol**och sedan **CMD** för att öppna ett kommandoradsverktyg.
1. Navigera till _katalogen wwwroot/Data/QnAMaker.Navigate to the site/wwwroot/Data/QnAMaker/_ directory.
1. Ta bort alla mappar vars `rd`namn börjar med .

    **Ta inte bort** följande:

    * KbIdToRankerMappings.txt-fil
    * EndpointSettings.json-fil
    * Mappen Slutpunktstangenter

1. Starta apptjänsten.
1. Öppna kunskapsbasen för att verifiera att det fungerar nu.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integrera med andra tjänster, inklusive Bots

<details>
<summary><b>Måste jag använda Bot Framework för att kunna använda QnA Maker?</b></summary>

**Svar**: Nej, du behöver inte använda [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) med QnA Maker. QnA Maker erbjuds dock som en av flera mallar i [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0). Bot Service möjliggör snabb intelligent bot utveckling genom Microsoft Bot Framework, och det körs i en server-mindre miljö.

</details>

<details>
<summary><b>Hur skapar jag en ny robot med QnA Maker?</b></summary>

**Svar**: Följ instruktionerna i den [här](./Quickstarts/create-publish-knowledge-base.md) dokumentationen för att skapa din bot med Azure Bot Service.

</details>

<details>
<summary><b>Hur använder jag en annan kunskapsbas med en befintlig Azure bot-tjänst?</b></summary>

**Svar**: Du måste ha följande information om din kunskapsbas:

* Knowledge base ID.
* Kunskapsbasens publicerade anpassade underdomännamn för `host`slutpunkt, så kallat , finns på **sidan Inställningar** när du har publicerat.
* Kunskapsbasens publicerade slutpunktsnyckel - finns på **sidan Inställningar** när du har publicerat.

Med den här informationen går du till robotens apptjänst i Azure-portalen. Ändra dessa värden under **Inställningar -> Konfiguration -> Programinställningar.**

Kunskapsbasens slutpunktsnyckel `QnAAuthkey` är märkt i ABS-tjänsten.

</details>

<details>
<summary><b>Kan två eller flera klientprogram dela en kunskapsbas?</b></summary>

**Svar**: Ja, kunskapsbasen kan efterfrågas från valfritt antal klienter. Om svaret från kunskapsbasen verkar vara långsam eller time out, överväg att uppgradera tjänstnivån för apptjänsten som är associerad med kunskapsbasen.

</details>

<details>
<summary><b>Hur bäddar jag in QnA Maker-tjänsten på min webbplats?</b></summary>

**Svar**: Så här bäddar du in QnA Maker-tjänsten som en webbchattkontroll på din webbplats:

1. Skapa din FAQ bot genom att följa instruktionerna [här](./Quickstarts/create-publish-knowledge-base.md).
2. Aktivera webbchatten genom att följa stegen [här](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)

</details>

## <a name="data-storage"></a>Datalagring

<details>
<summary><b>Vilka data lagras och var lagras de?</b></summary>

**Svar:**

När du skapar din QnA Maker-tjänst har du valt en Azure-region. Dina kunskapsbaser och loggfiler lagras i den här regionen.

</details>