---
title: Cortana Intelligence Gallery experimenten | Microsoft Docs
description: Identifiera och dela experiment i Cortana Intelligence Gallery.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: f4248922-c961-4d3a-9e1b-aec743210166
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: roopalik;garye
ms.openlocfilehash: 472ecac4ac1d9a86f0a3ad8271dd96257c331e5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="discover-experiments-in-cortana-intelligence-gallery"></a>Identifiera experiment i Cortana Intelligence Gallery
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="experiments-for-machine-learning-studio"></a>Experiment för Machine Learning Studio
Galleriet har ett stort antal [experiment](https://gallery.cortanaintelligence.com/experiments) som har utvecklats i [Azure Machine Learning Studio](https://studio.azureml.net). Experiment mellan snabb proof of concept experiment som visar specifik machine learning-teknik som gör att fullständigt utvecklade lösningar för komplexa machine learning problem.

> [!NOTE]
> En ***experimentera*** är en arbetsyta i Machine Learning Studio som du kan använda för att skapa en prediktiv analysmodell. Du kan skapa modellen genom att ansluta data med olika analytiska moduler. Du kan prova olika idéer gör utvärderingsversion körs och slutligen distribuera din modell som en webbtjänst i Azure. Ett exempel på hur du skapar en grundläggande experiment finns [självstudie om maskininlärning: skapa ditt första experiment i Azure Machine Learning Studio](create-experiment.md). En mer omfattande genomgång av hur du skapar en förutsägelseanalys finns [genomgång: utveckla en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning](walkthrough-develop-predictive-solution.md).
>
>

## <a name="discover"></a>Utforska
Att bläddra bland experiment [i galleriet](http://gallery.cortanaintelligence.com), markera överst på sidan galleri **experiment**.

Den  **[experiment](https://gallery.cortanaintelligence.com/experiments)**  sidan visas en lista över nyligen tillagda och populära experiment. Om du vill se alla experiment, Välj den **se alla** knappen. Om du vill söka efter en specifik experiment, Välj **se alla**, och välj sedan filtreringsvillkor. Du kan även ange sökorden i den **Sök** rutan längst upp på sidan Gallery.

Du kan få mer information om ett experiment på informationssidan experiment. Om du vill öppna ett experiment sidan Välj experimentet. På ett experiment information sidan den **kommentarer** avsnitt, du kan kommentera, ge feedback eller ställa frågor om experimentet. Du kan också dela experimentet med vänner och kollegor på Twitter och LinkedIn. Du kan också skicka en länk till sidan experiment information att bjuda in andra användare att visa sidan.

![Dela objektet med vänner](./media/gallery-how-to-use-contribute-publish/share-links.png)

![Lägga till egna kommentarer](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="download"></a>Ladda ned
Du kan hämta en kopia av alla experiment i galleriet till Machine Learning Studio-arbetsytan. Sedan kan du ändra din kopia om du vill skapa egna lösningar.

Cortana Intelligence Gallery finns två sätt att importera en kopia av ett experiment:

* **Från galleriet**. Om du hittar ett experiment i galleriet kan du hämta en kopia och öppna den i Machine Learning Studio-arbetsytan.
* **Inifrån Maskininlärning Studio**. I Machine Learning Studio, kan du använda alla experiment i galleriet som en mall för att skapa ett nytt experiment.

### <a name="from-the-gallery"></a>Från galleriet

1. Öppna sidan experiment i galleriet.
2. Välj **öppna i Studio**.

    ![Öppna experiment från galleriet](./media/gallery-experiments/open-experiment-from-gallery.png)

När du väljer **öppna i Studio**, experimentet öppnas i Machine Learning Studio-arbetsytan. (Om du inte redan är inloggad till Machine Learning Studio, du uppmanas att först logga in med ditt Microsoft-konto.)

### <a name="from-within-machine-learning-studio"></a>Inifrån Maskininlärning Studio

1. Välj i Machine Learning Studio **ny**.
2. Välj **Experiment**. Du kan välja från en lista över galleriet experiment eller söka efter en specifik experiment med den **Sök** rutan.
3. Peka med musen på ett experiment och välj sedan **öppna i Studio**. (Om du vill se information om experimentet väljer **vyn i galleriet**. Då kommer du att informationssidan experiment i galleriet.)

    ![Öppna galleriet experiment från i Machine Learning Studio](./media/gallery-experiments/open-experiment-from-studio.png)

Du kan anpassa, iterera och distribuera ett hämtade experiment som andra experiment som du skapar i Machine Learning Studio.

![Experiment som öppnas i Studio](./media/gallery-experiments/experiment-open-in-studio.png)

## <a name="contribute"></a>Bidra
När du loggar in i galleriet blir medlem i galleri. Du kan bidra egna experiment, så att andra användare kan dra nytta av de lösningar som du har identifierats som en medlem i gruppen.

### <a name="publish-your-experiment-to-the-gallery"></a>Publicera ditt experiment i galleriet

1. Logga in på Machine Learning Studio med hjälp av ditt Microsoft-konto.
2. Skapa experimentet och sedan köra den.
3. När du är redo att publicera ditt experiment i galleriet i listan över åtgärder under arbetsytan för experimentet väljer **publicera Gallery**.

    ![Välj ”publicera galleriet”](./media/gallery-experiments/publish-experiment-to-gallery.png)
4. På den **Experiment beskrivning** anger du en rubrik och taggar. Kontrollera namnet och taggar beskrivande. Markera de tekniker som du använde eller verkliga problemet du lösa. Ett exempel på en beskrivande experiment rubrik är ”binär klassificering: Twitter-Sentiment Analysis”.

    ![Ange rubrik och taggar för publicering](./media/gallery-experiments/experiment-description.png)
5. I den **sammanfattning** anger du en sammanfattning av experimentet. Beskriv experimentet löste problemet och hur du närmat sig.
6. I den **detaljerad beskrivning** rutan, beskriver hur du gjorde i varje del av experimentet. Vissa användbara avsnitt för att inkludera är:
   * Skärmbild av experimentet diagram
   * Förklaring och datakällor
   * Databearbetning
   * Funktionstekniker
   * Beskrivning
   * Resultat och utvärdering av modellen prestanda

   Du kan använda markdown för att formatera din beskrivning. Om du vill se hur posterna på experimentet beskrivningssida kommer när experimentet publiceras, Välj **Preview**.

   ![Välj ”förhandsversion” till att granska texten](./media/gallery-experiments/preview-markdown-text.png)

   > [!TIP]
   > Fälten för markdown redigering och förhandsgranskning är små. Vi rekommenderar att du skriva dokumentationen experiment i en markdown-redigerare, kopiera den och sedan klistra in den slutförda dokumentationen i textrutan i galleriet. När du har publicerat experimentet du eventuella ändringar med hjälp av standard webbaserade verktyg att använda markdown för redigering och förhandsgranskning.

7. På den **valet** väljer du miniatyrbilden för experimentet. På miniatyrbilden visas överst på sidan experiment information och i panelen experiment. Andra användare ser på miniatyrbilden när de söker i galleriet. Du kan ladda upp en bild från datorn eller välj en bild från galleriet.
    </br>
    ![Ladda upp eller välj en bild för galleriet](./media/gallery-experiments/select-gallery-image.png)
8. På den **inställningar** sidan under **synlighet**, Välj om du vill publicera innehållet offentligt (**offentliga**) eller att den är bara tillgängliga för användare som har en länk till sidan ( **Olistade**).

    ![Välj om du vill publicera offentligt eller som inte finns i listan](./media/gallery-experiments/choose-public-or-unlisted.png)

    <!-- -->

   > [!TIP]
   > Om du vill kontrollera dokumentationen verkar vara korrekta innan du släpper offentligt, kan du först publicera experiment som **nya**. Senare, du kan ändra inställningar för **offentliga** på informationssidan experiment.
   >
   >
9. Om du vill publicera experimentet i galleriet, Välj den **OK** är markerat.

    ![Välj kryssmarkeringen OK att publicera experimentet](./media/gallery-experiments/ok-checkmark.png)

Tips om hur du publicerar en högkvalitativ galleriet experiment finns [Tips för att dokumentera och publicering av experimentet](#tips-for-documenting-and-publishing-your-experiment).

Det är den - alla övningen.

Du kan nu visa ditt experiment i galleriet och dela länken med andra. Om du har publicerat experimentet genom att använda den **offentliga** synlighet inställningen experimentet visas i resultaten för Bläddra och Sök i galleriet. Du kan redigera experiment dokumentationen på sidan experiment information när du är inloggad i galleriet.

Om du vill se en lista över dina bidrag, markera avbildningen i övre högra hörnet på en gallerisida. Välj ditt namn för att öppna sidan för ditt konto.

![Välj namnet på ditt konto](./media/gallery-experiments/click-account-name.png)

### <a name="update-your-experiment"></a>Uppdatera experimentet
Om du behöver göra ändringar i arbetsflödet (moduler, parametrar och så vidare) i ett experiment som du har publicerat i galleriet. Gör alla ändringar som du vill göra till arbetsytan för experimentet och sedan publicera igen i Machine Learning Studio. Experimentet publicerade uppdateras med dina ändringar.

Du kan ändra följande information för experimentet direkt i galleriet:

* Namn på experimentet
* Sammanfattning eller beskrivning
* Taggar
* Bild
* Inställningar (**offentliga** eller **nya**)

Du kan också ta bort experimentet från galleriet.

Du kan göra dessa ändringar eller ta bort experiment, från sidan experiment information eller från din profilsida i galleriet.


#### <a name="from-the-experiment-details-page"></a>Från sidan experiment
På sidan experiment information om du vill ändra informationen för experimentet väljer **redigera**.

![Välj Redigera för att redigera experimentet](./media/gallery-experiments/edit-button.png)

Informationssidan försätts i redigeringsläge. Om du vill göra ändringar, Välj **redigera** bredvid experiment namn, sammanfattning eller taggar. När du är klar väljer **klar**.

![Välj ”Redigera” om du vill redigera detaljer och välj ”klar” när du är klar](./media/gallery-experiments/edit-details-page.png)

Ändra inställningarna för kolumnsynlighet för experimentet (**offentliga** eller **nya**), eller ta bort experimentet från galleriet genom att välja den **inställningar** ikon.

![Välj ”inställningar” för att ändra synlighet eller ta bort experimentet](./media/gallery-experiments/settings-button.png)

#### <a name="from-your-profile-page"></a>Från profilsidan
På profilsidan väljer du på nedpilen för experimentet och välj sedan **redigera**. Då kommer du till informationssidan för experimentet i redigeringsläge. När du är klar med ändringarna, Välj **klar**.

Om du vill ta bort experimentet från galleriet, Välj **ta bort**.

![Välj ”Redigera” eller ”ta bort”](./media/gallery-experiments/edit-delete-buttons.png)

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Tips för att dokumentera och publicering av experimentet
* Du kan anta att läsaren har tidigare datavetenskap upplevelse, men det kan vara bra att använda enkla språk. Innehåller en förklaring i detalj när det är möjligt.
* Cortana Intelligence Suite är relativt nytt. Inte alla läsare är bekanta med hur du använder den. Ge tillräckligt med information och detaljerade förklaringar för att kunna navigera experimentet.
* Visuell information kan vara användbart att tolka och använda dokumentationen experiment på rätt sätt. Visuell information inkluderar experimentdiagram och skärmdumpar av data. Mer information om hur du lägger till bilder i experimentet-dokumentationen finns det [publiceringsriktlinjer och exempel samling](https://gallery.cortanaintelligence.com/Collection/Publishing-Guidelines-and-Examples-1).
* Om du inkluderar en datamängd i experimentet (det vill säga du inte importerar datauppsättningen genom modulen importera Data), de uppgifter som är en del av experimentet och publiceras i galleriet. Se till att de uppgifter som du publicerar har licensvillkoren som tillåter delning och hämtning av vem som helst. Galleriet bidrag omfattas Azure [användningsvillkoren](https://azure.microsoft.com/support/legal/website-terms-of-use/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Vilka är kraven för att skicka eller redigerar en bild för mitt experiment?**

Bilder som du skickar med experimentet används för att skapa ett experiment panelen för ditt bidrag. Vi rekommenderar att bilder är mindre än 500 KB, med en aspect ratio av 3:2 och en upplösning på 960 &#215; 640.

**Vad händer med de data som angivits I i experimentet? Datauppsättningen också publiceras i galleriet?**

Om din datauppsättning är en del av experimentet och inte importeras via modulen importera Data, publiceras datauppsättningen i galleriet som en del av experimentet. Se till att den datamängd som du publicerar med experimentet har lämplig licensvillkoren. Licensvillkoren ska tillåta alla delar och hämta data.

**Jag har ett experiment som använder en importera Data-modul som hämtar data från Azure HDInsight eller SQL Server. Mina autentiseringsuppgifter används för att hämta data. Kan jag publicera den här typen av experimentet? Hur kan jag vara säker på att mina autentiseringsuppgifter inte delas?**

Du kan för närvarande inte publicera ett experiment som använder autentiseringsuppgifterna i galleriet.

**Hur anger flera taggar?**

Tryck på tabbtangenten när du anger en tagg för att ange en annan tagg.

**[Gå till galleriet](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
