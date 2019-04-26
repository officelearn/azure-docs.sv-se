---
title: Migrera Microsoft Translator Hub arbetsyta och projekt? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: Migrera din hubb arbetsyta och projekt till anpassad Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 73fc0d26612d32f2614899c62f680ff9e85d1609
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60512766"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrera Hub arbetsyta och projekt till anpassad Translator

Du kan enkelt migrera dina [Microsoft Translator Hub](https://hub.microsofttranslator.com/) arbetsyta och projekt till anpassad Translator. Migrering initieras från Microsoft Hub genom att välja en arbetsyta eller -projektet och sedan att välja en arbetsyta i anpassade Translator och sedan välja de kurser som du vill överföra. När migreringen har startat överförs inställningar för valda utbildning med alla relevanta dokumenten. Distribuerade modeller tränas och kan vara autodeployed när åtgärden har slutförts.

Dessa åtgärder utföras manuellt under migreringen:
* Alla dokument och definitioner av projektet att namnen på överförs med hjälp av ”hub_” som prefix till. Automatiskt genererade test och justera data får namnet hub_systemtune_\<modelid > eller hub_systemtest_\<modelid >.
* Alla kurser som fanns i distribuerat läge när migreringen sker kommer automatiskt tränas med dokument för Hub-utbildning. Den här kursen kommer inte att debiteras till din prenumeration. Om automatisk distribution har valts för migrering, kommer den tränade modellen att distribueras vid slutförandet. Vanliga som är värd för avgifter kommer att tillämpas.
* Alla migrerade utbildningar som inte är i distribuerat läge placeras i den migrerade utkast. Du har möjlighet att träna en modell med de migrerade definitionen i det här tillståndet, men vanliga utbildning avgifter tillkommer.
* När som helst BLEU poängen som har migrerats från hubben utbildning kan hittas på sidan TrainingDetails i modellen i ”Bleu poäng i MT-hubb” rubrik.

> [!Note]
> För en utbildning ska lyckas, kräver anpassade Translator minst 10 000 unika extraherade meningar. Anpassade Translator kan inte genomföra en utbildningar med färre än den [föreslagna minsta](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences).

## <a name="find-custom-translator-workspace-id"></a>Hitta anpassade Translator arbetsyte-ID

Att migrera [Microsoft Translator Hub](https://hub.microsofttranslator.com/) arbetsytan behöver du mål arbetsyte-ID i anpassade Translator. Målarbetsyta i anpassade Translator är där alla Hub arbetsytor och projekt ska migreras till.

Du hittar ditt mål för arbetsyte-ID på inställningssidan för anpassad Translator:

1. Gå till sidan ”inställningen” i anpassade Translator-portalen.

2. Du hittar arbetsyte-ID i grundläggande Information.

    ![Så här hittar du mål arbetsyte-ID](media/how-to/how-to-find-destination-ws-id.png)

3. Håll ditt mål för arbetsyte-ID för att referera under migreringsprocessen.

## <a name="migrate-a-project"></a>Migrera ett projekt

Om du vill migrera dina projekt selektivt gör Microsoft Translator Hub som möjligt.

Att migrera ett projekt:

1. Logga in till Microsoft Translator-hubb.

2. Gå till sidan ”projekt”.

3. Klicka på ”migrera”-länk till lämpligt projekt.

    ![Hur du migrerar från hubben](media/how-to/how-to-migrate-from-hub.png)

4. När du trycker på migrera länken visas ett formulär så att du kan:
   * Ange den arbetsyta som du vill överföra till på anpassade Translator
   * Ange om du vill att överföra alla utbildningar med lyckade utbildningar eller bara distribuerade utbildningar. Som standard överförs alla lyckade utbildningar.
   * Ange om du vill att din utbildning automatiskt om en utbildning har slutförts. Som standard utbildning inte automatisk distribuerats när åtgärden har slutförts.

5. Klicka på ”Skicka begäran”.

## <a name="migrate-a-workspace"></a>Migrera en arbetsyta

Förutom att du migrerar ett enda projekt, kan du också migrera alla projekt med lyckade utbildningar i en arbetsyta. Detta innebär att varje projekt i arbetsytan som ska utvärderas som om länken migrera har tryckts ned. Den här funktionen är lämplig för användare med flera projekt som vill migrera dem alla till anpassad Translator med samma inställningar. Migrering av arbetsyta kan initieras från inställningssidan för Translator-hubben.

Att migrera en arbetsyta:

1. Logga in till Microsoft Translator-hubb.

2. Gå till sidan ”Inställningar”.

3. Klicka på ”migrera arbetsytedata till anpassad Translator” på sidan ”Inställningar”.

    ![Hur du migrerar från hubben](media/how-to/how-to-migrate-workspace-from-hub.png)

4. På nästa sida väljer du något av dessa två alternativ:

    a. Distribuerade utbildningar: Det här alternativet kommer att migrera dina distribuerade system och relaterade dokument.

    b. Alla lyckade utbildningar: Det här alternativet kommer att migrera alla lyckade utbildningar och relaterade dokument.

    c. Ange ditt mål för arbetsyte-ID i anpassade Translator.

    ![Hur du migrerar från hubben](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klicka på Skicka begäran.

## <a name="migration-history"></a>Migreringshistoriken

När du har begärt arbetsyta / project migrering från hubben, hittar du din migreringshistoriken inställningssidan för anpassad Translator.

Följ dessa steg om du vill visa migreringshistoriken:

1. Gå till sidan ”inställningen” i anpassade Translator-portalen.

2. Klicka på Migreringshistoriken i avsnittet Migreringshistoriken på sidan Inställningar.

    ![Migreringshistoriken](media/how-to/how-to-migration-history.png)

Migrering historiksidan visar följande information som sammanfattning för varje migrering som du har begärt.

1. Migrera med: Namn och e-post för användaren skickat den här migreringsbegäran om

2. Migrerade på: Datum- och tidsstämpel av migreringen

3. Projekt: Antalet projekt som begärdes för en migrering v/s antal projekt har migrerats.

4. Kurser: Antal utbildningar som begärdes för en migrering v/s antalet utbildningar migrerats.

5. Dokument: Antal dokument som begärdes för en migrering v/s antal dokument som har migrerats.

    ![Historikinformation för migrering](media/how-to/how-to-migration-history-details.png)

Om du vill ha mer detaljerad migreringsrapport om ditt projekt, utbildningar och dokument, har du alternativet Exportera information som CSV.

## <a name="implementation-notes"></a>Implementeringsanteckningar
* System med språket par ännu inte tillgängliga i anpassade Translator går bara att komma åt data eller ta bort via anpassade Translator. Dessa projekt kommer att markeras som ”ej tillgänglig” på sidan projekt. Vi låter nya språkpar med anpassade Translator blir projekten aktiv för att träna och distribuera. 
* Migrera ett projekt från hubben till anpassad Translator har inte någon inverkan på din hubb utbildningar eller projekt. Vi ta inte bort projekt eller dokument från Hub under migreringen och vi ta bort inte modeller.
* Du får endast att migrera en gång per projekt. Om du vill upprepa en migrering i ett projekt kan du kontakta oss.
* Anpassade Translator stöder NMT språkpar till och från engelska. [Visa den fullständiga listan över stöds langauges](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Hub kräver inte basmodeller och därför har stöd för flera tusen språk. Du kan migrera ett nyckelpar med språket stöds inte, men vi kommer endast utför migrering av dokument och projektet definitioner. Vi kommer inte att kunna skapa den nya modellen. Dessutom är kommer dessa dokument och projekt att visas som inaktiva för att indikera att de inte kan användas just nu. Om du lägger till stöd för dessa projekt och/eller dokument, blir de aktiva och trainable.
* Anpassade Translator stöder för närvarande inte enspråkig träningsdata. Du kan migrera enspråkig dokument som par språket stöds inte, men de visas som inaktiva tills enspråkig stöds.
* Anpassade Translator kräver 10 k parallella meningar för att träna. Microsoft Hub kan utbilda i en mindre uppsättning data. Om ett utbildnings migreras som inte uppfyller det här kravet, kommer den inte tränas.

## <a name="custom-translator-versus-hub"></a>Anpassade Translator jämfört med Hub

Den här tabellen jämförs funktionerna mellan Microsoft Translator Hub och anpassade Translator.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Anpassning av funktionsstatus   | Allmän tillgänglighet  | Allmän tillgänglighet |
| API för textöversättning version  | V2    | V3  |
| SMT anpassning | Ja   | Nej |
| NMT anpassning | Nej    | Ja |
| Nya enhetliga Speech services anpassning | Nej    | Ja |
| Ingen spårning | Ja | Ja |

## <a name="new-languages"></a>Nya språk

Om du är en community eller en organisation som arbetar med att skapa ett nytt språksystem för Microsoft Translator, nå ut till [ custommt@microsoft.com ](mailto:custommt@microsoft.com) för mer information.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md).
- Börja använda dina distribuerade anpassade översättningsmodellen via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
