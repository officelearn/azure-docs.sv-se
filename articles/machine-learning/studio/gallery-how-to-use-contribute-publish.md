---
title: Azure AI-galleriet
titleSuffix: ML Studio (classic) - Azure
description: Dela och upptäck analysresurser med mera i Azure AI Gallery. Lär av andra och gör dina egna bidrag till samhället.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/11/2019
ms.openlocfilehash: 882b52b74603465bd0a55a12fd9ef03f7b625046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204332"
---
# <a name="share-and-discover-resources-in-the-azure-ai-gallery"></a>Dela och identifiera resurser i Azure AI Gallery

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

**[Azure AI Gallery](https://gallery.azure.ai)** är en community-driven webbplats för att upptäcka och dela lösningar som skapats med Azure AI.
Galleriet har en mängd olika resurser som du kan använda för att utveckla dina egna analyslösningar.

## <a name="what-can-i-find-in-the-gallery"></a>Vad hittar jag i galleriet?

Azure AI Gallery innehåller ett antal olika resurser som har bidragit från Microsoft och medlemmar i datavetenskapsgruppen. Exempel på dessa är:

* **Experiment** – Galleriet innehåller en mängd olika experiment som har utvecklats i Azure Machine Learning Studio (klassisk). Dessa sträcker sig från snabba proof-of-concept-experiment som demonstrerar en specifik maskininlärningsteknik till fullt utvecklade lösningar för komplexa maskininlärningsproblem.
* **Självstudier** - Ett antal självstudier finns tillgängliga för att gå igenom maskininlärningsteknik och -koncept, eller för att beskriva avancerade metoder för att lösa olika maskininlärningsproblem.
* **Samlingar** – En samling kan du gruppera experiment, API:er och andra galleriresurser som behandlar en viss lösning eller ett visst koncept.
* **Anpassade moduler** - Du kan ladda ner anpassade moduler till din Studio (klassiska) arbetsyta att använda i dina egna experiment.
* **Jupyter Bärbara datorer** - Jupyter bärbara datorer innehåller kod, datavisualiseringar och dokumentation i en enda, interaktiv duk. Anteckningsböcker i galleriet innehåller självstudier och detaljerade förklaringar av avancerade maskininlärningstekniker och lösningar.

## <a name="discover-and-contribute"></a>Upptäck och bidra

Vem som helst kan bläddra bland och söka i de olika typer av resurser i galleriet som har bidragit från Microsoft och datascience-communityn.
Använd dessa resurser för att lära dig mer och få ett försprång när det gäller att lösa dina egna dataanalysproblem.

Du kan enkelt hitta nyligen publicerade och populära resurser i galleriet, eller så kan du söka efter namn, taggar, algoritmer och andra attribut.
Klicka på **Bläddra bland alla** i gallerihuvudet och välj sedan sökförbättringar till vänster på sidan och ange söktermer högst upp.
Visa bidrag från en viss författare genom att klicka på författarens namn från någon av panelerna.
Du kan kommentera, ge feedback eller ställa frågor via kommentarsfältet på varje resurssida.
Du kan till och med dela en resurs av intresse med vänner eller kollegor med hjälp av delningsfunktionerna i LinkedIn eller Twitter, eller genom att skicka en länk via e-post.

När du loggar in blir du medlem i gallericommunityn. På så sätt kan du hämta resurser eller bidra med egna galleriobjekt så att andra kan dra nytta av de lösningar du har upptäckt.

Du kan hämta **experiment,** **anpassade moduler**och **Jupyter-anteckningsböcker** som du kan använda för att utveckla dina egna analyslösningar.
Du kan bidra **med experiment,** **självstudier**och **samlingar** till galleriet.

## <a name="download-experiments-modules-notebooks"></a>Ladda ner experiment, moduler, anteckningsböcker

Du kan ladda ner **experiment,** **anpassade moduler**och **Jupyter-anteckningsböcker** till din egen Machine Learning Studio (klassiska) arbetsyta som du kan använda för att utveckla dina egna lösningar.

Så här hämtar du en resurs från AI-galleriet:

1. Öppna resursen i AI Gallery.
1. Klicka på **Öppna i Studio**.

![Öppna ett objekt från AI-galleriet](./media/gallery-how-to-use-contribute-publish/open-experiment-from-gallery.png)

Så här hämtar du en resurs inifrån Studio (klassisk):

1. I Studio (klassisk), välj **NY**.
1. Välj **Modul,** **Experiment**eller **Anteckningsbok**.
1. Bläddra eller sök för att hitta en galleriresurs.
1. Peka musen mot resursen och välj sedan **Öppna i Studio**.
    ![Öppna galleriexperiment inifrån Machine Learning Studio (klassiskt)](./media/gallery-how-to-use-contribute-publish/open-experiment-from-studio.png)

När resursen är i din arbetsyta kan du anpassa och använda den som du skulle något som du skapar i Studio (klassisk).

Så här använder du en importerad anpassad modul:

1. Skapa ett experiment eller öppna ett befintligt experiment.
1. Om du vill expandera listan över anpassade moduler på arbetsytan väljer du **Anpassad**i modulpaletten . Modulpaletten är till vänster om experimentduken.
1. Markera den modul som du importerade och dra den till experimentet.

## <a name="contribute-experiments"></a>Contribute-experiment

Om du vill demonstrera analystekniker eller ge andra en rivstart på deras lösningar kan du bidra med **experiment** som du har utvecklat i Studio (klassisk).
När andra stöter på ditt bidrag i galleriet kan du följa antalet visningar och nedladdningar av ditt bidrag.
Användare kan också lägga till kommentarer och dela dina bidrag med andra medlemmar i datascience-communityn.
Och du kan logga in med ett diskussionsverktyg som Disqus för att få meddelanden för kommentarer om dina bidrag.

1. Öppna experimentet i Studio (klassiskt).

1. Välj Publicera i **galleri**i listan över åtgärder under arbetsytan .

1. Ange ett **namn** och **taggar** som är beskrivande i galleriet. Markera de tekniker du använde eller det verkliga problemet du löser. Ett exempel på en beskrivande experimenttitel är "Binary Classification: Twitter Sentiment Analysis".

1. I rutan **SAMMANFATTNING** anger du en sammanfattning av experimentet. Beskriv kortfattat problemet som experimentet löser och hur du närmade dig det.

1. Beskriv de steg du tog i varje del av experimentet i rutan **DETALJERAD BESKRIVNING.** Några användbara ämnen att ta med är:
   * Experiment diagram skärmdump
   * Datakällor och förklaring
   * Databearbetning
   * Funktionstekniker
   * Modellbeskrivning
   * Resultat och utvärdering av modellens prestanda

   Du kan använda markdown för att formatera beskrivningen. Om du vill se hur dina poster på experimentbeskrivningssidan kommer att se ut när experimentet publiceras väljer du **Förhandsgranska**.

   > [!TIP]
   > Textrutorna som tillhandahålls för markeringsredigering och förhandsgranskning är små. Vi rekommenderar att du skriver experimentdokumentationen i en markdown-redigerare (till exempel [Visual Studio-kod)](https://aka.ms/vscode)och sedan kopierar och klistrar in den färdiga dokumentationen i textrutan i galleriet.

1. På sidan **Bildval** väljer du en miniatyrbild för experimentet. Miniatyrbilden visas högst upp på experimentinformationssidan och i experimentpanelen. Andra användare ser miniatyrbilden när de bläddrar i galleriet. Du kan ladda upp en bild från datorn eller välja en arkivbild i galleriet.

1. På sidan **Inställningar** under **Synlighet**väljer du om du vill publicera ditt innehåll offentligt (**Offentlig**) eller om det bara ska vara tillgängligt för personer som har en länk till sidan (**Olistat**).

   > [!TIP]
   > Om du vill vara säker på att dokumentationen ser korrekt ut innan du släpper den offentligt kan du först publicera experimentet som **olistad**. Senare kan du ändra synlighetsinställningen till **Offentlig** på experimentinformationssidan. Observera att när du har angett ett experiment **till Offentlig** kan du inte senare ändra det till **Olistad**.

1. Om du vill publicera experimentet **OK** i galleriet markerar du OK-bocken.

### <a name="update-your-experiment"></a>Uppdatera experimentet

Om du behöver kan du göra ändringar i arbetsflödet (moduler, parametrar och så vidare) i ett experiment som du publicerade i galleriet. I Machine Learning Studio (klassisk) gör du alla ändringar du vill göra i experimentet och publicerar sedan igen. Det publicerade experimentet uppdateras med dina ändringar.

Du kan ändra någon av följande information för experimentet direkt i galleriet:

* Experimentnamn
* Sammanfattning eller beskrivning
* Taggar
* Bild
* Synlighetsinställning (**Offentlig** eller **Ej listad)**

Du kan också ta bort experimentet från galleriet.

Du kan göra dessa ändringar eller ta bort experimentet från experimentinformationssidan eller från profilsidan i galleriet.

* Om du vill ändra information om experimentet på sidan experimentinformation väljer du **Redigera**. Informationssidan går in i redigeringsläge. Om du vill göra ändringar väljer du **Redigera bredvid** experimentnamnet, sammanfattningen eller taggarna. När du är klar med ändringarna väljer du **Klar**. Om du vill ändra synlighetsinställningarna för experimentet (**Offentlig** eller **Ej listad**) eller ta bort experimentet från galleriet väljer du ikonen **Inställningar.**

* På profilsidan väljer du nedpilen för experimentet och väljer sedan **Redigera**. Detta tar dig till informationssidan för experimentet, i redigeringsläge. När du är klar med ändringarna väljer du **Klar**. Om du vill ta bort experimentet från galleriet väljer du **Ta bort**.

### <a name="tips-for-documenting-and-publishing-your-experiment"></a>Tips för att dokumentera och publicera experimentet

* Du kan anta att läsaren har tidigare datavetenskap erfarenhet, men det kan vara bra att använda enkelt språk. Förklara saker i detalj när det är möjligt.
* Ge tillräckligt med information och steg-för-steg-förklaringar för att hjälpa läsarna att navigera i experimentet.
* Visuella objekt kan vara till hjälp för läsarna att tolka och använda experimentdokumentationen på rätt sätt. Visuella objekt inkluderar experimentdiagram och skärmdumpar av data.
* Om du inkluderar en datauppsättning i experimentet (det vill säga du importerar inte datauppsättningen via modulen Importera data) är datauppsättningen en del av experimentet och publiceras i galleriet. Kontrollera att den datauppsättning du publicerar har licensvillkor som tillåter delning och nedladdning av vem som helst. Galleribidrag omfattas [av Azures användningsvillkor](https://azure.microsoft.com/support/legal/website-terms-of-use/).

## <a name="contribute-tutorials-and-collections"></a>Självstudier och samlingar i Contribute

Du kan hjälpa andra genom att skriva en **självstudiekurs** i galleriet som förklarar machine learning-begrepp eller genom att skapa en **samling** som samlar ihop flera resurser kring en viss lösning.

1. Logga in på Galleriet med ditt Microsoft-konto.

1. Markera bilden i det övre högra hörnet på sidan och välj sedan ditt namn.

1. Välj **Nytt objekt**.

1. Välj **Självstudie** eller **Samling**för **ARTIKELTYP**på sidan **Beskrivning** . Ange ett namn, en kort sammanfattning, en detaljerad beskrivning och eventuella taggar som kan hjälpa andra användare att hitta ditt bidrag. Klicka sedan på **Nästa**.

1. På sidan **Bildval** väljer du en bild som visas med ditt bidrag. Du kan ladda upp din egen bildfil eller välja en arkivbild. Välj en bild som kan hjälpa användarna att identifiera innehållet och syftet med ditt bidrag. Klicka sedan på **Nästa**.

1. Välj **Settings** om ditt bidrag är **offentligt** (vem som helst kan visa det) eller **Olistat** (endast personer med direktlänk kan visa det för **synlighet.**

   > [!TIP]
   > Om du vill vara säker på att dokumentationen ser korrekt ut innan du släpper den offentligt kan du först publicera experimentet som **olistad**. Senare kan du ändra synlighetsinställningen till **Offentlig** på experimentinformationssidan. Observera att när du har angett ett experiment **till Offentlig** kan du inte senare ändra det till **Olistad**.

1. Välj **Skapa**.

Ditt bidrag finns nu i Azure AI Gallery. Dina bidrag visas på din kontosida på fliken **Objekt.**

### <a name="add-to-and-edit-your-collection"></a>Lägga till i och redigera din samling

Du kan lägga till objekt i samlingen på två sätt:

* Öppna samlingen, välj **Redigera**och välj sedan **Lägg till objekt**. Du kan lägga till objekt som du har bidragit till i Galleriet eller söka i Galleriet efter objekt som ska läggas till. När du har markerat de objekt som du vill lägga till klickar du på **Lägg till**.

* Om du hittar ett objekt som du vill lägga till när du bläddrar i galleriet öppnar du objektet och väljer **Lägg till i samlingen**. Markera den samling som du vill lägga till objektet i.

Du kan redigera objekten i samlingen genom att välja **Redigera**.

* Du kan ändra sammanfattningen, beskrivningen eller taggarna för samlingen.
* Du kan ändra ordningen på objekten i samlingen med hjälp av pilarna bredvid ett objekt.
* Om du vill lägga till anteckningar i objekten i samlingen markerar du det övre högra hörnet av ett objekt och väljer sedan **Lägg till/redigera anteckning**.
* Om du vill ta bort ett objekt från samlingen markerar du det övre högra hörnet av ett objekt och väljer sedan **Ta bort**.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**Vilka är kraven för att skicka in eller redigera en bild?**

Bilder som du skickar in med ditt bidrag används för att skapa en panel. Vi rekommenderar att bilderna är mindre än 500 kB, med ett bildförhållande på 3:2 och en upplösning på 960 &#215; 640.

**Vad händer med datauppsättningen som jag använde i ett experiment? Publiceras datauppsättningen också i galleriet?**

Om datauppsättningen är en del av experimentet och inte importeras via modulen Importera data publiceras datauppsättningen i galleriet som en del av experimentet. Kontrollera att datauppsättningen som du publicerar med experimentet har rätt licensvillkor. Licensvillkoren bör göra det möjligt för vem som helst att dela och ladda ned data. Galleribidrag omfattas [av Azures användningsvillkor](https://azure.microsoft.com/support/legal/website-terms-of-use/).

**Jag har ett experiment som använder en importdatamodul för att hämta data från Azure HDInsight eller SQL Server. Den använder mina autentiseringsuppgifter för att hämta data. Kan jag publicera den här typen av experiment? Hur kan jag vara säker på att mina autentiseringsuppgifter inte delas?**

För närvarande kan du inte publicera ett experiment i Galleriet som använder autentiseringsuppgifter.

**Hur anger jag flera taggar?**

När du har angett en tagg trycker du på Tabb för att ange en annan tagg.

## <a name="we-want-to-hear-from-you"></a>Vi vill gärna höra vad du har att säga!

Vi vill att galleriet ska drivas av våra användare och för våra användare. Använd smiley till höger för att berätta vad du älskar eller hatar med galleriet.  

![Feedback](./media/gallery-how-to-use-contribute-publish/feedback.png)

**[TA MIG TILL GALLERIET >>](https://gallery.azure.ai)**
