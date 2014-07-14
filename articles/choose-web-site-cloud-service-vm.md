<properties  linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Web Sites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

# Jämförelse mellan Azure Web Sites, Molntjänster och Virtuella datorer

Azure erbjuder flera tjänster som kan vara värd för dina webbprogram, däribland [Azure Web Sites][1], [Molntjänster][2] och [Virtuella datorer][3]. När du har tittat på dessa olika alternativ kanske du är osäker på vilket som passar dig bäst, eller så kanske du inte känner dig helt säker på begrepp som IaaS och PaaS. Den här artikeln hjälper dig att förstå de olika alternativen och göra rätt val för ditt webbscenario. Även om alla tre av dessa alternativ gör det möjligt att köra mycket skalbara webbprogram i Azure, finns det skillnader som kan hjälpa dig att göra ett val.

I många fall är Azure Web Sites det bästa alternativet. Tjänsten tillhandahåller enkla och flexibla alternativ för distribution och hantering och har kapaciteten att vara värd för webbplatser med hög volym. Du kan snabbt skapa en ny webbplats med populära program, t.ex. WordPress, från galleriet för webbprogram eller flytta en befintlig webbplats till Azure Web Sites. Genom att använda [Azure WebJobs SDK][4] (som för närvarande finns i en förhandsversion) kan du även lägga till bearbetning av bakgrundsjobb.

Du har också alternativet att låta Azure Molntjänster eller Azure Virtuella datorer vara värd för dina webbprogram. Dessa alternativ är bra när din webbnivå kräver ökade nivåer för kontroll och anpassning som finns i dessa alternativ. Den utökade kontrollen kommer dock till ett pris av större komplexitet när det kommer till att skapa, hantera och distribuera program. Diagrammet nedan visar en jämförelse mellan de tre alternativen.

![ChoicesDiagram](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

Web Sites är enklare att konfigurera, hantera och övervaka, men du har färre konfigurationsalternativ. Huvudpoängen är att om du inte har en övertygande anledning till att lägga webbklientdelen på Molntjänster eller Virtuella datorer, bör du använda Azure Web Sites. Resten av det här dokumentet innehåller information som du behöver för att fatta ett väl underbyggt beslut. Det innefattar:

* [Scenarier](#scenarios)
* [Sammanfattning av tjänster](#services)
* [Jämförelse av funktioner](#features)

## <a name="scenarios"></a>Scenarier

### Jag äger ett litet företag och behöver en billig värdtjänst för min webbplats, men med framtida tillväxt i åtanke.

Azure Web Sites (WAWS) är en jättebra lösning för det här scenariot eftersom du kan börja använda tjänsten kostnadsfritt och sedan lägga till fler funktioner när du behöver dem. Med varje kostnadsfri webbplats får du exempelvis en domän som tillhandahålls av Azure (*ditt_företag*.azurewebsites.net). När du är redo att börja använda en egen domän kan du lägga till detta för så lite som 9,80 USD i månaden
(från och med januari 2014). Det finns många andra tjänster och skalningsalternativ som gör det möjligt för webbplatsen att utvecklas i takt med ökade krav från användarna. Med **Azure Web Sites** kan du:

* Börja på en kostnadsfri nivå och sedan skala upp när det behövs.
* Använda programgalleriet för att snabbt konfigurera populära
  webbprogram, t.ex. WordPress.
* Lägga till ytterligare Azure-tjänster och funktioner i ditt program
  efter behov.
* Säkra din webbplats med HTTPS genom att använda certifikatet som
  tillhandahålls tillsammans med
  *ditt_företag*.azurewebsites.net-domännamnet.

### Jag är en webbdesigner eller grafisk formgivare och vill designa och bygga webbplatser för mina kunder

Azure Web Sites ger dig som webbutvecklare det du behöver för att skapa sofistikerade webbprogram. Web Sites erbjuder en tät integrering med verktyg som Visual Studio och SQL-databas. Med **Web Sites** kan utvecklare:

* Använda kommandoradsverktyg för [automatiska uppgifter][5].
* Arbeta med populära språk som [.Net][6], [PHP][7], [Node.js][8] och
  [Python][9].
* Välj tre olika skalningsnivåer för att skala upp till en väldigt hög
  kapacitet.
* Integrera med andra Azure-tjänster, t.ex. [SQL-databas][10], [Service
  Bus][11] och [Lagring][12], eller partnererbjudanden från [Azure
  Store][13], t.ex. MySQL och MongoDB.
* Integrera med verktyg som Visual Studio, Git, WebMatrix, WebDeploy,
  TFS och FTP.

### Jag migrerar ett flerskiktat program med en webbklientdel till molnet

Om du kör ett flerskiktat program, t.ex. en webbserver som pratar med en databasserver för att lagra och hämta webbplatsdata, har du flera alternativ i Azure. Dessa arkitekturalternativ innefattar Web Sites, Molntjänster och Virtuella datorer. För det första är **Web Sites** ett bra alternativ för lösningens webbskikt och kan användas med Azure SQL-databas för att skapa en arkitektur med två skikt. Med Web Sites kan du dessutom köra bakgrundsprocesser eller processer som körs länge med hjälp av förhandsversionen av Azure WebJobs SDK. Om du behöver en mer avancerad arkitektur eller flexiblare skalningslaternativ är Molntjänster eller Virtuella datorer ett bättre val.

**Med Molntjänster** kan du:

* Vara värd för webb-, mellanskikts- och serverdelstjänster på skalbara
  Web- och Worker-roller.
* Vara värd för enbart mellanskikts- och serverdelstjänster på
  Worker-roller och behålla klientdelen på Azure Web Sites.
* Skala klientdel- och serverdelstjänster oberoende av varandra.

**Med Virtuella datorer** kan du:

* På ett lättare sätt migrera mycket anpassningsbara miljöer i form av
  en avbildning för virtuella datorer.
* Köra programvara eller tjänster som inte kan konfigureras på Web Sites
  eller Molntjänster.

### Mitt program är beroende av mycket anpassningsbara Windows- eller Linux-miljöer

Om ditt program kräver avancerad installation eller konfiguration av programvara och operativsystem är Virtuella datorer troligtvis den bästa lösningen. Med **Virtuella datorer** kan du:

* Använda galleriet för virtuella datorer och börja med ett
  operativsystem, exempelvis Windows eller Linux, och sedan anpassa det
  enligt kraven för ditt program.
* Skapa och ladda upp en egen avbildning av en befintlig lokal server
  och köra den på en virtuell dator i Azure.

### Min webbplats använder programvara med öppen källkod och jag vill att Azure ska vara värd för den

Alla tre alternativ låter dig vara värd för språk och ramverk med öppen källkod. **Molntjänster** kräver att du använder startuppgifter för att installera och konfigurera all programvara med öppen källkod som krävs och som kan köras i Windows. Med **Virtuella datorer** installerar och konfigurerar du programvaran på datoravbildningen, som kan vara både Windows- och Linux-baserad. Om det finns stöd för ditt ramverk med öppen källkod i Web Sites, är detta en enklare metod för att vara värd för dessa typer av program eftersom Web Sites kan konfigureras automatiskt med de språk och ramverk som ditt program behöver. **Med Web Sites** kan du:

* Använda många populära språk med öppen källkod, exempelvis [.Net][6],
  [PHP][7], [Node.js][8] och [Python][9].
* Konfigurera WordPress, Drupal, Umbraco, DNN och många andra
  webbprogram från tredje part.
* Migrera ett befintligt program eller skapa ett nytt från
  programgalleriet.

### Jag har en affärsapplikation som behöver ansluta till ett företagsnätverk

Om du vill skapa en affärsapplikation kan din webbplats behöva ha direkt åtkomst till tjänster eller data på företagets nätverk. Detta är möjligt med **Web Sites**, **Molntjänster** och **Virtuella datorer**. Det finns skillnader mellan de olika metoderna, däribland följande:

* Web Sites kan på ett säkert sätt ansluta till lokala resurser genom
  att använda ett Service Bus-relä. Detta gör det möjligt för tjänster
  på företagets nätverk att utföra uppgifter åt webbprogrammet utan att
  du behöver flytta allting till molnet eller konfigurera ett virtuellt
  nätverk.
* Molntjänster och Virtuella datorer kan utnyttja Virtuellt nätverk.
  Virtuellt nätverk gör det i praktiken möjligt för datorer som körs i
  Azure att ansluta till ett lokalt nätverk på plats. Azure blir då en
  förlängning av företagets datacenter.

### Jag vill vara värd för ett REST API eller en webbtjänst för mobila klienter

Med HTTP-baserade webbtjänster kan du erbjuda stöd för en lång rad olika klienter, däribland mobila klienter. Ramverk som ASP.NET Webb-API integreras med Visual Studio för att göra det enklare att skapa och använda REST-tjänster. De här tjänsterna görs tillgängliga från en webbslutpunkt, så det är möjligt att använda vilken webbvärdsteknik som helst på Azure för det här scenariot. **Web Sites** är dock ett bra val om du vill vara värd för REST API:er. Med Web Sites kan du:

* Snabbt skapa en webbplats där du kan vara värd för HTTP-webbtjänsten i
  ett av Azures globalt distribuerade datacenter.
* Migrera befintliga tjänster eller skapa nya, och möjligtvis utnyttja
  ASP.NET Webb-API i Visual Studio.
* Få ett SLA för tillgänglighet med en enkel instans eller skala ut till
  flera dedikerade datorer.
* Använda den publicerade webbplatsen för att erbjuda REST API:er till
  HTTP-klienter, inklusive mobila klienter.

## <a name="services"></a>Sammanfattning av tjänster

Med [Azure Web Sites][1] kan du snabbt bygga mycket skalbara webbplatser på Azure. Du kan använda Azure-portalen eller kommandoradsverktygen för att konfigurera populära språk som .NET, PHP, Node.js och Python på en webbplats. Ramverken som stöds är redan distribuerade och kräver inga fler installationssteg. Azure Web Sites-galleriet innehåller många tredje parts program som Drupal och WordPress samt utvecklingsramverk som Django och CakePHP. När du har skapat en webbplats kan du antingen migrera en befintlig webbplats eller bygga en helt ny webbplats. Web Sites eliminerar behovet av att hantera den fysiska maskinvaran och erbjuder dessutom flera skalningsalternativ. Du kan gå från en delad modell med flera innehavare till ett standardläge där dedikerade datorer står till tjänst för inkommande trafik. Med Web Sites kan du även integrera med andra Azure-tjänster som SQL-databas, Service Bus och Lagring. Genom att använda förhandsversionen av [Azure WebJobs SDK][4] kan du lägga till bakgrundsbearbetning. Sammanfattningsvis gör Azure Web Sites att det blir lättare att fokusera på programutveckling genom att ha stöd för en lång rad olika språk, program med öppen källkod och distributionsmetoder (FTP, Git, Web Deploy eller TFS). Om du inte har några speciella krav som kräver Molntjänster eller Virtuella datorer, är Azure Web Sites troligtvis det bästa valet.

[Molntjänster][2] gör det möjligt för dig att skapa mycket tillgängliga och skalbara webbprogram i en rik PaaS-miljö (plattform som en tjänst). Till skillnad från Web Sites skapas en molntjänst först i en utvecklingsmiljö, exempelvis Visual Studio, innan den distribueras till Azure. Ramverk, t.ex. PHP, kräver anpassade distributionssteg eller uppgifter som installerar ramverket när rollen startar. Den största fördelen med Molntjänster är möjligheten till stöd för mer avancerade flerskiktiga arkitekturer. En enskild molntjänst kan bestå av en Web-roll för klientdelen och en eller flera Worker-roller. De olika nivåerna kan skalas oberoende av varandra. Det finns även ökad kontroll över infrastrukturen för dina webbprogram. Du kan till exempel fjärransluta till skrivbordet på datorerna som kör rollinstanserna. Du kan även göra skript för mer avancerade ändringar i IIS och datorkonfigurationen som körs när rollen startar, inklusive uppgifter som kräver administratörskontroll.

[Virtuella datorer][3] gör det möjligt för dig att köra webbprogram på virtuella datorer i Azure. Den här funktionen kallas även IaaS (infrastruktur som en tjänst). Skapa nya Windows Server- eller Linux-datorer i portalen eller ladda upp en befintlig avbildning av en virtuell dator. Virtuella datorer ger dig störst kontroll över operativsystemet, konfigurationen och installerade program och tjänster. Det här är ett bra val för att snabbt migrera avancerade lokala webbprogram till molnet eftersom datorerna kan flyttas som en helhet. Med Virtuellt nätverk kan du dessutom ansluta dessa virtuella datorer till lokala företagsnätverk. Precis som med Molntjänster har du fjärråtkomst till dessa datorer och möjligheten att utföra konfigurationsändringar på administrativ nivå. Men till skillnad från Web Sites och Molntjänster måste du själv hantera dina avbildningar av virtuella datorer och programarkitekturen helt på infrastruktursnivå. Ett enkelt exempel är att du själv måste köra uppdateringar på operativsystemet.

## <a name="features"></a>Jämförelse av funktioner

Tabellen nedan jämför funktionerna i Web Sites, Molntjänster och Virtuella datorer för att hjälpa dig att göra ett så bra val som möjligt. Rutorna med en asterisk förklaras i närmare detalj i anmärkningarna efter tabellen.

<table  cellspacing="0" border="1">
<tr>
   <th  align="left" valign="middle">Funktion</th>

   <th  align="left" valign="middle">Web Sites</th>

   <th  align="left" valign="middle">Molntjänster (Web-roller)</th>

   <th  align="left" valign="middle">Virtuella datorer</th>

</tr>

<tr>
   <td  valign="middle"><p>Tillgång till tjänster som Service Bus, Lagring och SQL-databas</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Skikt för värdwebbplats eller webbtjänster i en flerskiktad arkitektur</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Mellanskikt för värd i en flerskiktad arkitektur</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Integrerat stöd för MySQL-as-a-service</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>1</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Stöd för ASP.NET, Classic ASP, Node.js, PHP, Python</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Skala ut till flera instanser utan att distribuera på nytt</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>2</sup>
</td>

</tr>

<tr>
   <td  valign="middle"><p>Stöd för SSL</p>
</td>

   <td  valign="middle">X <sup>3</sup>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Visual Studio-integrering</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Fjärrbaserad felsökning</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Distribuera kod med TFS</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Distribuera kod med GIT, FTP</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Distribuera kod med Web Deploy</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle"><sup>4</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Stöd för WebMatrix</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Nästintill direkt distribution</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Instanser delar innehåll och konfiguration</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Skala upp till större datorer utan att distribuera på nytt</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Flera distributionsmiljöer (produktion och mellanlagring)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Nätverksisolering med Azure Virtuellt nätverk</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Stöd för Azure Traffic Manager</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Fjärråtkomst till servrarna</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Möjlighet att definiera/köra startuppgifter</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Automatisk hantering av OS-uppdateringar</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Integrerad slutpunktsövervakning</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Smidig växling av plattform (32-bit/64-bit)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

</table>

<sup>1</sup>Web- eller Worker-roller kan integrera MySQL-as-a-service genom ClearDB:s erbjudanden, men inte som del av arbetsflödet i hanteringsportalen

<sup>2</sup> Även om Virtuella datorer kan skala ut till flera instanser måste tjänsterna som körs på dessa datorer vara skrivna för hantera denna utskalning. Ytterligare belastningsutjämning måste konfigureras för att dirigera förfrågningar mellan datorerna. Till sist bör en tillhörighetsgrupp skapas för alla datorer som deltar i samma roll för att skydda dem mot samtidiga omstarter till följd av underhåll eller maskinvarufel.

<sup>3</sup> I Web Sites stöds SSL för anpassade domännamn endast för standardläge. Mer information om att använda SSL med Web Sites finns på [Konfigurera ett SSL-certifikat för en Azure Web Site][14].

<sup>4</sup> Det finns stöd för Web Deploy i molntjänster när du distribuerar enkelinstansroller. Produktionsroller kräver dock flera instanser för att uppfylla Azures SLA. Därför är Web Deploy inte en lämplig distributionsmekanism för molntjänster under produktion.



[1]: http://go.microsoft.com/fwlink/?LinkId=306051
[2]: http://go.microsoft.com/fwlink/?LinkId=306052
[3]: http://go.microsoft.com/fwlink/?LinkID=306053
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[5]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites
[6]: http://www.windowsazure.com/en-us/develop/net/
[7]: http://www.windowsazure.com/en-us/develop/php/
[8]: http://www.windowsazure.com/en-us/develop/nodejs/
[9]: http://www.windowsazure.com/en-us/develop/python/
[10]: http://www.windowsazure.com/en-us/documentation/services/sql-database/
[11]: http://www.windowsazure.com/en-us/documentation/services/service-bus/
[12]: http://www.windowsazure.com/en-us/documentation/services/storage/
[13]: http://www.windowsazure.com/en-us/gallery/store/
[14]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/