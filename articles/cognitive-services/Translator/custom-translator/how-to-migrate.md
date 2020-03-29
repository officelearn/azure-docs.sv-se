---
title: Vill du migrera arbetsytan och projekten microsoft translator hub? - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du migrerar hubbarbetsytan och projekt till Azure Cognitive Services Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2fa90a8099778bf37ce8534e968a2b1b4345c2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446785"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrera hubbarbetsyta och projekt till anpassad översättare

Du kan enkelt migrera [arbetsytan Microsoft Translator Hub](https://hub.microsofttranslator.com/) och projekt till Custom Translator. Migrering initieras från Microsoft Hub genom att välja en arbetsyta eller ett projekt, sedan välja en arbetsyta i Anpassad översättare och sedan välja de utbildningar som du vill överföra. När migreringen startar överförs de valda utbildningsinställningarna med alla relevanta dokument. Distribuerade modeller tränas och kan distribueras automatiskt när de är klara.

Dessa åtgärder utförs under migreringen:
* Alla dokument och projektdefinitioner kommer att få sina namn överförda med tillägget "hub_" som är förutbestämt till namnet. Automatiskt genererade test- och justeringsdata kommer\<att namnges hub_systemtune_ modelid\<> eller hub_systemtest_ modelid>.
* Alla utbildningar som var i distribuerat tillstånd när migreringen sker tränas automatiskt med hjälp av hubbutbildningens dokument. Den här utbildningen debiteras inte för din prenumeration. Om automatisk distribution har valts för migreringen distribueras den tränade modellen när den är klar. Regelbundna värdavgifter kommer att tillämpas.
* Alla migrerade utbildningar som inte har distribuerats placeras i det migrerade utkasttillståndet. I det här tillståndet har du möjlighet att träna en modell med den migrerade definitionen, men regelbundna utbildningsavgifter tillkommer.
* När som helst kan BLEU-poängen som migrerats från Hub-utbildningen hittas på trainingdetails-sidan i modellen i rubriken "Bleu-poäng i MT Hub".

> [!Note] 
> För att en utbildning ska lyckas kräver Custom Translator minst 10 000 unika extraherade meningar. Custom Translator kan inte genomföra en utbildning med färre än det [föreslagna minimumet.](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)

## <a name="find-custom-translator-workspace-id"></a>Hitta anpassat arbetsytans arbetsyte för översättare

Om du vill migrera [arbetsytan Microsoft Translator Hub](https://hub.microsofttranslator.com/) behöver du målarbetsyte-ID i Anpassad översättare. Målarbetsytan i Custom Translator är där alla dina Hub-arbetsytor och projekt ska migreras till.

Du hittar ditt mål Workspace ID på sidan Anpassade översättareinställningar:

1. Gå till sidan "Inställning" i portalen Anpassad översättare.

2. Du hittar arbetsyte-ID:et i avsnittet Grundläggande information.

    ![Så här hittar du målarbetsyte-ID](media/how-to/how-to-find-destination-ws-id.png)

3. Behåll ditt mål Workspace ID för att referera under migreringsprocessen.

## <a name="migrate-a-project"></a>Migrera ett projekt

Om du vill migrera dina projekt selektivt ger Microsoft Translator Hub dig den möjligheten.

Så här migrerar du ett projekt:

1. Logga in på Microsoft Translator Hub.

2. Gå till sidan "Projekt".

3. Klicka på länken Migrera för lämpligt projekt.

    ![Så här migrerar du från Hub](media/how-to/how-to-migrate-from-hub.png)

4. När du trycker på länken för migrera kommer du att presenteras med ett formulär som gör att du kan:
   * Ange den arbetsyta som du vill överföra till på Anpassad översättare
   * Ange om du vill överföra alla utbildningar med framgångsrika utbildningar eller bara de utplacerade utbildningarna. Som standard kommer alla lyckade utbildningar att överföras.
   * Ange om du vill att din automatiska träning ska distribueras när utbildningen är klar. Som standard kommer din utbildning inte att distribueras automatiskt när den är klar.

5. Klicka på "Skicka begäran".

## <a name="migrate-a-workspace"></a>Migrera en arbetsyta

Förutom att migrera ett enskilt projekt kan du också migrera alla projekt med framgångsrika utbildningar på en arbetsyta. Detta gör att varje projekt på arbetsytan utvärderas som om migrerad länken hade tryckts ned. Den här funktionen är lämplig för användare med många projekt som vill migrera dem alla till Custom Translator med samma inställningar. En migrering på arbetsytan kan initieras från inställningssidan i Translator Hub.

Så här migrerar du en arbetsyta:

1. Logga in på Microsoft Translator Hub.

2. Gå till sidan "Inställningar".

3. På sidan Inställningar klickar du på "Migrera arbetsytans data till anpassad översättare".

    ![Så här migrerar du från Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. På nästa sida väljer du något av följande två alternativ:

    a. Endast distribuerade utbildningar: Om du väljer det här alternativet migreras bara dina distribuerade system och relaterade dokument.

    b. Alla lyckade utbildningar: Om du väljer det här alternativet migreras alla dina lyckade utbildningar och relaterade dokument.

    c. Ange ditt målarbetsyte-ID i Anpassad översättare.

    ![Så här migrerar du från Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klicka på Skicka begäran.

## <a name="migration-history"></a>Flyttningshistorik

När du har begärt migrering av arbetsyte/projekt från Hub hittar du din migreringshistorik på sidan Anpassade översättareinställningar.

Så här visar du migreringshistoriken:

1. Gå till sidan "Inställning" i portalen Anpassad översättare.

2. Klicka på Migreringshistorik i avsnittet Migreringshistorik på sidan Inställningar.

    ![Flyttningshistorik](media/how-to/how-to-migration-history.png)

Sidan Migreringshistorik visar följande information som sammanfattning för varje migrering du begärde.

1. Migrerad efter: Namn och e-post för den användare som skickat den här migreringsbegäran

2. Migrerad på: Datum- och tidsstämpel för migreringen

3. Projekt: Antal projekt som begärts för migrering v/s antal projekt har migrerats.

4. Utbildningar: Antal utbildningar som begärts för migrering v/s antal utbildningar har migrerats.

5. Dokument: Antalet dokument som begärs för migrering v/s antal dokument har migrerats.

    ![Information om migreringshistorik](media/how-to/how-to-migration-history-details.png)

Om du vill ha en mer detaljerad migreringsrapport om dina projekt, utbildningar och dokument har du möjlighet att exportera information som CSV.

## <a name="implementation-notes"></a>Implementeringsanteckningar
* System med språkpar SOM ÄNNU INTE är tillgängliga i Custom Translator kommer endast att vara tillgängliga för åtkomst till data eller odeploy via Custom Translator. Dessa projekt markeras som "Ej tillgängliga" på sidan Projekt. När vi möjliggör nya språkpar med Custom Translator kommer projekten att bli aktiva för att träna och distribuera. 
* Att migrera ett projekt från Hub till Custom Translator påverkar inte dina hubbutbildningar eller hubbprojekt. Vi tar inte bort projekt eller dokument från Hub under en migrering och vi tar inte bort distribuerande modeller.
* Du får bara migrera en gång per projekt. Om du behöver upprepa en migrering på ett projekt, vänligen kontakta oss.
* Custom Translator stöder NMT-språkpar till och från engelska. [Visa hela listan över språk som stöds](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Hubben kräver inte baslinjemodeller och stöder därför flera tusen språk. Du kan migrera ett språkpar som inte stöds, men vi utför bara migreringen av dokument och projektdefinitioner. Vi kommer inte att kunna träna den nya modellen. Dessutom visas dessa dokument och projekt som inaktiva för att ange att de inte kan användas just nu. Om stöd läggs till för dessa projekt och/eller dokument kommer de att bli aktiva och tågbara.
* Custom Translator stöder för närvarande inte enspråkiga utbildningsdata. Precis som språkpar som inte stöds kan du migrera enspråkiga dokument, men de visas som inaktiva tills enspråkiga data stöds.
* Custom Translator kräver 10k parallella meningar för att träna. Microsoft Hub kan träna på en mindre uppsättning data. Om en utbildning migreras som inte uppfyller detta krav kommer den inte att utbildas.

## <a name="custom-translator-versus-hub"></a>Anpassad översättare kontra hubb

I den här tabellen jämförs funktionerna mellan Microsoft Translator Hub och Custom Translator.

|   | Hubb | Custom Translator |
|:-----|:----:|:----:|
|Status för anpassningsfunktionen   | Allmän tillgänglighet  | Allmän tillgänglighet |
| Text-API-version  | V2    | V3 (på andra)  |
| SMT-anpassning | Ja   | Inga |
| NMT-anpassning | Inga    | Ja |
| Ny anpassning av enhetliga taltjänster | Inga    | Ja |
| Inga spår | Ja | Ja |

## <a name="new-languages"></a>Nya språk

Om du är en community eller organisation som arbetar med att [custommt@microsoft.com](mailto:custommt@microsoft.com) skapa ett nytt språksystem för Microsoft Translator kan du kontakta dig för mer information.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md).
- Börja använda din distribuerade anpassade översättningsmodell via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
