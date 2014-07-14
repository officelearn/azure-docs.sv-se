<properties  umbracoNaviHide="0" pageTitle="Application Model" metaKeywords="Azure, Azure, application model, Azure application model, development model, Azure development model, hosted service, Azure hosted service, web role, worker role" description="Learn about the Azure hosted service application model. Understand core concepts, design considerations, defining and configuring your application, and scaling." linkid="dev-net-fundamentals-application-model" urlDisplayName="Application Model" headerExpose="" footerExpose="" disqusComments="1" title="Application Model" authors="" />

# Körningsmodeller i Azure

Azure erbjuder olika körningsmodeller för att köra program. Varje modell erbjuder olika tjänsteuppsättningar, så vilken du väljer beror på exakt vad du vill göra. I den här artikeln tittar vi närmare på tre av dessa modeller och beskriver varje teknik och ger exempel på när du kan använda dem.

## Innehållsförteckning

* [Virtuella datorer](#VMachine)
* [Web Sites](#WebSites)
* [Molntjänster](#CloudServices)
* [Vad bör jag använda? Göra ett val](#WhatShouldIUse)

<h2><a  id="VMachine" ></a>Virtuella datorer</h2>


Azure Virtuella datorer gör det möjligt för utvecklare, IT-driftansvariga och andra att skapa och använda virtuella datorer i molnet. Genom att erbjuda s.k. *IaaS (infrastruktur som en tjänst)* kan den här tekniken användas på flera olika sätt. [Figur 1](#Fig1) visar de grundläggande komponenterna.

<a name="Fig1"></a>![01_CreatingVMs](./media/fundamentals-application-models/ExecModels_01_CreatingVMs.png)

**Figur 1: Azure Virtuella datorer erbjuder IaaS (infrastruktur som en tjänst).**

Som figuren visar kan du skapa virtuella datorer via antingen hanteringsportalen för Azure eller Azures REST-baserade tjänsthanterings-API. Du kommer åt hanteringsportalen från alla populära webbläsare, däribland Internet Explorer, Mozilla och Chrome. Gällande REST API:et erbjuder Microsoft klientskriptverktyg för Windows-, Linux- och Macintosh-system. Annan programvara kan också använda detta gränssnitt.

Hur du än kommer åt plattformen krävs det att du väljer en virtuell hårddisk (VHD) för den virtuella datorns avbildning när du skapar en ny virtuell dator. Dessa VHD:er lagras i Azures blob-lagring.

Du har två val för att komma igång

1.  Ladda upp en egen VHD 2.  Använda en VHD som tillhandahålls av Microsoft och dess partner i
    Azures galleri för virtuella datorer eller på Microsofts öppna
    webbplats [VMDepot][1].

VHD:erna i galleriet och på VMDepot inkluderar rena avbildningar av Microsoft- och Linux-operativsystem samt avbildningar med Microsoft-produkter och andra tredje parts produkter installerade. Alternativen ökar hela tiden. Exempel är olika versioner, utgåvor och konfigurationer av:

* Windows Server
* Linux-servrar som Suse, Ubuntu och CentOS
* SQL Server
* BizTalk Server
* SharePoint Server

Tillsammans med en VHD anger du storleken på din nya virtuella dator. Fullständig statistik för varje storlek finns i [Azure-biblioteket][2].

* **Extra liten** med en delad kärna och ett minne på 768 MB.
* **Liten** med 1 kärna och ett minne på 1,75 GB.
* **Medelstor** med 2 kärnor och ett minne på 3,5 GB.
* **Stor** med 4 kärnor och ett minne på 7 GB.
* **Extra store** med 8 kärnor och ett minne på 14 GB.
* **A6** med 4 kärnor och ett minne på 28 GB.
* **A7** med 8 kärnor och ett minne på 56 GB.

Till sist väljer du vilket Azure-datacenter du vill köra din nya virtuella dator i, oavsett om det är i USA, Europa eller Asien.

När en virtuell dator väl körs betalar du för varje timme den körs och du slutar betala när du tar bort den virtuella datorn. Summan du betalar baseras inte på hur mycket din virtuella dator används -- det baseras endast på faktisk tid. En virtuell dator som har låg belastning under en timme kostar lika mycket som en virtuell dator med hög belastning.

Varje virtuell dator som körs har en associerad *OS-disk* som lagras i ett blob-objekt. När du skapar en virtuell dator med en VHD från galleriet, kopieras denna VHD till din virtuella dators OS-disk. Alla ändringar du gör i din virtuella dators operativsystem lagras här. Om du till exempel installerar ett program som ändrar Windows-registret, lagras denna ändring på din virtuella dators OS-disk. Nästa gång du skapar en virtuell dator från samma OS-disk fortsätter den virtuella datorn att köras i samma tillstånd som du lämnade den i. För VHD:er som lagras i galleriet kör Microsoft uppdateringar när det behövs, till exempel operativsystemuppdateringar. För VHD:er på din egen OS-disk ansvarar du dock själv för att köra dessa uppdateringar (även om Windows Update är aktiverat som standard).

Virtuella datorer som körs kan också ändras och sedan avbildas med verktyget sysprep. Sysprep tar bort specifik information, t.ex. datorns namn, så att en VHD-avbildning kan användas för att skapa flera virtuella datorer med samma allmänna konfiguration. Dessa avbildningar lagras i hanteringsportalen tillsammans med dina uppladdade avbildningar så att de kan användas som utgångspunkt för nya virtuella datorer.

Virtuella datorer övervakar dessutom maskinvaran som är värd för varje virtuell dator du skapar. Om en fysisk server där en virtuell dator körs slutar fungera, märker plattformen detta och startar samma virtuella dator på en annan fysisk server. Och förutsatt att du har rätt licens, kan du kopiera en ändrad VHD från din OS-disk och sedan köra den någon annanstans, till exempel i ditt eget lokala datacenter eller hos en annan molnleverantör.

Tillsammans med OS-disken har din virtuella dator en eller flera datadiskar. Även om dessa ser ut som en monterad hårddisk på din virtuella dator, lagras faktiskt innehållet på varje disk i ett blob-objekt. Varje skrivåtgärd som görs till en datadisk lagras i det underliggande blob-objektet. Precis som med alla blob-objekt replikerar Azure dessa både inom ett och samma datacenter och mellan olika datacenter för att skydda mot fel.

Virtuella datorer som körs kan hanteras i hanteringsportalen, PowerShell och andra skriptverktyg, eller direkt genom REST API:et. (Allt du kan göra i hanteringsportalen kan faktiskt även göras genom detta API.) Microsoft-partner som RightScale och ScaleXtreme tillhandahåller även hanteringstjänster som använder REST API:et.

Azure Virtuella datorer kan användas på flera olika sätt. De huvudsakliga scenarierna som Microsoft riktar in sig på innefattar:

* **Virtuella datorer för utveckling och testning.**Utvecklingsgrupper
  behöver vanligtvis virtuella datorer med specifika konfigurationer när
  de skapar program. Azure Virtuella datorer erbjuder en enkel och
  billig metod för att skapa dessa virtuella datorer, använda dem och
  sedan ta bort dem när de inte längre behövs.
* **Köra program i molnet.**För vissa program är det vettigt att köra
  dem i det offentliga molnet. Tänk exempelvis på ett program med stora
  toppar när det kommer till efterfrågan. Det går alltid att köpa
  tillräckligt med datorer till ditt eget datacenter för att köra det
  här programmet, men de flesta av dessa datorer kommer troligtvis ofta
  stå oanvända. Genom att köra programmet i Azure kan du betala för
  extra virtuella datorer enbart när du behöver dem och sedan stänga av
  dem när toppen har nått sitt slut. Eller anta att du har ett nytt
  företag som behöver datorresurser på begäran snabbt och utan åtagande.
  Än en gång kan Azure vara rätt val.
* **Utvidga ditt lokala datacenter till det offentliga molnet.**Med
  Azure Virtuellt nätverk kan din organisation skapa ett virtuellt
  nätverk (VNET) som gör att en grupp virtuella datorer i Azure visas
  som en del av ditt eget lokala nätverk. Det gör det möjligt att köra
  program som SharePoint på Azure, en strategi som kan vara lättare att
  distribuera och/eller billigare än att köra programmet i ditt eget
  datacenter.
* **Katastrofåterställning.**Istället för att betala fortlöpande för ett
  säkerhetskopierat datacenter som sällan används, kan du med
  IaaS-baserad katastrofåterställning endast betala för datorresurserna
  du behöver när du faktiskt behöver dem. Om ditt primära datacenter
  till exempel slutar fungera, kan du skapa virtuella datorer som körs
  på Azure och köra viktiga program och sedan stänga ner dem när de inte
  längre behövs.

Det här är inte de enda möjligheterna, men de är bra exempel på hur du kan använda Azure Virtuella datorer.

### Gruppera virtuella datorer: Molntjänster

När du skapar en ny virtuell dator med Azure Virtuella datorer kan du välja att köra den fristående eller göra den till en del av en grupp virtuella datorer som körs tillsammans i en *molntjänst*. (De liknande namnen till trots ska du inte blanda ihop det här konceptet med Molntjänster, namnet på Azures PaaS-teknik; det är inte samma sak.) Alla fristående virtuella datorer får en egen offentlig IP-adress, medan alla virtuella datorer i samma molntjänst nås genom en enda offentlig IP-adress. [Figur 2](#Fig2) visar hur detta ser ut.

<a name="Fig2"></a>![02_CloudServices](./media/fundamentals-application-models/ExecModels_02_CloudServices.png)

**Figur 2: Alla fristående virtuella datorer har en egen offentlig IP-adress, medan virtuella datorer som grupperas i en molntjänst görs tillgängliga genom en och samma offentliga IP-adress.**

Om du till exempel skapar en virtuell dator där du ska skapa och testa ett enkelt program skulle du troligtvis använda en fristående virtuell dator. Men om du skapar ett flerskiktat program med en webbklientdel, en databas och kanske till och med ett mellanskikt, skulle du troligtvis ansluta flera virtuella datorer till en molntjänst, som du kan se i figur 2.

Genom att gruppera virtuella datorer i en molntjänst kan du dessutom använda andra alternativ. Azure erbjuder belastningsutjämning för virtuella datorer i samma molntjänst och sprider ut användarförfrågningar mellan dem. Virtuella datorer som är anslutna på det här sättet kan även kommunicera direkt med varandra över det lokala nätverket i ett Azure-datacenter.

Virtuella datorer i samma molntjänst kan också grupperas i en eller flera *tillgänglighetsuppsättningar*. Du kan förstå varför det här är viktigt genom att föreställa dig ett webbprogram som kör flera virtuella datorer för klientdelar. Om alla dessa virtuella datorer distribueras på samma fysiska dator eller till och med på samma datorrack kan ett enda maskinvarufel göra att det inte går att komma åt någon av dem. Men om dessa virtuella datorer grupperas i en tillgänglighetsuppsättning kommer Azure att distribuera dem över hela datacentret så att ingen av dem delar en och samma felpunkt.

### Scenario: Köra ett program med SQL Server

För att få en bättre förståelse för hur Azure Virtuella datorer fungerar är det bra att titta lite närmare på ett par scenarier. Föreställ dig till exempel att du vill skapa ett tillförlitligt och skalbart webbprogram som körs på Azure. Ett sätt att göra detta på är att köra programmets logik på en eller flera virtuella datorer i Azure och sedan använda SQL Server för datahanteringen. [Figur 3](#Fig3) visar hur detta ser ut.

<a name="Fig3"></a>![03_AppUsingSQLServer](./media/fundamentals-application-models/ExecModels_03_AppUsingSQLServer.png)

**Figur 3: Ett program som körs i Azure Virtuella datorer kan använda SQL Server för lagring.**

I det här exemplet skapas båda typerna av virtuella datorer med standard-VHD:er från galleriet. Programmets logik körs på Windows Server 2008 R2, så utvecklaren skapar tre virtuella datorer från denna VHD och installerar sedan sitt program på alla tre. Eftersom alla dessa virtuella datorer finns i samma molntjänst kan han konfigurera maskinvarans belastningsutjämning till att sprida ut förfrågningar mellan dem. Utvecklaren skapar också två virtuella datorer från galleriets VHD med SQL Server 2012. När dessa är igång konfigurerar han SQL Server på varje instans till att använda databasspegling med automatisk växling vid fel. Detta krävs dock inte -- programmet kan använda en enda SQL Server-instans, men den här strategin ökar tillförlitligheten.

### Scenario: Köra en SharePoint-grupp

Föreställ dig att en organisation vill skapa en SharePoint-grupp, men inte vill köra gruppen i sitt eget datacenter. Det lokala datacentret kanske saknar resurser, eller så kanske affärsenheten som skapar gruppen inte vill ha att göra med den interna IT-gruppen. I sådana fall kan det vara vettigt att köra SharePoint på Azure Virtuella datorer. [Figur 4](#Fig4) visar hur detta ser ut.

<a name="Fig4"></a>![04_SharePointFarm](./media/fundamentals-application-models/ExecModels_04_SharePointFarm.png)

**Figur 4: Azure Virtuella datorer gör det möjligt att köra en SharePoint-grupp i molnet.**

En SharePoint-grupp har flera komponenter och var och en körs i en virtuell dator i Azure som skapats från olika VHD:er. Det här behövs:

* Microsoft SharePoint. Det finns utvärderingsavbildningar i galleriet
  eller så tillhandahåller organisationen en egen VHD.
* Microsoft SQL Server. SharePoint är beroende av SQL Server, så
  organisationen skapar virtuella datorer som kör SQL Server 2012 med en
  standard-VHD från galleriet.
* Windows Server Active Directory. SharePoint kräver också Active
  Directory, så organisationen skapar domänkontrollanter i molnet med
  hjälp av en Windows Server-avbildning från galleriet. Detta krävs inte
  strikt sett -- det är också möjligt att använda lokala
  domänkontrollanter -- men SharePoint interagerar ofta med Active
  Directory, så strategin som visas här har bättre prestanda.

Även om det inte syns i figuren är den här SharePoint-gruppen troligtvis ansluten till ett lokalt nätverk med hjälp av Azure Virtuellt nätverk. Det gör att de virtuella datorerna -- och programmen på dem -- kan visas som lokala för personerna som använder nätverket.

Som du kan se i dessa exempel kan du använda Azure Virtuella datorer till att skapa och köra nya program i molnet, köra befintliga program, eller på andra sätt. Vilket alternativ du än väljer är teknikens mål detsamma: tillhandahålla en allmän grund för det offentliga molnet.

<h2><a  id="WebSites" ></a>Web Sites</h2>


Man kan använda webbtekniker på många olika sätt. Ett företag kan behöva migrera eller konfigurera en webbplats för webbnärvaro som kan hantera miljontals träffar varje vecka och distribuera den i flera datacenter över hela världen. En webbdesignbyrå kanske arbetar med ett utvecklarteam för att bygga ett anpassat webbprogram som kan hantera tusentals användare. En företagsutvecklare kanske behöver konfigurera ett program för att spåra kostnadsrapporter i molnet för autentiserade användare från företagets Active Directory. En IT-konsult kanske använder ett populärt program med öppen källkod för att konfigurera ett innehållshanteringssystem för ett litet företag. Allt detta skulle kunna åstadkommas med hjälp av Azure Virtuella datorer. Men att skapa och hantera råa virtuella datorer kräver en del kunskap och mycket arbete. Om du behöver implementera en webbplats eller ett webbprogram finns det en enklare (och billigare) lösning: strategin som vanligtvis kallas PaaS (plattform som en tjänst). Som figur 5 visar erbjuder Azure detta i Web
Sites.

<a name="Fig5"></a>![05_Websites](./media/fundamentals-application-models/ExecModels_05_Websites.png)

**Figur 5: Azure Web Sites innehåller stöd för statiska webbplatser, populära webbprogram och anpassade webbprogram som byggts med olika tekniker.**

Azure Web Sites har byggts ovanpå Azure Molntjänster för att skapa en PaaS-lösning som är optimerad för att köra webbprogram. Som figuren visar körs Web Sites på en uppsättning enstaka virtuella datorer som kan innehålla flera webbplatser som skapats av flera användare och standardmässiga virtuella datorer som tillhör en enskild användare. Virtuella datorer är en del av en resurspool som hanteras av Azure Web Sites och möjliggör därför hög tillförlitlighet och feltolerans. Det är enkelt att komma igång. Med Azure Web Sites kan användare välja bland en rad olika program, ramverk och mallar och skapa en webbplats på några sekunder. De kan sedan använda sina favoritutvecklingsverktyg (WebMatrix, Visual Studio, eller valfritt redigeringsprogram) och källkontrollsalternativ för att konfigurera kontinuerlig integrering och utveckla i ett team. Program som är beroende av en MySQL-databas kan använda en MySQL-tjänst för Azure som tillhandahålls av Microsoft-partnern ClearDB. Utvecklare kan skapa stora, skalbara webbprogram med Web Sites. Tekniken har stöd för att skapa program i ASP.NET, PHP, Node.js och Python. Programmen kan till exempel använda klistriga sessioner och befintliga webbprogram kan flyttas till den här molnplattformen utan några ändringar. Program som byggts i Web Sites kan använda andra delar av Azure, exempelvis Service Bus, SQL-databas och Blob-lagring. Du kan även köra flera kopior av ett program i olika virtuella datorer och Web Sites belastningsutjämnar då förfrågningar mellan dem automatiskt. Och eftersom nya instanser av Web Sites skapas i virtuella datorer som redan finns går det väldigt snabbt att starta ett nytt program -- betydligt snabbare än att vänta på att en ny virtuell dator ska skapas. Som [figur 5](#Fig5) visar kan du publicera kod och annat webbinnehåll i Web Sites på flera olika sätt. Du kan använda FTP, FTPS eller Microsofts WebDeploy-teknik. Web Sites har också stöd för publicering av kod från källkontrollssystem, däribland Git, GitHub, CodePlex, BitBucket, Dropbox, Mercurial, Team Foundation Server och molnbaserade Team Foundation Service.

<h2><a  id="CloudServices" ></a>Molntjänster</h2>


Azure Virtuella datorer erbjuder IaaS, medan Azure Web Sites erbjuder webbvärdtjänster. Det tredje alternativet, Molntjänster, erbjuder *PaaS (plattform som en tjänst)*. Den här tekniken har utformats för att ge stöd för program som är skalbara, tillförlitliga och billiga i drift. Det ska också tillåta utvecklare att sluta tänka på att hantera plattformen de använder och låta dem koncentrera sig helt och hållet på sina program. [Figur 6](#Fig6) visar konceptet.

<a name="Fig6"></a>![06_CloudServices2](./media/fundamentals-application-models/ExecModels_06_CloudServices2.png)

**Figur 6: Azure Molntjänster erbjuder PaaS.**

Precis som andra Azure-alternativ förlitar sig Molntjänster på virtuella datorer. ekniken erbjuder två något olika alternativ för virtuella datorer: instanser av *Web-roller* kör en variant av Windows Server med IIS, medan instanser av *Worker-roller* kör samma variant av Windows Server utan IIS. Ett program i Molntjänster förlitar sig på en kombination av dessa två alternativ.

Till exempel ett enkelt program kanske bara använder en Web-roll, medan ett mer avancerat program kanske använder en Web-roll för att hantera inkommande förfrågningar från användare och sedan skicka vidare arbetet som dessa förfrågningar skapar till en Worker-roll som bearbetar dem. (Denna kommunikation skulle kunna använda Service Bus eller köer i Azure.)

Som figuren visar körs alla virtuella datorer för ett enskilt program i samma molntjänst, på samma sätt som beskrivs ovan för Azure Virtuella datorer. Till följd av detta kommer användarna åt programmet genom en enda offentlig IP-adress där förfrågningar automatiskt belastningsutjämnas mellan programmets virtuella datorer. Och precis som med molntjänster som skapats med Azure Virtuella datorer, kommer plattformen att distribuera de virtuella datorerna i ett program i Molntjänster så att man undviker en enstaka punkt för maskinvarufel.

Även om program körs i virtuella datorer är det viktigt att förstå att Molntjänster tillhandahåller PaaS, inte IaaS. Så här kan man se på saken: Med IaaS, t.ex. Azure Virtuella datorer, kan du först skapa och konfigurera den miljö som ditt program ska köras i och sedan distribuera programmet i den här miljön. Du ansvarar själv för mycket av hanteringen och får göra saker som att distribuera nya korrigerade versioner av operativsystemet på varje virtuella dator. I PaaS däremot, är det som att miljön redan finns. Allt du behöver göra är att distribuera ditt program. Hanteringen av plattformen det körs på, inklusive distribution av nya versioner av operativsystemet, sköts åt dig.

Med Molntjänster skapar du inga virtuella datorer. Istället tillhandahåller du en konfigurationsfil som säger åt Azure hur många du vill ha av varje, exempelvis tre instanser av Web-roller och två instanser av Worker-roller, och sedan skapar plattformen dem åt dig. Du kan fortfarande välja storleken på dessa virtuella datorer -- alternativen är desamma som för Azure Virtuella datorer -- men du skapar dem uttryckligen inte själv. Om ditt program behöver hantera en större belastning kan du be om flera virtuella datorer, så skapar Azure dessa instanser. Om belastningen minskar kan du stänga ner dessa instanser och sluta betala för dem.

Ett program i Molntjänster görs vanligtvis tillgängligt för användare via en tvåstegsprocess. Första laddar utvecklaren upp programmet till plattformens mellanlagring. När utvecklaren är redo att lansera programmet använder hon hanteringsportalen i Azure till att begära att programmet görs tillgängligt. Den här växlingen från mellanlagring till produktion kan göras utan någon nedtid, vilket betyder att ett program som körs kan uppgraderas till en ny version utan att störa användarna.

Molntjänster erbjuder även övervakning. Precis som i Azure Virtuella datorer upptäcks en felaktig fysisk server och de virtuella datorerna som kördes på den servern startas om på en annan. Men Molntjänster upptäcker även felaktiga virtuella datorer och program, inte bara maskinvarufel. Till skillnad från Virtuella datorer finns det en agent inuti varje Web- och Worker-roll, så att nya instanser av virtuella datorer och program kan startas när fel uppstår.

Molntjänsters PaaS-natur har även andra följder. En av de viktigaste är att program som byggts på denna teknik bör skrivas för att köras korrekt när fel uppstår på en instans av en Web- eller Worker-roll. För att uppnå detta bör ett program i Molntjänster inte upprätthålla tillstånd i filsystemet på sina egna virtuella datorer. Till skillnad från virtuella datorer som skapas med Azure Virtuella datorer, är skrivåtgärder som görs till virtuella datorer i Molntjänster inte beständiga; det finns ingenting liknande datadisken i Virtuella datorer. Istället bör ett program i Molntjänster uttryckligen skriva alla tillstånd till SQL-databas, blob-objekt, tabeller eller ett annat externt lagringsutrymme. Genom att bygga program så här blir det enklare att skala dem och de blir mer tåliga mot fel -- två viktiga mål för Molntjänster.

<h2><a  id="WhatShouldIUse" ></a>Vad bör jag använda? Göra ett val</h2>


Alla tre körningsmodeller i Azure gör det möjligt att bygga skalbara och tillförlitliga program i molnet. Med denna grundläggande likhet i åtanke, vilken bör du då använda? Svaret beror på vad du vill göra.

Virtuella datorer erbjuder den mest allmänna lösningen. Om du vill ha så mycket kontroll som möjligt eller om du behöver generiska virtuella datorer, t.ex. för utveckling och testning, är det här det bästa alternativet. Virtuella datorer är också det bästa valet för att köra lokala standardprogram i molnet, vilket demonstrerades av SharePoint-exemplet ovan. Och eftersom de virtuella datorer du skapar med den här tekniken kan se ut precis som dina lokala virtuella datorer, är det också troligt att det här är det bästa valet för katastofåterställning. Haken är såklart att med stor kraft kommer stort ansvar, och IaaS kräver en del administrativt arbete från din sida.

Web Sites är det bästa valet när du vill skapa en enkel webbplats. Det stämmer i synnerhet om din webbplats ska baseras på ett befintligt program som Joomla, WordPress eller Drupal. Web Sites är också ett bra val om du vill skapa ett webbprogram med låg administration, till och med ett program som måste vara relativt skalbart, eller för att flytta ett befintligt IIS-webbprogram till det offentliga molnet. Det erbjuder dessutom snabb distribution. En ny instans av ditt program kan köras nästan direkt, medan det kan ta flera minuter att distribuera en ny virtuell dator med antingen Virtuella datorer eller Molntjänster.

Molntjänster, som var den första körningsmodellen som erbjöds av Azure, är uttryckligen en PaaS-strategi. Även om gränsen mellan PaaS och webbvärdtjänster är suddig, skiljer sig Molntjänster från Web Sites på några viktiga punkter, däribland följande:

* Till skillnad från Web Sites erbjuder Molntjänster administrativ
  åtkomst till programmets virtuella datorer. Det gör att du kan
  installera valfri programvara som ditt program behöver, något som inte
  är möjligt med Web Sites.
* Eftersom Molntjänster erbjuder både Web-roller och Worker-roller, är
  det ett bättre val än Web Sites för flerskiktiga program som behöver
  separata virtuella datorer för sin affärslogik.
* Molntjänster erbjuder separata miljöer för mellanlagring och
  produktion, vilket gör att programuppdateringar blir något smidigare
  än med Web Sites.
* Till skillnad från Web Sites kan du använda nätverkstekniker som Azure
  Virtuellt nätverk och Azure Connect för att ansluta lokala datorer
  till program i Molntjänster.
* Molntjänster gör det möjligt att använda Fjärrskrivbord för att
  ansluta direkt till ett programs virtuella datorer, något som inte är
  möjligt med Web Sites.

Eftersom det är en PaaS-lösning, erbjuder Molntjänster även vissa fördelar jämfört med Azure Virtuella datorer. Fler hanteringsuppgifter sköts åt dig, till exempel distribution av uppdateringar för operativsystem, så dina driftkostnader blir troligtvis lägre än med IaaS-strategin i Azure Virtuella datorer.

Alla tre körningsmodeller i Azure har för- och nackdelar. För att göra det bästa valet måste du förstå dessa, veta vad du försöker åstadkomma och sedan välja den lösning som passar bäst.

Ibland finns det en enskild körningsmodell som är rätt. I sådana situationer går det bra att kombinera alternativen. Anta till exempel att du bygger ett program där du vill ha hanteringsfördelarna i Molntjänsters Web-roller, men du behöver också använda en standard-SQL Server av anledningar som rör kompatibilitet och prestanda. I det här fallet är det bästa alternativet att kombinera körningsmodeller, som du kan se i [figur 7](#Fig7).

<a name="Fig7"></a>![07_CombineTechnologies](./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png)

**Figur 7: Ett enskilt program kan använda flera körningsmodeller.**

Som figuren visar körs virtuella datorer i Molntjänster i en separat molntjänst, avskilt från de virtuella datorerna i Virtuella datorer. De två kan ändå kommunicera ganska effektivt, så att bygga ett program på det här sättet är ibland det bästa alternativet.

Azure erbjuder olika körningsmodeller eftersom molnplattformar måste ha stöd för många olika scenarier. Alla som vill använda den här plattformen på ett effektivt sätt -- och om du har läst ända hit, så inkluderar det troligtvis dig -- måste förstå var och en av dem.



[1]: http://vmdepot.msopentech.com/
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dn197896.aspx