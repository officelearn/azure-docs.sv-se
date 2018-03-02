## <a name="microsoft-open-source-code-of-conduct"></a>Microsoft Open källa uppförandekod

Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/).
Mer information finns i [genomföra FAQ kod](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta [ opencode@microsoft.com ](mailto:opencode@microsoft.com) med några andra frågor eller kommentarer.

## <a name="contribute-to-azure-technical-documentation"></a>Bidra till teknisk dokumentation för Azure
Vi välkomnar bidrag från vår community och från Microsofts anställda från utanför dokumentationen team. Hur du bidra beror på vilka du är och sorteringen av ändringar du vill bidra:

* **Community - mindre uppdateringar**: Om du bidrar med mindre uppdateringar utanför goodness av din hjärta, du kan hitta artikeln på den här lagringsplatsen eller finns i artikeln på [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) och klicka på den **Redigera** länken i den artikel som leder till GitHub-källan för artikeln. Sedan bara Använd GitHub UI för att göra din uppdateringar. Eller också är Välkommen till förgrening databasen och skicka uppdateringar från din förgrening. Se vår allmänna [guide för bidragsinlämnare](https://docs.microsoft.com/contribute/) för mer information.

* **Community - nya artiklar + större ändringar**: Om du är en del av Azure community och du vill skapa en ny artikel eller skicka större ändringar så att skicka ett problem att starta en konversation med dokumentationsteamet. När du har godkänt en plan kan behöver du arbeta med en medarbetare för att anpassa det nya innehållet genom en kombination av arbete i offentliga och privata databaser. 

* **Anställda**: Om du är en teknisk skrivare, Programhanteraren eller utvecklaren från Produktteamet för en Azure-tjänst och det är ditt jobb att bidra till eller redigera tekniska artiklar, bör du använda privata databasen (https://github.com/ MicrosoftDocs/azure-docs-pr). Anställda från andra delar av världen Microsoft bör använda offentliga lagringsplatsen för mindre uppdateringar.

## <a name="about-your-contributions-to-azure-content"></a>Om dina bidrag till Azure-innehåll
### <a name="minor-corrections"></a>Mindre ändringar
Mindre ändringar eller förtydliganden du skickar dokumentation och kodexempel i den här lagringsplatsen styrs av den [docs.microsoft.com användningsvillkoren](https://docs.microsoft.com/legal/termsofuse).

### <a name="larger-submissions-from-community-members"></a>Större bidrag från community-medlemmar
Om du skickar en pullbegäran med betydande förändringar av dokumentationen och kodexempel visas ett meddelande i pull-begäran där du uppmanas att skicka ett licensavtal för bidrag online (CLA). Vi måste du genomföra online-formuläret innan vi kan granska pull-begäran.

## <a name="tools-and-setup"></a>Verktyg och inställningar
Communitydeltagare kan använder GitHub UI eller duplicera lagringsplatsen bidra - mer information finns i vår [guide för bidragsinlämnare](https://docs.microsoft.com/contribute). 

## <a name="repository-organization"></a>Så här är lagringsplatsen organiserad
Innehållet i dokumenten azure-databasen följer av dokumentationen på https://docs.microsoft.com/azure. Den här lagringsplatsen innehåller två rotmappar:

### <a name="articles"></a>\articles
Mappen *\articles* innehåller dokumentationsartiklarna formaterade som Markdown-filer med filtillägget *.md*. Artiklar grupperas vanligtvis av Azure-tjänsten.

Den *\articles* mappen innehåller de *\media* mapp för roten directory artikel mediefiler, inom vilket finns det undermappar med avbildningar för varje artikel.  Tjänstemapparna innehåller en separat mediemapp för artiklarna i varje enskild tjänstemapp. Mapparna med artikelavbildningar får samma namn som artikelfilen, minus filnamnstillägget *.md*.

### <a name="includes"></a>\includes
Du kan skapa återanvändbara innehållssektioner som ska ingå i en eller flera artiklar. 

## <a name="how-to-use-markdown-to-format-your-topic"></a>Så här använder du Markdown för att formatera ditt ämne
Alla artiklar på den här lagringsplatsen använder informativ (flavored) markdown för GitHub. Om du inte är bekant med markdown, se:

* [Grunderna i markdown](https://help.github.com/articles/markdown-basics/)
* [Utskrivbara fusklappar för markdown](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)


## <a name="labels"></a>Etiketter
I offentliga azure-dokumenten-databasen tilldelas automatiskt etiketter pull-förfrågningar som hjälper oss att hantera arbetsflöden för pull-begäran och för att informera dig om vad som händer med din pull-begäran:

* **Ändra skickas till redigera**: författaren har underrättats om väntande pull-begäran.
* **redo att sammanfoga**: klar för granskning av våra pull-begäran granska team.


