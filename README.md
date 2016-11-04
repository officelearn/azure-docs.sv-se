# Guide för bidragsinlämnare av teknisk dokumentation för Azure
Du har kommit till GitHub-lagringsplatsen där källan för den tekniska dokumentationen som publiceras på Azure Documentation Center på [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) finns.

På den här lagringsplatsen finns också anvisningar om hur du bidrar till vår tekniska dokumentation.  En lista över artiklarna i guiden för bidragsinlämnare finns i [indexet](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Bidra till Azure-dokumentationen
Tack för ditt intresse för Azure-dokumentation!

* [Sätt att bidra](#ways-to-contribute)
* [Uppförandekod](#code-of-conduct)
* [Om dina bidrag till Azure-innehåll](#about-your-contributions-to-azure-content)
* [Så här är lagringsplatsen organiserad](#repository-organization)
* [Använda GitHub, Git och den här lagringsplatsen](#use-github-git-and-this-repository)
* [Så här använder du Markdown för att formatera ditt ämne](#how-to-use-markdown-to-format-your-topic)
* [Feedback, kommentarer och support](./contributor-guide/feedback-and-comments.md)
* [Fler resurser](#more-resources)
* [Index över alla artiklar i guiden för bidragsinlämnare](./contributor-guide/contributor-guide-index.md) (öppnas i en ny sida)

## Sätt att bidra
Du kan bidra till [Azure-dokumentationen](http://azure.microsoft.com/documentation/) på olika sätt:

* Bidra med en [forumdiskussion](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Skriv Disqus-kommentarer längst ned i artiklarna.
* Du kan enkelt bidra med tekniska artiklar i användargränssnittet för GitHub. Du kan antingen hitta artikeln på den här lagringsplatsen eller så kan du söka efter artikeln på [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) och klicka på länken i den artikel som leder till GitHub-källan för artikeln.
* Om du gör omfattande ändringar i en befintlig artikel, lägger till eller ändrar bilder eller bidrar med en ny artikel, måste du duplicera den här lagringsplatsen, installera Git BASH och Markdown Pad samt lära dig vissa git-kommandon.

## Uppförandekod
Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Läs [Vanliga frågor och svar om uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta [opencode@microsoft.com](mailto:opencode@microsoft.com) om du har några andra frågor eller kommentarer.

## Om dina bidrag till Azure-innehåll
### Mindre ändringar
Mindre ändringar eller förtydliganden i dokumentation och kodexempel som du skickar in till den här lagringsplatsen styrs av [Användningsvillkoren för Azure-webbplatsen (ToU)](http://azure.microsoft.com/support/legal/website-terms-of-use/).

### Större bidrag
Om du skickar en pull-begäran med nya eller betydande förändringar av dokumentationen och kodexempel, skickar vi en kommentar i GitHub där du uppmanas att skicka ett licensavtal för bidragsinlämnare online (CLA, Contribution License Agreement), om du tillhör någon av dessa grupper:

* Medlemmar av gruppen Microsoft Open Technologies.
* Bidragsinlämnare som inte arbetar för Microsoft.

Du måste fylla i hela online-formuläret innan vi kan acceptera din pull-begäran.

Fullständig information finns på [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## Så här är lagringsplatsen organiserad
Innehållet på lagringsplatsen för Azure-innehåll följer samma struktur som dokumentationen på [Azure.Microsoft.com](http://azure.microsoft.com). Den här lagringsplatsen innehåller två rotmappar:

### \articles
Mappen *\articles* innehåller dokumentationsartiklarna formaterade som Markdown-filer med filtillägget *.md*.

Artiklar i rotkatalogen publiceras till Azure.Microsoft.com med sökvägen *http://azure.microsoft.com/documentation/articles/ {artikelns-namn-utan-md} /*.

* **Filnamn för artiklar:** Se [våra riktlinjer för namngivning](./contributor-guide/file-names-and-locations.md).

Artiklar som har en egen tjänstemapp publiceras till Azure.Microsoft.com med sökvägen *http://azure.microsoft.com/documentation/articles/service-folder/{artikelns-namn-utan-md}/*

* **Undermappar för media:** Mappen *\articles* innehåller mappen *\media* där mediefilerna för rotkatalogens artikel finns. Inne i den här mappen finns det undermappar med bilder för varje artikel.  Tjänstemapparna innehåller en separat mediemapp för artiklarna i varje enskild tjänstemapp. Mapparna med artikelavbildningar får samma namn som artikelfilen, minus filnamnstillägget *.md*.

### \includes
Du kan skapa återanvändbara innehållssektioner som ska ingå i en eller flera artiklar. Se [Anpassade tillägg som används i det tekniska innehållet](./contributor-guide/custom-markdown-extensions.md).

### \markdown templates
Den här mappen innehåller den standardiserade Markdown-mallen, med den grundläggande Markdown-formatering som du behöver för en artikel.

### \contributor-guide
Den här mappen innehåller artiklar som ingår i vår guide för bidragsinlämnare.  

## Använda GitHub, Git och den här lagringsplatsen
Om du vill få mer information om hur du kan bidra, hur du använder GitHub UI för att bidra med små ändringar samt hur du kan duplicera och klona lagringsplatsen för att skicka in större bidrag, kan du gå till [Installera och konfigurera verktyg för redigering i GitHub](./contributor-guide/tools-and-setup.md).

Om du installerar Git BASH och väljer att arbeta lokalt, kan du gå till [Git-kommandon för att skapa en ny artikel eller uppdatera en befintlig](./contributor-guide/git-commands-for-master.md) för att få anvisningar om hur du skapar en ny lokal arbetsgren, gör ändringar och skickar tillbaka ändringarna till huvudgrenen.

### Grenar
Vi rekommenderar att du skapar lokala arbetsgrenar som är inriktade på ett specifikt ändringsområde. Varje gren bör begränsas till ett enda koncept eller en enda artikel, både för att effektivisera arbetsflödet och för att minska risken för sammanslagningskonflikter.  Följande åtgärder har en lämplig omfattning för en ny gren:

* En ny artikel (och associerade bilder)
* Stavnings- och grammatikändringar i en artikel.
* Tillämpa en enda typ av formatändringar på ett stort antal artiklar (t.ex. en ny sidfot angående upphovsrätten).

## Så här använder du Markdown för att formatera ditt ämne
Alla artiklar på den här lagringsplatsen använder informativ (flavored) markdown för GitHub.  Här följer en lista över resurser.

* [Grunderna i Markdown](https://help.github.com/articles/markdown-basics/)
* [Utskrivbara fusklappar för Markdown](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)
* En lista över Markdown-redigerare finns under [ämnet verktyg och installation](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Metadata för artiklar
Metadata för artiklar tillhandahåller vissa funktioner på webbplatsen azure.microsoft.com, till exempel författarattribut, attribut för bidragsinlämnare, synliga sökvägar, artikelbeskrivningar och SEO-optimeringar samt även rapportering av Microsoft-användning för att utvärdera hur väl innehållet fungerar. Metadata är därför viktiga! [Här följer riktlinjer om hur du för in dina metadata på rätt](./contributor-guide/article-metadata.md).

## Fler resurser
Gå till [indexet i guiden för bidragsinlämnare](./contributor-guide/contributor-guide-index.md) för att visa alla hjälpämnen.

<!--HONumber=Aug16_HO1-->


