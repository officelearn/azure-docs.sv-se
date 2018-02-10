## <a name="microsoft-open-source-code-of-conduct"></a>Microsoft Open källa uppförandekod

Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/).
Mer information finns i [genomföra FAQ kod](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta [ opencode@microsoft.com ](mailto:opencode@microsoft.com) med några andra frågor eller kommentarer.

## <a name="contribute-to-azure-technical-documentation"></a>Bidra till teknisk dokumentation för Azure
Vi välkomnar bidrag från vår community (användare, kunder, partner, MSFT anställda utanför core Azure produktenheter, etc.) och från medarbetare som arbetar i core Azure produktenheter. Hur du bidra beror på vilka är:

* **Community - mindre uppdateringar**: Om du bidrar med mindre uppdateringar utanför goodness av din hjärta, du kan hitta artikeln på den här lagringsplatsen eller finns i artikeln på [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) och klicka på den **Redigera** länken i den artikel som leder till GitHub-källan för artikeln. Sedan bara Använd GitHub UI för att göra din uppdateringar. Eller också är Välkommen till förgrening databasen och skicka uppdateringar från din förgrening.

* **Community - nya artiklar**: Om du är en del av Azure community och du vill skapa en ny artikel, du behöver arbeta med en medarbetare för att anpassa den nya innehållet i genom en kombination av resurser i offentligt och privata databasen.

* **Anställda**: Om du är en teknisk skrivare, Programhanteraren eller utvecklaren från Produktteamet för en Azure-tjänst och det är ditt jobb att bidra till eller redigera tekniska artiklar, bör du använda privata databasen (https://github.com/ MicrosoftDocs/azure-docs-pr). Om du gör betydande ändringar i en befintlig artikel, måste lägger till eller ändrar bilder eller bidrar med en ny artikel du duplicera den här lagringsplatsen, installera Git Bash och markdown-redigerare och lära dig vissa git-kommandon. Se [interna deltagare guiden](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) för mer information.


## <a name="about-your-contributions-to-azure-content"></a>Om dina bidrag till Azure-innehåll
### <a name="minor-corrections"></a>Mindre ändringar
Mindre ändringar eller förtydliganden du skickar dokumentation och kodexempel i den här lagringsplatsen styrs av den [docs.microsoft.com användningsvillkoren](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions"></a>Större bidrag
Om du skickar en pull-begäran med nya eller betydande förändringar av dokumentationen och kodexempel skickar vi en kommentar i GitHub där du uppmanas att skicka ett online bidrag License avtal (CLA) om du inte är anställd hos Microsoft. Du måste fylla i hela online-formuläret innan vi kan acceptera din pull-begäran.

## <a name="tools-and-setup"></a>Verktyg och inställningar
Communitydeltagare kan använda GitHub UI eller duplicera lagringsplatsen med egna bidrag. Anställda Gå [interna deltagare guiden](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master) för mer information om hur du kan bidra till den tekniska dokumentationen.

## <a name="repository-organization"></a>Så här är lagringsplatsen organiserad
Innehållet i dokumenten azure-databasen följer av dokumentationen på https://docs.microsoft.com/azure. Den här lagringsplatsen innehåller två rotmappar:

### <a name="articles"></a>\articles
Mappen *\articles* innehåller dokumentationsartiklarna formaterade som Markdown-filer med filtillägget *.md*. Artiklar grupperas vanligtvis av Azure-tjänsten.

Den *\articles* mappen innehåller de *\media* mapp för roten directory artikel mediefiler, inom vilket finns det undermappar med avbildningar för varje artikel.  Tjänstemapparna innehåller en separat mediemapp för artiklarna i varje enskild tjänstemapp. Mapparna med artikelavbildningar får samma namn som artikelfilen, minus filnamnstillägget *.md*.

### <a name="includes"></a>\includes
Du kan skapa återanvändbara innehållssektioner som ska ingå i en eller flera artiklar. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Så här använder du Markdown för att formatera ditt ämne
Alla artiklar på den här lagringsplatsen använder informativ (flavored) markdown för GitHub.  Här följer en lista över resurser.

* [Grunderna i markdown](https://help.github.com/articles/markdown-basics/)
* [Utskrivbara fusklappar för markdown](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)


## <a name="labels"></a>Etiketter
I offentliga azure-dokumenten-databasen tilldelas automatiskt etiketter pull-förfrågningar som hjälper oss att hantera arbetsflöden för pull-begäran och för att informera dig om vad som händer med din pull-begäran:

* Bidrag licensavtalet relaterade
  * CLA inte krävs: ändringen är relativt små och kräver inte att du registrerar en CLA.
  * CLA krävs: omfånget för ändringen är relativt stora och kräver att du registrerar en CLA.
  * CLA-signerad: deltagaren signerade CLA, så pull-begäran kan nu flytta framåt för granskning.
* Ändra skickas till författare: författaren har underrättats om väntande pull-begäran.
* redo att sammanfoga: klar för granskning av våra pull-begäran granska team.


