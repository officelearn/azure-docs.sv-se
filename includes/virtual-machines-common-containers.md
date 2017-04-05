Azure-molnlösningar bygger på virtuella datorer (emulering av fysisk datormaskinvara), vilket möjliggör flexibel förpackning av programvarudistributioner och bättre resurskonsolidering än med fysisk maskinvara. [Docker](https://www.docker.com)-behållare och docker-ekosystem har på ett dramatiskt sätt utvidgat möjligheterna att utveckla, leverera och hantera distribuerade program. Programkod i en behållare är isolerad från den virtuella värddatorn och andra behållare på samma virtuella dator. Denna isolering ger mer flexibilitet vid utveckling och distribution.

Azure erbjuder följande Docker-värden:

* [Många](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) [olika](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) sätt att skapa Docker-värdar för behållare så att de passar din situation
* [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) skapar kluster av behållarvärdar med hjälp av dirigerare som **marathon** och **swarm**.
* [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) och [resursgruppsmallar](../articles/resource-group-authoring-templates.md) för att förenkla distribution och uppdatering av komplexa distribuerade program
* integrering med en stor matris av verktyg för konfigurationshantering med både upphovsrättsskyddad och öppen källkod

Och eftersom du programmässigt kan skapa virtuella datorer och Linux-behållare på Azure kan du även använda *dirigeringsvertyg* för virtuella datorer och behållare och skapa grupper med virtuella datorer och distribuera program i Linux-behållare och nu [Windows-behållare](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

I den här artikeln beskrivs inte bara dessa koncept på hög nivå, utan här finns även en mängd länkar till mer information, självstudier och produkter som hör till behållar- och klusteranvändning på Azure. Om du känner till allt detta och bara vill ha länkarna finns de här i [verktyg för att arbeta med behållare](#tools-for-working-with-azure-vms-and-containers).

## <a name="the-difference-between-virtual-machines-and-containers"></a>Skillnaden mellan virtuella datorer och behållare
Virtuella datorer körs i en isolerad maskinvaruvirtualiseringsmiljö som tillhandahålls av ett [hypervisor-program](http://en.wikipedia.org/wiki/Hypervisor). I Azure hanterar tjänsten [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) allt detta åt dig: Du skapar virtuella datorer genom att välja operativsystemet och konfigurera &mdash;eller genom att överföra en egen anpassad VM-avbildning. Virtual Machines är en tidstestad och beprövad teknik och det finns många tillgängliga verktyg för att hantera operativsystem och konfigurera program i VM.  Appar i en virtuell dator är dolda för värdoperativsystemet. För de som använder den virtuella datorn och för programmen ser den virtuella datorn ut som en autonom fysisk dator.

[Linux-behållare](http://en.wikipedia.org/wiki/LXC) och de som skapas och hanteras med hjälp av Docker-verktyg använder inte ett hypervisor-program för isolering. När det gäller behållare använder behållarvärden process- och filsystemsisoleringsfunktionerna i Linux-kerneln för att exponera behållaren, dess program, vissa kernelfunktioner och dess egna isolerade filsystem. Behållaren verkar vara en unik operativsystemsinstans för en app som körs i en behållare. En innesluten app kan inte se processer eller andra resurser utanför dess behållare.

Mycket färre resurser används i en Docker-behållare än i en virtuell dator. Docker-behållare använder en modell för isolering och körning av program som inte delar Docker-värdens kernel. Behållaren tar mycket mindre diskutrymme eftersom den inte innehåller hela operativsystemet. Starttiden och det diskutrymme som krävs är betydligt lägre än i en virtuell dator.
Windows-behållare ger samma fördelar som Linux-behållare för appar som körs i Windows. Windows-behållare har stöd för Docker-avbildningsformatet och Docker API, men de kan också hanteras med hjälp av PowerShell. Två behållarkörningar är tillgängliga med Windows-behållare: Windows Server-behållare och Hyper-V-behållare. Hyper-V-behållare ger ett extra lager av isolering genom värdhantering av varje behållare i en superoptimerad virtuell dator. Mer information om Windows-behållare finns i avsnittet [About Windows Containers](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) (Om Windows-behållare). Om du vill komma igång med Windows-behållare i Azure bör du lära dig att [distribuera ett kluster med Azure Container Service](/articles/container-service/container-service-deployment.md).

## <a name="what-are-containers-good-for"></a>Vad är behållare bra för?

Behållare kan förbättra:

* hur snabbt programkod kan utvecklas och delas brett
* hur snabbt och tillförlitligt en app kan testas
* hur snabbt och tillförlitligt en app kan distribueras

Behållare körs på en behållarvärd&mdash;ett operativsystem, och i Azure innebär det en virtuell Azure-dator. Även om du redan gillar tanken på behållare kommer du fortfarande att behöva en virtuell datorinfrastruktur som fungerar som värd för behållarna, men fördelen är att det för behållarna inte spelar någon roll på vilken virtuell dator de körs (även om det kan spela roll att exempelvis en Linux- eller Windows-körningsmiljö passar bäst för behållaren).


## <a name="what-are-containers-good-for"></a>Vad är behållare bra för?
De fungerar bra för många saker, men de uppmuntrar&mdash;precis som [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) och [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)&mdash;skapandet av mikrotjänstinriktade distribuerade program med en tjänst, där programmets utformning bygger fler små, sammansättningsbara delar i stället för större och mer starkt sammanfogade komponenter.

Detta gäller särskilt i miljöer med ett offentligt moln som Azure, där du hyr virtuella datorer när och var du vill. Du får inte bara isolering och snabba distributions- och dirigeringsverktyg, utan kan även ta mer effektiva beslut om programinfrastrukturen.

Du kan till exempel för närvarande ha en distribution som består av nio virtuella Azure-datorer med stor storlek för högtillgänglig, distribuerad tillämpning. Om komponenterna i det här programmet kan distribueras i behållare, kanske du bara kan använda fyra virtuella datorer och distribuera programkomponenterna i 20 behållare för redundans och belastningsutjämning.

Detta är bara ett exempel förstås, men om du kan göra detta i ditt scenario kan du justera användningstopparna med fler behållare i stället för flera virtuella Azure-datorer och använda återstående total processorbelastning på ett mycket mer effektivt sätt än tidigare.

Dessutom finns många scenarier som inte lämpar sig för ett mikrotjänstperspektiv; du vet själv bäst om mikrotjänster och behållare kan vara till nytta för dig.

### <a name="container-benefits-for-developers"></a>Behållarfördelar för utvecklare
I allmänhet är det enkelt att se att behållartekniken innebär ett steg framåt, men det finns även mer specifika fördelar. Låt oss ta Docker-behållare som ett exempel. Det här avsnittet kommer utförligt beskriva Docker just nu (läs [Vad är Docker?](https://www.docker.com/whatisdocker/) om du vill ta reda på det, eller [wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)), men Docker och dess ekosystem ger enorma fördelar både utvecklare och IT-proffs.

Utvecklare tar snabbt till Docker-behållare, eftersom de framför allt gör det enkelt att använda Linux- och Windows-behållare:

* De kan använda enkla, inkrementella kommandon för att skapa en fast avbildning som är lätt att distribuera och kan automatisera och bygga dessa avbildningar med hjälp av en docker-fil
* De kan enkelt dela dessa avbildningar med okomplicerade, [git](https://git-scm.com/)-lika push- och pull-kommandon till [offentliga](https://registry.hub.docker.com/) eller [privata docker-register](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* De kan tänka på isolerade programkomponenter i stället för datorer
* De kan använda ett stort antal verktyg som fungerar bra med Docker-behållare och olika grundläggande avbildningar

### <a name="container-benefits-for-operations-and-it-professionals"></a>Fördelar med behållare för verksamheter och IT-tekniker
IT- och drifttekniker kan också ha nytta av kombinationen av behållare och virtuella datorer.

* inneslutna tjänster är isolerade från den virtuella datorns värdkörningsmiljö
* den inneslutna koden är verifierbart identisk
* inneslutna tjänster kan startas, stoppas och snabbt flyttas mellan utvecklings-, testnings- och produktionsmiljöerna

Funktioner som dessa &mdash;och det finns flera&mdash; är intressanta för etablerade verksamheter där professionella IT-organisationer har fått uppgiften att anpassa resurserna&mdash;inklusive ren processorkraft&mdash; efter de aktiviteter som krävs för att inte bara hålla igång verksamheten, utan också för att öka kundnöjdheten och räckvidden. Småföretag, oberoende programvaruleverantörer och nystartade företag har exakt samma krav, men de kan beskriva det på olika sätt.

## <a name="what-are-virtual-machines-good-for"></a>Vad är virtuella datorer bra för?
Virtuella datorer utgör navet i molndatorhanteringen och detta förändras inte. Även om virtuella datorer startar långsammare, har ett större diskutrymmeskrav och mappar inte direkt till en mikrotjänstarkitektur, har de mycket viktiga fördelar:

1. Som standard har de mycket mer stabila standardsäkerhetsskydd för värddatorn
2. De har stöd för alla viktiga operativsystems- och programinställningar
3. De har långvariga verktygsekosystem för kommando och kontroll
4. De tillhandahåller en körningsmiljö åt värdbehållare

Den sistnämnda egenskapen är viktig, eftersom ett inneslutet program fortfarande kräver ett specifikt operativsystem och en viss processortyp, beroende på de anrop programmet kommer att göra. Det är viktigt att komma ihåg att du installerar behållare på virtuella datorer eftersom de innehåller de program som du vill distribuera; behållare utgör inte ersättningar för virtuella datorer eller operativsystem.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>Utförlig funktionsjämförelse av virtuella datorer och behållare
I följande tabell beskrivs på en mycket hög nivå vilken typ av funktionsskillnader det finns mellan virtuella datorer och Linux-behållare utan &mdash;att man behöver utföra mycket extra arbete&mdash;. Observera att vissa funktioner kan vara mer eller mindre önskvärda beroende på ditt programbehov, och att precis som med all programvara, ger extra arbete ökat funktionsstöd, särskilt när det kommer till säkerhet.

| Funktion | Virtuella datorer | Behållare |
|:--- | --- | --- |
| Stöd för standardsäkerhet |i större utsträckning |i något mindre utsträckning |
| Minnesutrymme på disken som krävs |Fullständigt operativsystem plus appar |Enbart appkrav |
| Den tid det tar att starta upp |Betydligt längre: Start av operativsystem plus appinläsning |Avsevärt kortare: Endast appar behöver starta eftersom kerneln redan körs |
| Portabilitet |Portabilitet med rätt förberedelse |Portabel inom avbildningsformatet; normalt mindre |
| Automatisering av avbildningar |Varierar mycket beroende på operativsystem och appar |[Docker-registret](https://registry.hub.docker.com/); andra |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>Skapa och hantera grupper av virtuella datorer och behållare
I det här läget kan en arkitekt, utvecklare eller IT-specialist kanske tänka: ”Jag kan automatisera ALLT detta; det här ÄR verkligen datacenter som en tjänst!”.

Du har rätt, det kan det vara, och det finns många system som du kanske redan använder som antingen kan hantera grupper av virtuella datorer på Azure och mata in anpassad kod med skript, ofta med [CustomScriptingExtension för Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) eller [CustomScriptingExtension för Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/). Du kan&mdash;och kanske redan har&mdash;automatiserat dina Azure-distributioner med hjälp av PowerShell- eller Azure CLI-skript.

Dessa funktioner migreras ofta sedan till verktyg som [Puppet](https://puppetlabs.com/) och [Chef](https://www.chef.io/) i syfte att automatisera skapandet av och konfigurationen för virtuella datorer i större skala. (Här följer några länkar om [att använda de här verktygen med Azure](#tools-for-working-with-containers).)

### <a name="azure-resource-group-templates"></a>Azure-resursgruppsmallar
Nyligen publicerade Azure [Azure Resource Management](../articles/resource-manager-deployment-model.md) REST API samt uppdaterade PowerShell- och Azure CLI-verktyg så att den går lätt att använda. Du kan distribuera, ändra eller distribuera om hela programtopologier med [Azure Resource Manager-mallar](../articles/resource-group-authoring-templates.md) med Azures resurshanterings-API med hjälp av:

* tipset [Azure Portal med hjälp av mallar](https://github.com/Azure/azure-quickstart-templates)&mdash;, använd knappen "DeployToAzure"
* [Azure CLI](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure PowerShell-modulerna](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>Distribution och hantering av hela grupper av virtuella datorer i Azure och behållare
Det finns flera populära system som kan distribuera hela grupper av virtuella datorer och installera Docker (eller andra Linux-behållarvärdsystem) på dem som en automatiserbar grupp. Direktlänkar finns i avsnittet [behållare och verktyg](#containers-and-vm-technologies) nedan. Det finns flera system som gör detta i större eller mindre omfattning, och den här listan är inte komplett. Beroende på dina kunskaper och scenarier kan de vara mer eller mindre användbara.

Docker har en egen uppsättning verktyg för att skapa virtuella datorer ([docker-machine](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) och ett belastningsutjämnande, docker-behållare-klusterhanteringsverktyg ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Dessutom levereras [Azure Docker VM-tillägget](https://github.com/Azure/azure-docker-extension/blob/master/README.md) med standardstöd för [`docker-compose`](https://docs.docker.com/compose/), vilket gör att det blir möjligt att distribuera konfigurerade programbehållare över flera behållare.

Dessutom kan du kan prova [Mesospheres DCOS (Data Center Operating System)](http://docs.mesosphere.com/install/azurecluster/). DCOS baseras på en distribuerad systemkernel, [mesos](http://mesos.apache.org/), med öppen källkod som gör att du kan hantera ditt datacenter som en adresserbar tjänst. DCOS har inbyggda paket för flera viktiga system som [Spark](http://spark.apache.org/) och [Kafka](http://kafka.apache.org/) (och andra) samt inbyggda tjänster, som [Marathon](https://mesosphere.github.io/marathon/) (ett system för behållarkontroll) och [Chronos](https://mesos.github.io/chronos/) (en distribuerad schemaläggare). Mesos har skapats utifrån erfarenheter från Twitter, AirBnb och andra företag med omfattande webbnärvaro. Du kan även använda **swarm** som dirigeringsmotor.

[kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) är ett system för hantering av virtuella datorer och behållargrupper med öppen källkod som bygger på erfarenheter på Google. Du kan även använda [kubernetes med väv för att tillhandahålla stöd för nätverk](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave).

[Deis](http://deis.io/overview/) är en öppen PaaS-källkod (Platform-as-a-Service) som gör det enkelt att distribuera och hantera program på egna servrar. Deis bygger på Docker och CoreOS och tillhandahåller en enkel PaaS med ett Heroku-inspirerat arbetsflöde. Du kan enkelt[skapa en virtuell Azure-datorgrupp med tre noder och installera Deis](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) på Azure och sedan [installera ett Hello World Go-program](../articles/virtual-machines/linux/deis-cluster.md#deploy-and-scale-a-hello-world-application).

[CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html), en Linux-distribution med ett optimerat utrymmeskrav, stöd för Docker och ett eget behållarsystem som heter [rkt](https://github.com/coreos/rkt), har också ett hanteringsverktyg för behållargrupper som kallas för [fleet](https://coreos.com/using-coreos/clustering/).

Ubuntu, en annan mycket populär Linux-distribution, ger bra stöd för Docker, men stöder dessutom [Linux-kluster (LXC-typ)](https://help.ubuntu.com/lts/serverguide/lxc.html).

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Verktyg för att arbeta med virtuella Azure-datorer och behållare
I arbetet med behållare och virtuella datorer på Azure används verktyg. Det här avsnittet innehåller en lista över bara några av de mest användbara och viktiga begrepp som används för behållare, grupper och större konfigurations- och dirigeringsverktyg som används tillsammans med dem.

> [!NOTE]
> Det här området förändras otroligt snabbt och trots att vi gör vårt bästa för att hålla det här avsnittet och dess länkar uppdaterade, är det inte säkert att det alltid är så. Se till att söka efter intressanta ämnen för att hålla dig uppdaterad!
>
>

### <a name="containers-and-vm-technologies"></a>Behållare och tekniker för virtuella datorer
Vissa Linux-behållartekniker:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS och rkt](https://github.com/coreos/rkt)
* [Öppna behållarprojekt](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Windows-behållarlänkar:

* [Windows-behållare](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Visual Studio Docker-länkar:

* [Visual Studio 2015 RC-verktyg för Docker – förhandsversion](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Docker-verktyg:

* [Daemon för Docker](https://docs.docker.com/installation/#installation)
* Docker-klienter
  * [Windows Docker-klienten på Chocolatey](https://chocolatey.org/packages/docker)
  * [Installationsinstruktioner för Docker](https://docs.docker.com/installation/#installation)

Docker på Microsoft Azure:

* [Docker VM-tillägg för Linux på Azure](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Användarhandbok för Azure Docker VM-tillägg](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [Använda tillägget för virtuella datorer med Docker genom kommandoradsgränssnittet för Azure (Azure CLI)](../articles/virtual-machines/linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Använda tillägget för virtuella datorer med Docker från Azure Portal](../articles/virtual-machines/linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Så använder du en Docker-dator med Azure](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Så här använder du Docker med Swarm på Azure](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Kom igång med Docker och Compose på Azure](../articles/virtual-machines/linux/docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Med hjälp av en mall för Azure-resursgrupp för att snabbt skapa en Docker-värd på Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [Inbyggt stöd för `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) för befintliga program
* [Implementera ett privat Docker-register på Azure](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Linux-distributioner och Azure-exempel:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

Konfiguration, klusterhantering och behållardirigering:

* [Flotta på CoreOS](https://coreos.com/using-coreos/clustering/)
* Deis

  * [Skapa en virtuell Azure-datorgrupp med tre noder, installera Deis och starta ett Hello World Go-program](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Kubernetes

  * [Fullständig handbok för automatisk Kubernetes-klusterdistribution med CoreOS och Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Mesospheres DCOS (Data Center Operating System)](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) och [Hudson](http://hudson-ci.org/)

  * [Blogg: Det sekundära plugin-programmet Jenkins för Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [GitHub-repo: Lagringsplugin-programmet Jenkins för Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [Tredje part: Det sekundära plugin-programmet Hudson för Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [Tredje part: Plugin-programmet Hudson för lagring på Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Azure Automation](https://azure.microsoft.com/services/automation/)

  * [Video: How to Use Azure Automation with Linux VMs (Så här använder du Azure Automation med virtuella Linux-datorer)](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* PowerShell DSC för Linux

  * [Blogg: How to do Powershell DSC for Linux (Så här använder du Powershell DSC för Linux)](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub: Docker-klient DSC](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>Nästa steg
Titta på [Docker](https://www.docker.com) och [Windows-behållare](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview).

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->
