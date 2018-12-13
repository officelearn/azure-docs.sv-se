---
title: Azure AI-galleriet experiment – Azure Machine Learning Studio | Microsoft Docs
description: Upptäcka och dela experiment i Azure AI-galleriet. Ett experiment är en arbetsyta i Machine Learning Studio som du kan använda för att skapa en prediktiv analysmodell
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.openlocfilehash: 12fb45b00848e5e34760ccb0691998c1f72237b8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269309"
---
# <a name="discover-azure-machine-learning-studio-experiments-in-azure-ai-gallery"></a>Identifiera Azure Machine Learning Studio-experiment i Azure AI-galleriet

[Azure AI-galleriet](http://gallery.cortanaintelligence.com) har olika [experiment](https://gallery.cortanaintelligence.com/experiments) som har utvecklats i [Azure Machine Learning Studio](https://studio.azureml.net). Experiment sträcker sig från snabb proof of concept-experiment som visar en specifik machine learning-teknik för att fullständigt har utvecklat lösningar för problem med komplexa machine learning.

> [!NOTE]
> En ***experimentera*** är en arbetsyta i Machine Learning Studio som du kan använda för att skapa en prediktiv analysmodell. Du kan skapa modellen genom att ansluta data med olika analytiska moduler. Du kan prova olika idéer gör testkörningar och slutligen distribuerar din modell som en webbtjänst i Azure. Ett exempel på hur du skapar en grundläggande experiment finns i [självstudie om maskininlärning: Skapa ditt första experiment i Azure Machine Learning Studio](create-experiment.md). En mer omfattande genomgång av hur du skapar en lösning för förutsägelseanalys, se [genomgång: Utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Utforska
Att bläddra bland experiment [i galleriet](http://gallery.cortanaintelligence.com), överst på sidan galleri väljer **experiment**.

Den **[experiment](https://gallery.cortanaintelligence.com/experiments)** sidan visar en lista över nyligen har lagts till och populära experiment. Om du vill se alla experiment, Välj den **se alla** knappen. För att söka efter en viss experiment, Välj **se alla**, och välj sedan filtreringsvillkor. Du kan även ange sökorden i den **Search** rutan längst upp på sidan för galleriet.

Du kan få mer information om ett experiment på sidan med experiment. Välj experimentet för att öppna ett experiment informationssida. På ett experiment information sidan den **kommentarer** avsnitt, du kan kommentera, ge feedback eller ställa frågor om experimentet. Du kan också dela experimentet med vänner och kollegor på Twitter och LinkedIn. Du kan också skicka en länk till sidan experiment, bjuda in andra användare att visa sidan.

![Dela det här objektet med vänner](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Lägga till egna kommentarer](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Ladda ned
Du kan hämta en kopia av alla experiment i galleriet till din Machine Learning Studio-arbetsyta. Sedan kan du ändra din kopia om du vill skapa egna lösningar.

Azure AI-galleriet erbjuder två sätt att importera en kopia av ett experiment:

* **Från galleriet**. Om du hittar ett experiment som helst i galleriet kan du ladda ned en kopia och öppna den i din Machine Learning Studio-arbetsyta.
* **Machine Learning Studio inifrån**. I Machine Learning Studio, kan du använda alla experiment i galleriet som mall för att skapa ett nytt experiment.

### <a name="from-the-gallery"></a>Från galleriet

1. Öppna sidan experiment i galleriet.
2. Välj **öppna i Studio**.

    ![Öppna experiment från galleriet](./media/gallery-experiments/open-experiment-from-gallery.png)

När du väljer **öppna i Studio**, experimentet öppnas i Machine Learning Studio-arbetsyta. (Om du inte redan är inloggad till Machine Learning Studio, uppmanas du att loggar in första med hjälp av ditt Microsoft-konto.)

### <a name="from-within-machine-learning-studio"></a>Inifrån Machine Learning Studio

1. Välj i Machine Learning Studio, **NEW**.
2. Välj **Experiment**. Du kan välja från en lista över experiment i galleriet eller hitta ett specifikt experiment genom att använda den **Search** box.
3. Peka med musen på ett experiment och välj sedan **öppna i Studio**. (Om du vill se information om experimentet **vyn i galleriet**. Då kommer du till sidan experiment i galleriet.)

    ![Öppna galleriet experimentera från i Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

Du kan anpassa, iterera och distribuera en hämtade experiment som andra experiment som du skapar i Machine Learning Studio.

![Experiment som öppnas i Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Bidra
När du loggar in i galleriet, bli medlem i galleri. Du kan bidra dina egna experiment, så att andra användare kan dra nytta av de lösningar som du har identifierats som en medlem i gruppen.

### <a name="publish-your-experiment-to-the-gallery"></a>Publicera ditt experiment i galleriet

1. Logga in på Machine Learning Studio med hjälp av ditt Microsoft-konto.
2. Skapa ditt experiment och sedan köra den.
3. När du är redo att publicera ditt experiment i galleriet, i listan över åtgärder under arbetsytan för experimentet väljer **publicera på galleriet**.

    ![Välj ”publicera till galleriet”](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. På den **Experiment beskrivning** anger en rubrik och taggar. Gör rubriken och taggar beskrivande. Markera de tekniker som du använde eller verkliga problemet som du vill lösa. Ett exempel på en beskrivande experiment rubrik är ”binär klassificering: Twitter-Attitydanalys ”.

    ![Ange rubrik och taggar för publicering](./media/gallery-experiments/experiment-description.png)
5. I den **sammanfattning** anger du en sammanfattning av experimentet. Beskriv experimentet löste problemet och hur du närmat sig.
6. I den **detaljerad beskrivning** rutan, beskriver de steg som du har gjort i varje del av experimentet. Vissa användbara avsnitt att inkludera är:
   * Skärmbild av experimentet graph
   * Datakällor och förklaring
   * Databearbetning
   * Funktionstekniker
   * Modellbeskrivning
   * Resultat och utvärdering av modellprestanda

   Du kan använda markdown för att formatera en beskrivning. Om du vill se hur dina poster i experimentet beskrivningssida ser ut när experimentet har publicerats, **förhandsversion**.

   ![Välj ”förhandsversion” för att se hur texten ser ut](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Textrutorna för markdown-redigering och förhandsgranskning är små. Vi rekommenderar att du skriva din experiment dokumentation i en markdown-redigerare, kopiera den och sedan klistra in den slutförda dokumentationen i textrutan i galleriet. När du har publicerat ditt experiment du eventuella ändringar med hjälp av standard webbaserade verktyg att använda markdown för att redigera och förhandsgranska.

7. På den **valet av** väljer du en miniatyrbild för experimentet. På miniatyrbilden visas överst på sidan med experiment och i panelen experiment. Andra användare ser på miniatyrbilden när de söker i galleriet. Du kan ladda upp en bild från datorn eller välj en bild från galleriet.
    </br>
    ![Ladda upp eller välj en avbildning för galleriet](./media/gallery-experiments/select-gallery-image.png)
8. På den **inställningar** sidan under **synlighet**, Välj om du vill publicera innehållet offentligt (**offentliga**) eller att den endast är tillgängligt för personer som har en länk till sidan ( **Olistade**).

    ![Välj om du vill publicera offentligt eller som inte finns i listan](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Om du vill kontrollera att din dokumentation ser korrekt ut innan du släpper offentligt du först publicera experiment som **nya**. Senare, du kan ändra synlighetsinställningen till **offentliga** på sidan med experiment.
   >
   >
9. Om du vill publicera experimentet i galleriet, Välj den **OK** kryssmarkeringen.

    ![Välj kryssmarkeringen OK att publicera experimentet](./media/gallery-experiments/ok-checkmark.png)

Tips om hur du publicerar en högkvalitativ galleriet experiment finns [Tips för att dokumentera och publicera ditt experiment](#tips-for-documenting-and-publishing-your-experiment).

Det var allt – är du klar.

Du kan nu visa ditt experiment i galleriet och dela länken med andra. Om du har publicerat ditt experiment med hjälp av den **offentliga** synlighet inställningen experimentet som visas i Bläddra och om sökresultaten i galleriet. Du kan redigera experimentet dokumentationen på sidan med experiment som helst som du har loggat in i galleriet.

Om du vill se en lista över dina bidrag, väljer du din avbildning i det övre högra hörnet på varje gallerisida. Välj namnet för att öppna din kontosida.

![Välj namnet på ditt konto](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Uppdatera experimentet
Om du vill kan göra du ändringar i arbetsflödet (moduler, parametrar och så vidare) i ett experiment som du har publicerat i galleriet. Gör alla ändringar som du vill göra till arbetsytan för experimentet och sedan publicera igen i Machine Learning Studio. Ditt publicerade experiment kommer att uppdateras med ändringarna.

Du kan ändra följande information för experimentet direkt i galleriet:

* Namn på experiment
* Sammanfattning eller beskrivning
* Taggar
* Bild
* Inställningar (**offentliga** eller **nya**)

Du kan också ta bort experimentet från galleriet.

Du kan göra dessa ändringar eller ta bort du experimentet, från sidan experiment eller från din profilsida i galleriet.


#### <a name="from-the-experiment-details-page"></a>Från sidan experiment
På sidan med experiment, om du vill ändra information om ditt experiment, Välj **redigera**.

![Välj Redigera så här redigerar du experimentet](./media/gallery-experiments/edit-button.png)

Sidan försätts i redigeringsläge. Om du vill göra ändringar, Välj **redigera** bredvid experiment namn, sammanfattning eller taggar. När du är klar med ändringarna väljer **klar**.

![Välj ”Redigera” om du vill redigera information och välj ”klar” när du är klar](./media/gallery-experiments/edit-details-page.png)

Ändra inställningarna för kolumnsynlighet för experimentet (**offentliga** eller **nya**), eller om du vill ta bort experimentet från galleriet, Välj den **inställningar** ikon.

![Välj ”inställningar” ändra synlighet eller ta bort experimentet](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Från din profilsida
Välj nedpilen för experimentet på din profilsida och välj sedan **redigera**. Detta tar dig till sidan för ditt experiment i redigeringsläge. När du är klar med ändringarna, Välj **klar**.

Om du vill ta bort experimentet från galleriet, Välj **ta bort**.

![Välj ”Redigera” eller ”ta bort”](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Tips för att dokumentera och publicera ditt experiment
* Du kan anta att läsaren har tidigare datavetenskap upplevelse, men det kan vara bra att använda enkla språk. Innehåller en förklaring i detalj när det är möjligt.
* Cortana Intelligence Suite är relativt nytt. Inte alla läsare är bekanta med hur du använder den. Ge tillräckligt med information och detaljerade förklaringar för att kunna gå experimentet.
* Visuella objekt kan vara användbart att tolka och använda dokumentationen experiment på rätt sätt. Visuella objekt är experimentdiagram och skärmdumpar av data. Mer information om hur du inkludera bilder i dokumentationen till din experiment finns i den [publiceringsriktlinjer och exempel samling](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Om du inkluderar en uppsättning data i ditt experiment (det vill säga du inte importerar datauppsättningen genom modulen importera Data), datauppsättningen är en del av ditt experiment och publiceras i galleriet. Kontrollera att datauppsättningen som du publicerar har licensvillkor som tillåter delning och hämtning av vem som helst. Bidrag till galleriet ingår i Azure [användningsvillkor](https://azure.microsoft.com/support/legal/website-terms-of-use/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Vilka är kraven för att skicka in eller redigera en bild för mitt experiment?**

Bilder som du skickar med experimentet används för att skapa ett experiment panelen för ditt bidrag. Vi rekommenderar att avbildningar är mindre än 500 KB med ett aspect ratio av 3:2 och en upplösning på 960 &#215; 640.

**Vad händer med den datauppsättning som jag använde i experimentet? Publiceras datauppsättningen också i galleriet?**

Om din datauppsättning är en del av ditt experiment och inte importeras via modulen importera Data, publiceras datauppsättningen i galleriet som en del av experimentet. Se till att den datauppsättning som du publicerar med experimentet har lämpliga licensvillkoren. Licensvillkoren ska tillåta vem som helst kan dela och hämta data.

**Jag har ett experiment som använder en modul för importera Data att hämta data från Azure HDInsight eller SQL Server. Mina autentiseringsuppgifter används för att hämta data. Kan jag publicera den här typen av experimentet? Hur kan jag vara säker på att mina autentiseringsuppgifter inte delas?**

För närvarande kan publicera du inte ett experiment som använder autentiseringsuppgifterna i galleriet.

**Hur anger jag flera taggar?**

När du anger en tagg för att ange en annan tagg, trycker du på TABB-tangenten.

**[Gå till galleriet](http://gallery.cortanaintelligence.com)**


