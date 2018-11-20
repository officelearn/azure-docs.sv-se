---
title: Migrera Microsoft Translator Hub arbetsyta och projekt? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: Migrera din hubb arbetsyta och projekt till anpassad Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 611cd0878e88d2e1c0a988f73b57e391c5a8551d
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975915"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Migrera Hub arbetsyta och projekt till anpassad Translator

Du kan migrera dina [Microsoft Translator Hub](https://hub.microsofttranslator.com/) arbetsyta och projekt till anpassad Translator. Migreringen börjar från hubben.


De här objekten migreras under processen:

1.  Definitioner för projektet.

2.  Definitionen för utbildning används för att skapa en ny modell-definition på anpassade Translator.

3.  Parallella program och enspråkig-filer som används i utbildningar kommer att migreras som nya dokument i anpassade Translator.

4.  Automatiskt skapade system test och justera data exporteras och skapat som nya dokument i anpassade Translator.

För alla distribuerade utbildningar alla anpassade Translator tränar utan kostnad. Du distribuerar dem manuellt.

>[!Note]
>För en utbildning ska lyckas, kräver anpassade Translator minst 10 000 extraherade meningar. För mindre antal extraherade meningar än den [föreslagna minst](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), anpassade Translator kan inte genomföra en utbildningar.

För alla lyckade utbildningar som inte har distribuerats, kommer de att migreras som utkast i anpassade Translator.

## <a name="find-custom-translator-workspace-id"></a>Hitta anpassade Translator arbetsyte-Id

Att migrera [Microsoft Translator Hub](https://hub.microsofttranslator.com/) arbetsytan som du behöver mål arbetsyte-Id i anpassade Translator. Målarbetsyta i anpassade Translator är där alla Hub arbetsytor och projekt ska migreras till.

Du hittar ditt mål för arbetsyte-Id på inställningssidan för anpassad Translator: 

1.  Gå till sidan ”inställningen” i anpassade Translator-portalen.

2.  Du hittar arbetsyte-Id i grundläggande Information.

    ![Så här hittar du mål arbetsyte-ID](media/how-to/how-to-find-destination-ws-id.png)

3. Håll ditt mål för arbetsyte-Id för att referera under migreringsprocessen.

## <a name="migrate-workspace"></a>Migrera arbetsyta

När du migrerar din fullständiga Hub-arbetsyta till anpassad translator, migreras dina projekt, dokument och utbildningar till anpassad translator. Före migreringen måste du välja om du vill migrera endast distribuerade utbildningar eller om du vill flytta alla dina lyckad utbildningar.

Att migrera en arbetsyta:

1.  Logga in till Microsoft Translator-hubb.

2.  Gå till sidan ”Inställningar”.

3.  Klicka på ”migrera arbetsytedata till anpassad Translator” på sidan ”Inställningar”.

    ![Hur du migrerar från hubben](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  På nästa sida väljer du något av dessa två alternativ:

    a.  Distribuerat utbildningar endast: det här alternativet kommer att migrera dina distribuerade system och relaterade dokument.

    b.  Alla lyckade utbildningar: Det här alternativet kommer att migrera alla lyckade utbildningar och relaterade dokument.

    c.  Ange ditt mål för arbetsyte-ID i anpassade Translator.

    ![Hur du migrerar från hubben](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Klicka på Skicka begäran.

## <a name="migrate-project"></a>Migrera projekt

Om du vill migrera dina projekt selektivt gör Microsoft Translator Hub som möjligt.

Att migrera ett projekt:

1.  Logga in till Microsoft Translator-hubb.

2.  Gå till sidan ”projekt”.

3.  Klicka på ”migrera”-länk till lämpligt projekt.

    ![Hur du migrerar från hubben](media/how-to/how-to-migrate-from-hub.png)

4.  På nästa sida väljer du något av dessa två alternativ:

    a.  Distribuerat utbildningar endast: det här alternativet kommer att migrera dina distribuerade system och relaterade dokument. 

    b.  Alla lyckade utbildningar: Det här alternativet kommer att migrera alla lyckade utbildningar och relaterade dokument.

    c.  Ange ditt mål för arbetsyte-ID i anpassade Translator.

    ![Hur du migrerar från hubben](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Klicka på ”Skicka begäran”.

## <a name="migration-history"></a>Migreringshistoriken

När du har begärt arbetsyta / project migrering från hubben, hittar du din migreringshistoriken inställningssidan för anpassad Translator. 

Följ dessa steg om du vill visa migreringshistoriken:

1.  Gå till sidan ”inställningen” i anpassade Translator-portalen.

2.  Klicka på Migreringshistoriken i avsnittet Migreringshistoriken på sidan Inställningar.

    ![Migreringshistoriken](media/how-to/how-to-migration-history.png)

Migrering historiksidan visar följande information som sammanfattning för varje migrering som du har begärt.

1.  Migrerade av: Namn och e-post för användaren skickat den här migreringsbegäran om

2.  Migrerade på: Datum och tid stämpel för migreringen

3.  Projekt: Antalet projekt som begärdes för en migrering v/s antal projekt har migrerats.

4.  Utbildningar: Antal utbildningar som begärdes för en migrering v/s antalet utbildningar migrerats.

5.  Dokument: Antal dokument som krävs för migrering v/s antal dokument som har migrerats.

    ![Historikinformation för migrering](media/how-to/how-to-migration-history-details.png)

Om du vill ha mer detaljerad migreringsrapport om ditt projekt, utbildningar och dokument, har du alternativet Exportera information som CSV.

>[!Note]
>Migrering stöds bara för språkpar där NMT språk finns. Kontrollera listan över för närvarande [NMT språk som stöds](https://www.microsoft.com/translator/business/languages/). Data kommer att flyttas från hubben till anpassad Translator för språkpar där NMT språk inte finns, men utbildningar kan inte utföras på dessa språkpar.

## <a name="custom-translator-versus-hub"></a>Anpassade Translator jämfört med Hub

Den här tabellen jämförs funktionerna mellan Microsoft Translator Hub och anpassade Translator.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Anpassning av funktionsstatus   | Allmän tillgänglighet  | Förhandsversion |
| API för textöversättning version  | V2    | V3  |
| SMT anpassning | Ja   | Nej |
| NMT anpassning | Nej    | Ja |
| Nya enhetliga Speech services anpassning | Nej    | Ja |
| Ingen spårning | Ja | Ja |

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md).
- Börja använda dina distribuerade anpassade översättningsmodellen via [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
