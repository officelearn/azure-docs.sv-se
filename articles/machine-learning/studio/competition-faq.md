---
Rubrik: Vanliga frågor och svar Cortana Intelligence-tävlingar titleSuffix: Beskrivning av Azure Machine Learning Studio: Vanliga frågor om Microsoft Cortana Intelligence, tävlingar.
tjänster: machine learning ms.service: machine learning ms.component: studio ms.topic: artikel

author: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 12/18/2017
---
# <a name="microsoft-cortana-intelligence-competitions-faq"></a>Microsoft Cortana Intelligence, tävlingar, vanliga frågor och svar
**Vad är Cortana Intelligence, tävlingar?**

Microsoft Cortana Intelligence, tävlingar, unites en global community med dataentusiaster genom att lösa några av världens mest komplexa data science problem tillsammans. Cortana Intelligence, tävlingar, Tillåt dataentusiaster från över hela världen att konkurrera och skapa modeller för mycket exakta och intelligenta data science. De här värdbaserade tävlingar baseras på unika datauppsättningar som har gjorts tillgängliga offentligt för första gången. Deltagare kan vinna belöningar eller få erkännande via de översta 10 offentliga rankningslista. Du kan komma åt webbplatsen tävlingar på [aka.ms/CIComp](https://aka.ms/CIComp).

**Hur ofta kommer Microsoft att släppa nya tävlingar?**

Vi kommer att startas part 1: a, ägs av Microsoft tävlingar med jämna mellanrum, ungefär var 8-12 veckor. 

**Var kan jag ställa allmänna frågor om datavetenskap?**

För allmänna frågor kan du använda den [Microsoft Azure Machine Learning-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Hur anger jag en tävling**

Åtkomst till den [tävlingar](https://gallery.cortanaintelligence.com/competitions) startsida i den [Azure AI-galleriet](https://gallery.cortanaintelligence.com/), eller gå till [ http://aka.ms/CIComp ](https://aka.ms/CIComp). På sidan listar alla tävlingar som körs för närvarande. Varje tävling har detaljerade anvisningar och deltagande regler, priser och varaktighet för dess registreringssidan.

1. Hitta den tävling som du vill delta i, läsa alla instruktioner och se självstudievideo. Klicka sedan på den **ange tävling** knappen och kopiera Starter experimentet till din befintliga Azure Machine Learning-arbetsyta. Om du inte redan har åtkomst till en arbetsyta, måste du skapa en i förväg. Kör experimentet Starter, se prestandamått och sedan använda din kreativitet för att förbättra prestandan för modellen. Du kommer troligen att tillbringa merparten av din tid i det här steget.   

2. Skapa ett förutsägbart Experiment med den tränade modellen av experimentet Starter. Sedan noggrant justera indata och utdata schemat för webbtjänsten för att se till att de uppfyller de krav som anges i dokumentationen för tävlingen. Självstudiekursen dokumentet har vanligtvis detaljerade instruktioner för detta. Du kan också se självstudievideo, om det är tillgängligt.   

3. Distribuera en webbtjänst utanför din förutsägbart Experiment. Testa din web service med hjälp av den **Test** knapp eller Excel-mallen som skapas automatiskt för dig att kontrollera att den fungerar korrekt.   

4. Skicka in din webbtjänst som posten konkurrens och se dina offentliga poäng på sidan Azure AI-galleriet tävling. Och fira om du gör det till ledande!  

När du har skickat en post, kan du gå tillbaka till experimentet kopierade Starter. Sedan iterera och uppdatera din Förutsägelseexperiment, uppdatera webbtjänsten och skicka en ny post.   

**Kan jag använda open source-verktyg för att du deltar i dessa tävlingar?**

Tävlingen deltagarna använder Azure Machine Learning Studio, en molnbaserad tjänst i Cortana Intelligence Suite för utveckling av data science-modeller och skapa tävling poster för bidrag. Machine Learning Studio erbjuder inte bara ett GUI-gränssnitt för att konstruera machine learning-experiment, det också möjligt att ta med din egen R och/eller Python-skript för körning av interna. R och Python-körningar i Studio levereras med en omfattande uppsättning R/Python-paket med öppen källkod. Du kan importera egna paket som en del av experimentet samt. Studio har även en inbyggd Jupyter Notebook-tjänst som du kan kostnadsfria style datautforskning. Du kan naturligtvis kan alltid hämta datauppsättningar som används i tävlingen och utforska i din favorit verktyget utanför Machine Learning Studio. 

**Måste jag vara någon dataexpert för att ange?**

Nej. I själva verket gärna dataentusiaster, vem som helst nyfiken på datavetenskap och andra blivande dataexperter vår tävlingen. Vi har utformat stöddokument för Tillåt alla att delta. Målgruppen är:

* **Data utvecklare**, **Dataexperter**, **BI** och **Analytics proffs**: de som ansvarar för att producera och analys innehåll för andra kan använda
* **Data förvaltare**: de som har kunskapen om data, vad det innebär och hur den har avsedd att användas och vilka ändamål
* **Studenter** och **forskare:** de som utbildning och få användbara data som är relaterade kunskaper via akademiska programmen på universitet eller deltagare i stora öppna onlinekurser (Mooc)

**Kan jag ange med min kollegor som ett team?**

Tävlingen plattformen stöder för närvarande inte team deltagande. Varje post i tävlingen är kopplad till en enda användaridentitet. 

**Behöver jag betala för att delta i en tävling?**

Tävlingarna är kostnadsfria att delta i. Du, men behöver åtkomst till en Azure Machine Learning-arbetsyta för att delta. Du kan skapa en kostnadsfri arbetsyta utan kreditkort genom att logga in med ett giltigt microsoftkonto eller ett Office 365-konto. Om du redan är kund med Azure eller Cortana Intelligence Suite kan du skapa och använda standardarbetsytan under samma Azure-prenumeration. Om du vill köpa en Azure-prenumeration går du till den [prissättning för Azure](https://azure.microsoft.com/pricing) sidan. Observera att standardpriser gäller när du använder en standardarbetsytan för att konstruera experiment. Mer information finns i [information om priser för Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/). 



**Vad är offentliga och privata poäng?**

I de flesta tävlingar ser du att du får en offentlig poäng för alla bidrag du gör, vanligtvis inom 10-20 minuter. Men efter konkurrenterna ut, får du en privat poäng som används för slutlig rangordning. 

Här är vad som händer:

* Hela datamängden som används i tävlingen delas slumpmässigt med stratifiering till utbildning och testning (de återstående) data. Slumpmässig delningen stratified för att säkerställa att distributioner för etiketter i både för träning och testning av data är konsekventa.
* Träningsdata överförs och fått som en del av Starter Experiment i modulkonfigurationen importera Data.
* Testdata ytterligare delas upp i offentliga och privata testdata, med hjälp av samma stratifieringen.
* Offentliga testdata används för den första runda av bedömning. Resultatet kallas för den offentliga poängen. Det är det som visas i historiken skickas när du skickar in ditt bidrag. Det här resultatet beräknas för varje post som du skickar. Den här offentliga poäng används för att du är på den offentliga rankningslista.
* Privata testdata används för den slutliga runda av bedömning när tävlingen har upphört. Detta kallas för privata poäng. 
* För varje deltagare väljs ett fast antal poster med högsta offentliga poängen automatiskt för att ange den privata bedömnings runda (det här antalet kan variera beroende på tävlingen). Posten med den högsta privata poängen väljs då att ange sista rangordning som slutligen avgör priset vinnarna.  

**Kunder kan ha en tävling på vår plattform?**

Vi välkomnar 3 part organisationer för att samarbeta med oss och vara värd för både offentliga och privata tävlingar på vår plattform. Vi har en tävling onboarding-teamet som glada över att diskutera registreringsprocessen för sådana tävlingar.  Skaffa kontakten med oss på [ compsupport@microsoft.com ](mailto:compsupport@microsoft.com) för mer information. 

**Vilka är begränsningarna för bidrag?**

En typisk tävling kan välja att begränsa antalet poster som du kan skicka i ett 24-timmars intervall (UTC-tid 00:00:00 till 23:59:59) och det totala antalet poster som du kan skicka under loppet av tävlingen. Du får felmeddelanden när en begränsning har överskridits. 

**Vad händer om jag vinna en tävling?**

Microsoft verifierar resultaten av den privata rankningslista och sedan vi kontaktar dig. Kontrollera att din e-postadress i användarprofilen är uppdaterad.

**Hur får jag pris pengar om jag vinna en tävling?**

Om du är en vinnare av tävling, behöver du logga en deklaration av behörighet och licens versionen från. Det här formuläret reiterates regler för Innovationstävling. Vinnare måste du fylla i ett formulär för oss skatt W-9 eller ett formulär W-8BEN om de inte är USA skattebetalare. Vi kommer att kontakta alla vinnare som en del av fördelarna utbetalning processen genom att använda sin e-post för registrering. Se våra [villkor](https://aka.ms/comptermsandconditions) för ytterligare information.

**Vad händer om mer än en post får samma poäng?**

Skicka-tid är prioritet. Den post som har skickats tidigare outranks posten senare.

**Kan jag delta med hjälp av gäst-arbetsytan?**

Nej. Du måste använda en kostnadsfri eller en standardarbetsytan för att delta. Du kan öppna tävling starter experiment i en gäst-arbetsyta, men du kommer inte att kunna skapa en giltig post för bidrag från arbetsytan. 

**Kan jag delta med en arbetsyta i alla Azure-regioner?**

Stöder för närvarande plattformen konkurrensen bara poster som har skickats från en arbetsyta i den **södra centrala USA** Azure-region. Alla kostnadsfria arbetsytor finns i södra centrala USA, så kan du skicka en post från en kostnadsfri arbetsyta. Om du väljer att använda en standardarbetsytan, kontrollerar du att den finns i regionen södra centrala USA Azure, annars ditt bidrag inte lyckas. 

**Håller vi användarnas tävlingar lösningar och -poster?**

Användarposter behålls endast att identifiera de vinnande lösningarna i utvärderingssyfte. Se våra [villkor](https://aka.ms/comptermsandconditions) ge specifik information.

