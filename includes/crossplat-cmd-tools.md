# <a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Använda Azures kommandoradsverktyg för Mac och Linux
Här beskrivs hur du använder Azures kommandoradsverktyg för Mac och Linux för att skapa och hantera tjänster i Azure. Du kan bland annat läsa om hur du **installerar verktygen**, **importerar dina publiceringsinställningar**, **skapar och hanterar Azure Websites** och **skapar och hanterar Azure Virtual Machines**. Omfattande referensdokumentation finns i [Dokumentation för Azure-kommandoradsverktyg för Mac och Linux][reference-docs]. 

## <a name="table-of-contents"></a>Innehållsförteckning
* [Om Azures kommandoradsverktyg för Mac och Linux](#Overview)
* [Installera Azures kommandoradsverktyg för Mac och Linux](#Download)
* [Skapa ett Azure-konto](#CreateAccount)
* [Hämta och importera publiceringsinställningar](#Account)
* [Skapa och hantera en Azure-webbplats](#WebSites)
* [Skapa och hantera en virtuell Azure-dator](#VMs)

<h2><a id="Overview"></a>Om Azures kommandoradsverktyg för Mac och Linux</h2>

Azures kommandoradsverktyg för Mac och Linux är en uppsättning kommandoradsverktyg för distribution och hantering av Azure-tjänster.

Du kan till exempel:

* Importera publiceringsinställningar.
* Skapa och hantera Azure Websites.
* Skapa och hantera Azure Virtual Machines.

Om du vill se en fullständig lista över kommandon som stöds kan du skriva `azure -help` på kommandoraden (efter att du har installerat verktygen) eller läsa [referensdokumentationen][reference-docs].

<h2><a id="Download">Installera Azures kommandoradsverktyg för Mac och Linux</a></h2>

Följande lista innehåller information om hur du installerar kommandoradsverktyg, beroende på vilket operativsystem du har:

* **Mac**: Hämta [Azure SDK Installer][mac-installer]. Öppna den hämtade PKG-filen och slutför installationsstegen.
* **Linux**: Installera den senaste versionen av [Node.js][nodejs-org] (se [Installera Node.js via Package Manager][install-node-linux]) och kör sedan följande kommando:
  
        npm install azure-cli -g
  
    **Obs!** Du kan behöva köra det här kommandot med utökade privilegier:
  
        sudo npm install azure-cli -g
* **Windows**: Kör Winows Installer (MSI-fil), som finns här: [Azures kommandoradsverktyg][windows-installer].

Testa installationen genom att skriva `azure` i kommandotolken. När installationen är klar visas en lista över alla tillgängliga `azure`-kommandon.

<h2><a id="CreateAccount"></a>Skapa ett Azure-konto</h2>

Du behöver ett Azure-konto för att kunna använda Azures kommandoradsverktyg för Mac och Linux.

Öppna en webbläsare och gå till [http://www.windowsazure.com][windowsazuredotcom] och klicka på **kostnadsfritt konto** längst upp till höger.

![Azure-webbplats][Azure Web Site]

Följ instruktionerna för att skapa ett konto.

<h2><a id="Account"></a>Hämta och importera publiceringsinställningar</h2>

Innan du kan börja måste du hämta och importera dina publiceringsinställningar. Det krävs för att du ska kunna skapa och hantera Azure-tjänster med verktygen. Hämta dina publiceringsinställningar med `account download`-kommandot:

    azure account download

Din standardwebbläsare öppnas och du uppmanas att logga in på hanteringsportalen. När du har loggat in hämtas `.publishsettings`-filen. Notera var filen sparas.

Sedan importerar `.publishsettings`-filen genom att köra följande kommando (ersätt `{path to .publishsettings file}` med sökvägen till din `.publishsettings`-fil):

    azure account import {path to .publishsettings file}

Du kan ta bort all information som lagras av <code>import</code>-kommandot med hjälp av <code>account clear</code>-kommandot:

    azure account clear

Om du vill visa en lista över alternativ för `account`-kommandon kan du använda alternativet `-help`:

    azure account -help

När du har importerat publiceringsinställningarna bör du ta bort filen `.publishsettings` av säkerhetsskäl.

> [!NOTE]
> När du importerar publiceringsinställningar sparas inloggningsuppgifter för din Azure-prenumeration i mappen `user`. Mappen `user` skyddas av operativsystemet. Men vi rekommenderar att du vidtar ytterligare åtgärder för att kryptera mappen `user`. Så här kan du göra det:    
> 
> * I Windows ändrar du mappegenskaperna eller använder BitLocker.
> * I Mac aktiverar du FileVault för mappen.
> * I Ubuntu använder du katalogfunktionen Encrypted Home. Andra Linux-distributioner som har motsvarande funktioner.
> 
> 

Nu kan du börja skapa och hantera Azure Websites och Azure Virtual Machines.  

<h2><a id="WebSites"></a>Skapa och hantera en Azure-webbplats</h2>

### <a name="create-a-website"></a>Skapa en webbplats
Om du vill skapa en Azure-webbplats börjar du med att skapa en tom katalog med namnet `MySite` och öppnar den katalogen.

Kör sedan följande kommando:

    azure site create MySite --git

Utdata från det här kommandot innehåller standard-URL för den nya webbplatsen. Med alternativet `--git` kan du använda git för att publicera till din webbplats genom att skapa git-arkiv både i din lokala programkatalog och i webbplatsens datacenter. Observera att om din lokala mapp redan är en git-lagringsplats kommer kommandot att lägga till en ny fjärrlagringsplats till den befintliga lagringsplatsen som pekar till lagringsplatsen på din webbplats datacenter.

Observera att du kan köra `azure site create`-kommandot med följande alternativ:

* `--location [location name]`. Med det här alternativet kan du ange platsen för det datacenter som din webbplats skapas i (t.ex. USA, västra). Om du utelämnar det här alternativet ombeds du välja en plats.
* `--hostname [custom host name]`. Med det här alternativet kan du ange ett anpassat värdnamn för webbplatsen.

Du kan sedan lägga till innehåll i din webbplatskatalog. Använd ditt vanliga git-flöde (`git add`, `git commit`) för att spara (eng. ”commit”) ditt innehåll. Du kan använda git-kommandot för att överföra (eng. ”push”) ditt webbplatsinnehåll till Azure: 

    git push azure master

### <a name="set-up-publishing-from-github"></a>Konfigurera publicering från GitHub
Om du vill konfigurera kontinuerlig publicering från en GitHub-lagringsplats använder du alternativet `--GitHub` när du skapar en webbplats:

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Om du har en lokal klon av en GitHub-lagringsplats eller om du har en lagringsplats med en enskild fjärreferens till en GitHub-lagringsplats publiceras koden automatiskt med det här kommandot i GitHub-lagringsplatsen till din webbplats. Därefter publiceras alla ändringar som skickas till GitHub-lagringsplatsen automatiskt på webbplatsen.

När du konfigurerar publicering från GitHub används mastergrenen (eng. ”master branch”) som standardgren. Om du vill ange en annan gren kör du följande kommando från din lokala lagringsplats:

    azure site repository <branch name>

### <a name="configure-app-settings"></a>Konfigurera appinställningar
Appinställningarna är nyckel/värde-par som är tillgängliga för ditt program under körning. När appinställningsvärden har angetts för en Azure-webbplats åsidosätter de inställningar med samma nyckel som har definierats i filen Web.config för webbplatsen. Appinställningar är tillgängliga som miljövariabler för Node.js- och PHP-program. I följande exempel visas hur du installerar ett nyckel/värde-par:

    azure site config add <key>=<value> 

Använd följande om du vill visa en lista över alla nyckel/värde-par:

    azure site config list 

Om du känner till nyckeln och vill visa värdet kan du använda:

    azure site config get <key> 

Om du vill ändra värdet för en befintlig nyckel måste du först radera den befintliga nyckeln och sedan lägga till den på nytt. Kommandot för att radera är:

    azure site config clear <key> 

### <a name="list-and-show-sites"></a>Visa platser
Om du vill visa dina webbplatser använder du följande kommando:

    azure site list

Om du vill ha detaljerad information om en webbplats kan använda kommandot `site show`. I följande exempel visas information om `MySite`:

    azure site show MySite

### <a name="stop-start-or-restart-a-site"></a>Stoppa, starta eller starta om en webbplats
Du kan stoppa, starta eller starta om en plats med kommandot `site stop`, `site start`, eller `site restart`:

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

### <a name="delete-a-site"></a>Ta bort en webbplats
Du kan ta bort en webbplats med kommandot `site delete`:

    azure site delete MySite

Observera att om du kör något av kommandona som anges ovan från den mapp där du körde `site create` så behöver du inte ange webbplatsnamnet `MySite` som sista parameter.

Om du vill visa en fullständig lista över `site`-kommandon använder du alternativet `-help`:

    azure site -help 

<h2><a id="VMs"></a>Skapa och hantera en virtuell Azure-dator</h2>

en virtuell Azure-dator skapas från en avbildning av en virtuell dator (en VHD-fil) som du tillhandahåller eller som finns i galleriet med avbildningar. Du kan visa tillgängliga avbildningar med kommandot `vm image list`:

    azure vm image list

Du kan etablera och starta en virtuell dator från någon av de tillgängliga avbildningarna med kommandot `vm create`. I följande exempel visas hur du skapar en virtuell Linux-dator (kallas `myVM`) från en avbildning i galleriet (CentOS 6.2). Namn och lösenord för rotanvändaren för den virtuella datorn är `myusername` och `Mypassw0rd`. (Observera att parametern `--location` anger det datacenter som den virtuella datorn skapas i. Om du utelämnar parametern `--location` ombeds du välja en plats.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Du kan skicka `--ssh`-flaggan (Linux) eller `--rdp`-flaggan (Windows) till `vm create` om du vill aktivera fjärranslutningar till den virtuella dator som du nyss har skapat.

Om du hellre vill etablera en virtuell dator från en anpassad avbildning kan du skapa en avbildning från en VHD-fil med kommandot `vm image create` och sedan använda kommandot `vm create` för att etablera den virtuella datorn. I följande exempel visas hur du skapar en Linux-avbildning (kallas `myImage`) från en lokal VHD-fil. (Parametern `--location` anger data som avbildningen lagras i.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Du kan skapa en avbildning från en VHD-fil som lagras i Azure Blob Storage i stället för att skapa en avbildning från en lokal VHD-fil. Det kan du göra med parametern `blob-url`:

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

När du har skapat en avbildning kan du etablera en virtuell dator från avbildningen med `vm create`. Kommandot nedan skapar en virtuell dator med namnet `myVM` från den avbildning som skapades ovan (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

När du har etablerat en virtuell dator kanske du vill skapa slutpunkter för att tillåta fjärråtkomst till den virtuella datorn (till exempel). I följande exempel används kommandot `vm create endpoint` för att öppna extern port 22 och lokal port 22 på `myVM`:

    azure vm endpoint create myVM 22 22

Du kan få detaljerad information om en virtuell dator (inklusive IP-adress, DNS-namn och slutpunkt) med kommandot `vm show`:

    azure vm show myVM

Om du vill stänga av, starta eller starta om den virtuella datorn använder du något av följande kommandon:

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Och om du vill ta bort den virtuella datorn använder kommandot `vm delete`:

    azure vm delete myVM

Om du vill ha en fullständig lista över kommandon för att skapa och hantera virtuella datorer använder du alternativet `-h`:

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com

