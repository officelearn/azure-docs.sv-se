---
title: Vad är nytt i Azure Machine Learning
description: Det här dokumentet beskriver uppdateringar till Azure Machine Learning.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.service: machine-learning
ms.workload: data-services
ms.topic: reference
ms.date: 03/28/2018
ms.openlocfilehash: 9d16606665bf043e094bebdfbbce973910135f1a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="whats-new-in-azure-machine-learning"></a>Vad är nytt i Azure Machine Learning?

I den här artikeln lär dig mer om de nya versionerna för [Azure Machine Learning Services](overview-what-is-azure-ml.md). 

## <a name="2018-03-sprint-4"></a>2018-03 (sprint 4)
**Versionsnumret**: 0.1.1801.24353 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))


Välkommen till den femte uppdateringen Azure Machine Learning-arbetsstationen. Många av följande uppdateringar görs direkt resultatet av din feedback. Skriv ned dem kommer!

**Viktiga nya funktioner och ändringar**

- Stöd för att köra skript på fjärranslutna Ubuntu VMs internt på miljön utöver remote docker baserat körning.
- Den nya miljön upplevelsen i arbetsstationen App kan du skapa beräkning mål och kör konfigurationer förutom våra CLI-baserad upplevelse.
![Fliken miljöer](media/azure-machine-learning-release-notes/environment-page.png)
- Anpassningsbara Körningshistorik rapporterar ![bild av nya kör rapporter om Enhetshistorik](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Detaljerad uppdateringar**

Nedan följer en lista över detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

### <a name="workbench-ui"></a>Workbench UI
- Anpassningsbara Körningshistorik rapporter
  - Förbättrad diagram konfiguration för Körningshistorik rapporter
    - Använda entrypoints kan ändras
    - Översta filter kan läggas till och ändras ![Lägg till filter](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Diagram och statistik kan läggas till eller ändras (och dra och släpp ordnas om).
    ![Skapa nya diagram](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD för Körningshistorik rapporter
  - Flytta alla befintliga Kör listan historikvyn kalkylbladen för konfiguration på serversidan rapporter som fungerar som pipelines på körs från de valda startpunkterna.

- Fliken miljöer
  - Enkelt lägga till nya beräkning mål och kör konfigurationsfiler till projektet ![nytt Compute mål](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Hantera och uppdatera dina konfigurationsfiler med hjälp av en enkel, formulärbaserad UX
  - Ny knapp för att förbereda dina miljöer för körning

- Prestandaförbättringar i listan med filer i sidopanelen

### <a name="data-preparation"></a>Förberedelse av data 
- Azure Machine Learning arbetsstationen kan du nu för att kunna söka efter en kolumn med en känd kolumnnamnet.


### <a name="experimentation"></a>Experimentering
- Azure Machine Learning arbetsstationen stöder nu kör skripten internt på python eller pyspark miljön. För den här funktionen användaren skapar och hanterar sina egna miljö på fjärranslutna VM, och Använd Azure Machine Learning-arbetsstationen för att utföra sina skript på målet. Se [konfigurerar Azure Machine Learning experiment Service](experimentation-service-configuration.md) 

### <a name="model-management"></a>Modellhantering
- Stöd för att anpassa de behållare som distribuerats: gör det möjligt att anpassa avbildningen behållare genom att tillåta installation av externt bibliotek med lgh get etc. Det är inte längre begränsad till pip-installeras bibliotek. Finns det [dokumentationen](model-management-custom-container.md) för mer information.
  - Använd den `--docker-file myDockerStepsFilename` flaggan och filnamnet med manifest-, image- eller skapa kommandon.
  - Observera att basavbildningen är Ubuntu och kan inte ändras.
  - Exempel-kommando: 
  
      ```shell
      $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
      ```



## <a name="2018-01-sprint-3"></a>2018-01 (sprint 3) 
**Versionsnumret**: 0.1.1712.18263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Följande är uppdateringar och förbättringar i den här sprint. Många av dessa uppdateringar görs direkt följd av användarfeedback. 


Nedan följer en lista över detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

- Uppdateringar till stacken autentisering tvingar val för inloggning och kontot vid start

### <a name="workbench"></a>Workbench
- Möjlighet att installera eller avinstallera appen från Lägg till/ta bort program
- Uppdateringar till stacken autentisering tvingar val för inloggning och kontot vid start
- Förbättrad upplevelse för enkel inloggning (SSO) i Windows
- Användare som hör till flera klienter med andra autentiseringsuppgifter ska nu kunna logga in på arbetsstationen

### <a name="ui"></a>UI
- Allmänna förbättringar och korrigeringarna

### <a name="notebooks"></a>Bärbara datorer
- Allmänna förbättringar och korrigeringarna

### <a name="data-preparation"></a>Förberedelse av data 
- Förbättrad automatisk-förslag vid utförandet av exempel omvandlingar
- Förbättrad algoritm för mönster frekvens inspector
- Möjlighet att skicka exempeldata och feedback när du utför med hjälp av exempel transformationer ![bild av skicka feedbacklänk på härledd kolumn transformeringen](media/azure-machine-learning-release-notes/SendFeedbackFromDeriveColumn.png)
- Spark Runtime-förbättringar
- Scala har ersatt Pyspark
- Fast oförmåga att stänga Data är inte tillämpligt för tid serien Inspector 
- Fast låser sig tiden för körningen Data Prep för HDI

### <a name="model-management-cli-updates"></a>Uppdaterar modellen Management CLI 
  - Ägarskapet för prenumerationen är inte längre krävs för att etablera resurser. Deltagare åtkomst till resursgruppen räcker att ställa in distributionsmiljön.
  - Aktiverade lokala miljön kostnadsfritt konfigurera prenumerationer 

## <a name="2017-12-sprint-2-qfe"></a>2017 12 (Skriv 2 QFE) 
**Versionsnumret**: 0.1.1711.15323 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Det här är version QFE (Quick Fix Engineering), en lägre version. Det tar flera telemetri frågor och hjälper Produktteamet för att bättre förstå hur produkten används. Informationen kan försättas i framtida ansträngningar för att förbättra upplevelsen för produkten. 

Dessutom finns två viktiga uppdateringar:

- Fast ett programfel i data prep som förhindrade tid serien inspector visas i paket för förberedelse av data.
- I kommandoradsverktyget behöver du inte längre vara en Azure-prenumeration ägare att etablera Machine Learning Compute ACS-kluster. 

## <a name="2017-12-sprint-2"></a>2017 – 12 (sprint 2)
**Versionsnumret**: 0.1.1711.15263 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Välkommen till den tredje Azure Machine Learning-uppdateringen. Den här uppdateringen innehåller förbättringar i appen arbetsstationen, kommandoradsgränssnittet (CLI) och backend-tjänster. Tack för att skicka leenden och frowns. Många av följande uppdateringar görs direkt resultatet av din feedback. 

**Viktiga nya funktioner**
- [Stöd för SQL Server och SQL Azure DB som en datakälla](data-prep-appendix2-supported-data-sources.md#types) 
- [Djupgående inlärning på Spark med GPU stöd med MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Alla AML behållare är kompatibla med Azure IoT-gränsenheterna när distribuerade (inga ytterligare åtgärder krävs)](http://aka.ms/aml-iot-edge-blog)
- Registrerad modell lista och detaljer vyer tillgängliga Azure-portalen
- Åtkomst till beräkning mål med hjälp av SSH-nyckel för autentisering förutom användarnamn/lösenord-baserad åtkomst. 
- Nya mönstret frekvens Inspector i data Förbered dig upplevelse. 

**Detaljerad uppdateringar** följer en lista över detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

### <a name="installer"></a>Installer
- Installationsprogrammet kan self uppdatering så som buggar korrigeringar och nya funktioner som kan användas utan att användaren behöver installera om det.

### <a name="workbench-authentication"></a>Autentisering av arbetsstation
- Flera korrigeringar av system för autentisering. Berätta om du fortfarande har problem med inloggningen.
- UI-ändringar som gör det lättare att hitta inställningarna för proxyservern Manager.

### <a name="workbench"></a>Workbench
- Skrivskyddad vy har nu lätta blå bakgrund
- Flyttade redigera-knappen till höger för att göra det lättare att hitta.
- ”dsource”, ”dprep” och ”ipynb” filformat kan nu återges i raw-format
- Arbetsstationen har nu en ny redigera miljö som hjälper användaren mot med externa IDEs Redigera skript och använda arbetsstationen endast för att redigera filtyper som har en innehållsrik redigering (till exempel bärbara datorer, datakällor, paket för förberedelse av Data)
- Inläsning av listan med arbetsytorna och projekt som användaren har åtkomst till är betydligt snabbare nu

### <a name="data-preparation"></a>Förberedelse av data 
- Ett mönster frekvens Inspector att visa mönster i en kolumn. Du kan också filtrera dina data med hjälp av dessa mönster. Detta visar vyn liknar värdet räknar-inspector. Skillnaden är att mönster frekvens visar antalet unika mönster av data i stället för antalet unika data. Du kan också filtrera in eller ut alla rader som uppfyller ett visst mönster.

![Bild av mönstret frekvens inspector på produkt-nummer](media/azure-machine-learning-release-notes/pattern-inspector-product-number.png)

- Prestandaförbättringar vid rekommenderar edge fall att granska i omvandling-härledd kolumn efter exempel'

- [Stöd för SQL Server och SQL Azure DB som en datakälla](data-prep-appendix2-supported-data-sources.md#types) 

![Bild av hur du skapar en ny SQL server-datakälla](media/azure-machine-learning-release-notes/sql-server-data-source.png)

- Aktiverad ”i korthet” vy över rad- och antal

![Bild av radantal för kolumnen vid en grace](media/azure-machine-learning-release-notes/row-col-count.png)

- Data prep är aktiverat i alla beräkning sammanhang
- Datakällor som använder en SQL Server-databas är aktiverade i alla beräkning sammanhang
- Data Förbered dig rutnätet kolumner kan filtreras efter datatyp
- Fast problemet med att konvertera flera kolumner till dags datum
- Problem har åtgärdats kan användaren välja utdatakolumnen som källa i härledd kolumn av exemplet om användaren har ändrat utdata kolumnnamn i Avancerat läge.

### <a name="job-execution"></a>Jobbkörningen
Du kan nu skapa och få åtkomst till remotedocker eller kluster typen Beräkning mål med SSH-nyckel för autentisering följa de här stegen:
- Koppla beräkning mål med följande kommando i CLI

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
>[!NOTE]
>-k (eller--Använd azureml-ssh-nyckel) anger i kommandot för att generera och använder SSH-nyckel.

- Azure ML-arbetsstationen ska generera en offentlig nyckel och utdata som i konsolen. Logga in på målet beräkning med samma användarnamn och Lägg till ~/.ssh/authorized_keys filen med den här offentliga nyckeln.

- Du kan förbereda det här målet för beräkning och använda den för körning och Azure ML-arbetsstationen använder denna nyckel för autentisering.  

Mer information om hur du skapar beräkning mål finns [konfigurerar Azure Machine Learning experiment Service](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools för AI
- Tillagt stöd för [Visual Studio Tools för AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Kommandoradsgränssnitt (CLI)
- Lägga till `az ml datasource create` -kommandot tillåter att skapa en datakällfil från kommandoraden

### <a name="model-management-and-operationalization"></a>Modellen hanterings- och Operationalization
- [Alla AML behållare är kompatibla med Azure IoT-gränsenheterna när operationalized (inga ytterligare åtgärder krävs)](http://aka.ms/aml-iot-edge-blog) 
- Förbättringar av felmeddelanden i o16n CLI
- Felkorrigeringar i hanteringsportalen för modellen UX  
- Konsekvent bokstav skiftläge för modellen management attribut i den här sidan
- Realtid bedömningen anrop timeout inställt på 60 sekunder
- Registrerad modell lista och information om vyer som är tillgängliga i Azure-portalen

![modellinformation i portalen](media/azure-machine-learning-release-notes/model-list.jpg)

![Översikt över säkerhetsmodell i portalen](media/azure-machine-learning-release-notes/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Djup Learning på Spark med [GPU-support](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Stöd för Resource Manager-mallar för distribution av enkelt resurs
- Stöd för SparklyR ekosystem
- [AZTK integrering](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Exempelprojekt
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) och [MMLSpark](https://github.com/Azure/mmlspark) exempel uppdateras med den nya versionen av Azure ML-SDK

### <a name="breaking-changes"></a>Gör ändringar
- Upphöja den `--type` växla i `az ml computetarget attach` till en underkommandot. 

    - `az ml computetarget attach --type remotedocker` är nu `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` är nu `az ml computetarget attach cluster`

## <a name="2017-11-sprint-1"></a>2017-11 (sprint 1) 
**Versionsnumret**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Vi har gjort förbättringar av säkerhet, stabilitet och underhålla i appen arbetsstationen, CLI och backend-tjänster lagret i den här versionen. Tack för att mycket Skicka oss leenden och frowns. Många av de nedan uppdateringar görs direkt resultatet av din feedback. Låt dem kommer!

### <a name="notable-new-features"></a>Viktiga nya funktioner
- Azure ML finns nu i två nya Azure-regioner: **Västeuropa** och **Sydostasien**. De ansluta till de föregående områdena av **östra USA 2**, **Väst centrala oss**, och **östra**, när det totala antalet distribueras regioner till fem.
- Vi har aktiverat Python-kod för syntaxmarkering i appen arbetsstationen för att göra det lättare att läsa och redigera Python källkod. 
- Nu kan du starta din favorit IDE direkt från en fil i stället för från hela projektet.  Öppna en fil i arbetsstationen och sedan klicka på ”Redigera” startar din IDE (för närvarande VS-kod och PyCharm stöds) till den aktuella filen och projektet.  Du kan också klicka på pilen bredvid knappen Redigera för att redigera filen i textredigeraren arbetsstationen.  Filer är skrivskyddade förrän du klickar på Redigera, att förhindra oavsiktliga ändringar.
- Populära ritområdet biblioteket `matplotlib` version 2.1.0 nu levereras med appen arbetsstationen.
- Vi uppgraderade .NET Core till 2.0 data prep-motorn. Detta bort behovet av brew installera openssl under installationen av appen på macOS. Den banar också vägen för mer spännande data prep funktioner som finns inom en snar framtid. 
- Vi har aktiverat ett versionsspecifikt app webbsida, så att du kan få mer relevanta viktig information och uppdatera frågor baserat på din nuvarande version av appen.
- Om din lokala användarnamnet har ett utrymme i den, kan programmet nu installeras. 

### <a name="detailed-updates"></a>Detaljerad uppdateringar
Nedan visas en lista över detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

#### <a name="installer"></a>Installer
- Appinstallationsprogram rensar nu installationskatalogen som skapats av en äldre version av appen.
- Fast ett programfel som leder installer fastnar i macOS hög Sierra 100%.
- Det finns nu en direktlänk till katalogen installer för användaren att granska installer loggar om installationen misslyckas.
- Installera nu fungerar för användare som har utrymme i sitt användarnamn.

#### <a name="workbench-authentication"></a>Autentisering av arbetsstation
- Stöd för autentisering i Proxy-hanteraren.
- Logga in nu lyckas om användare som finns bakom en brandvägg. 
- Om användaren har experiment konton i Azure-regioner, och om en region händer blir otillgänglig, avbryter appen inte längre.
- När autentisering inte har slutförts och dialogrutan autentisering visas fortfarande försöker appen inte längre läsa arbetsytan från lokala cacheminne.

#### <a name="workbench-app"></a>Workbench App
- Syntaxmarkering för Python-kod är aktiverat i textredigeraren.
- Knappen Redigera i textredigeraren kan du redigera filen antingen i en IDE (VS-kod och PyCharm stöds) eller i den inbyggda textredigeraren.
- Textredigerare är i skrivskyddat läge som standard. 
- Spara knappen visual nu tillståndsändringar inaktiverad när den aktuella filen är sparad och därför inte längre är felaktig.
- Arbetsstationen sparar _alla_ osparade filer när du initierar en körning.
- Arbetsstationen kommer ihåg senast använda arbetsytan på den lokala datorn så att den öppnas automatiskt.
- En instans av Workbench tillåts nu köra. Tidigare kunde flera instanser startas som orsakas av problem när du arbetar i samma projekt.
- Har bytt namn till Arkiv-menyn ”Öppna projekt...” till ”Lägg till befintliga mappen som projekt...” 
- Fliken växling är mycket snabbare.
- Hjälplänkar läggs till i dialogrutan Konfigurera IDE.
- Feedback-formuläret nu ihåg e-postadressen du angav senast.
- Leenden och frowns formuläret textområde är nu större, så att du kan skicka oss mer feedback! 
- Den `--owner` växla hjälptexten i `az ml workspace create` är åtgärdat.
- Vi har lagt till en ”CA” dialogruta för att hjälpa användare enkelt kan visa och kopiera versionsnumret för appen.
- Menyalternativets ”föreslå” läggs till Hjälp-menyn.
- Experiment kontonamnet visas nu i appen namnlisten och föregående appnamnet ”Azure Machine Learning arbetsstationen”.
- En version-specifik app webbsida visas nu baserat på version av appen upptäcktes.

#### <a name="data-preparation"></a>Förberedelse av data 
- Extern webbplats kan inte läsas in från kartan Inspector för att förhindra problem med säkerheten.
- Histogram och antal kontrollanter har nu möjlighet att visa diagram i logaritmisk skala.
- När en beräkning är pågående, kvaliteten datastapel visas nu en annan färg att signalera ”beräkning”-tillstånd.
- Kolumnen mått kan du nu visa statistik för kategoriskt värde kolumner.
- Det sista tecknet i namnet på datakällan trunkeras inte längre.
- Förberedelseverktyg datapaketet är nu öppen när du växlar flikar, som följer märkbar prestandavinster.
- I datakällan, när du växlar mellan data och mått visning ändras ordningen på kolumnerna nu längre.
- Öppna en ogiltig `.dprep` eller `.dsource` gör filen inte längre arbetsstationen kraschar.
- Förberedelseverktyg datapaketet kan nu använda relativ sökväg för utdata i _skriva till CSV_ transformeringen.
- _Behåll kolumnen_ transformering nu kan användaren att lägga till ytterligare kolumner när redigeras.
- _Ersätt det_ menyn nu faktiskt startar _ersätta värdet_ dialogrutan.
- _Ersätt värdet_ transformera nu fungerar som förväntat i stället för att fel.
- Förberedelseverktyg datapaketet använder nu absolut sökväg när du refererar till filer utanför projektmappen, gör det möjligt att köra paketet i lokala sammanhang med den absoluta sökvägen till filen.
- _Fullständig_ som en provtagning stöds nu när du använder Azure blob som datakälla.
- Genererade Python-kod (från prep datapaketet) har nu både CR och LF, vilket gör det egna i Windows.
- _Välj mått_ listrutan nu döljer egenskapen när du växlar till datavyn.
- Arbetsstationen kan nu processen parkettgolv filer även om det använder Python-körning. Tidigare kan endast Spark användas vid bearbetning av parkettgolv filer. 
- Filtrera värden i en kolumn med _datum_ datatyp medför inte längre data prep motorn kraschar.
- Måttvy respekterar nu provtagning strategi för uppdateringar.
- Remote provtagning jobb nu fungerar korrekt.

#### <a name="job-execution"></a>Jobbkörningen
- Argumentet ingår nu i körningshistorik post.
- Jobb som har inletts CLI nu visas i panelen för kör händelser jobbet automatiskt.
- Jobbet panelen visas nu jobben som skapats av gästanvändare som lagts till i Azure AD-klient.
- Avbryta jobb panelen och borttagna åtgärder är stabilare.
- När du klickar på Kör utlöses felmeddelande nu om konfigurationsfilerna är i felaktigt format.
- Avslutande appen stör inte längre jobb som har inletts i CLI.
- Jobb som har inletts CLI nu fortsätter att dela ut standard ut även efter körning av en timme.
- Bättre felmeddelanden visas när prep datapaketet körs inte i Python/PySpark.
- `az ml experiment clean` Rensar nu Docker-avbildningar i samt fjärranslutna VM.
- `az ml experiment clean` nu fungerar korrekt för lokala mål i macOS.
- Felmeddelanden när målobjekt för lokal eller fjärransluten Docker körs rensas upp och lättare att läsa.
- Bättre felmeddelande visas när HDInsight-klustrets huvudnod namn inte har utformats korrekt när anslutna som ett mål för körning.
- Bättre felmeddelande visas när hemlighet inte hittades i tjänsten autentiseringsuppgifter. 
- MMLSpark biblioteket har uppgraderats till stöd för Apache Spark 2.2.
- MMLSpark innehåller nu ämne kodning transformeringen (nät kodning) för medicinska dokument.
- `matplotlib` version 2.1.0 är nu levereras out av den ruta arbetsstationen.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- Bärbar dator namnsökning fungerar nu korrekt i vyn bärbara datorer.
- Nu kan du ta bort en bärbar dator i vyn bärbara datorer.
- Nya magic `%upload_artifact` har lagts till för att överföra filer skapas i anteckningsboken körningsmiljön i körningshistorik datalagret.
- Kernel-fel är nu anslutna i anteckningsboken jobbstatus för enklare felsökning.
- Jupyter servern nu stängs korrekt av när användaren loggar ut från appen.

#### <a name="azure-portal"></a>Azure Portal
- Experiment konto och hantering av modellen konto nu kan skapas i två nya Azure-regioner: västra Europa och Sydostasien.
- Modellen Management konto DevTest plan nu är endast tillgängligt när det är den första som ska skapas i prenumerationen. 
- Hjälplänken i Azure-portalen har uppdaterats för att peka på sidan rätt dokumentation.
- Beskrivningsfält tas bort från informationssidan för Docker-avbildningen eftersom den inte är tillämplig.
- Information, inklusive inställningar för AppInsights och Autoskala läggs till sidan information om tjänsten.
- Sidan för hantering av modellen återger nu även om cookies från tredje part är inaktiverade i webbläsaren. 

#### <a name="operationalization"></a>Operationalization
- Webbtjänsten med ”resultat” i namnet inte längre.
- Användare kan nu skapa en distributionsmiljö med bara deltagare åtkomst till en Azure-resursgrupp eller prenumerationen. Ägaråtkomst till hela prenumerationen behövs inte längre.
- Operationalization CLI nu gillar fliken automatisk komplettering på Linux.
- Konstruktionen bildhanteringstjänst stöder nu skapa avbildningar för Azure IoT-tjänster/enheter.

#### <a name="sample-projects"></a>Exempelprojekt
- [_Klassificera Iris_ ](./tutorial-classifying-iris-part-1.md) exempelprojektet:
    - `iris_pyspark.py` har bytt namn till `iris_spark.py`.
    - `iris_score.py` har bytt namn till `score_iris.py`.
    - `iris.dprep` och `iris.dsource` uppdateras för att återspegla de senaste uppdateringarna för data prep-motorn.
    - `iris.ipynb` Bärbar dator ändras för att fungera i HDInsight-kluster.
    - Kör historik är aktiverat i `iris.ipynb` anteckningsboken cell.
- [_Avancerade Data Prep med cykeln filresurs-Data_ ](./tutorial-bikeshare-dataprep.md) exempelprojektet ”hantera felvärde” steg som har åtgärdats.
- [_MMLSpark på vuxna inventering Data_ ](https://github.com/Azure/MachineLearningSamples-mmlspark) exempelprojektet `docker.runconfig` format uppdateras från JSON till YAML.
- [_Distribuerad Hyperparameter justera_ ](./scenario-distributed-tuning-of-hyperparameters.md) exempelprojektet`docker.runconfig` format uppdateras från JSON till YAML.
- Nya exempelprojektet [ _avbildningen klassificering med CNTK_](./scenario-image-classification-using-cntk.md).


## <a name="2017-10-sprint-0"></a>2017 10 (sprint 0) 
**Versionsnumret**: 0.1.1710.31013 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;([hitta din version](known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Välkommen till den första uppdateringen av Azure Machine Learning arbetsstationen efter vårt första förhandsversion på Microsoft Ignite 2017 konferens. De huvudsakliga uppdateringarna i den här versionen är tillförlitlighet och stabilisering åtgärdas.  Några av de viktiga problem vi är:

### <a name="new-features"></a>Nya funktioner
- macOS hög Sierra stöds nu

### <a name="bug-fixes"></a>Felkorrigeringar
#### <a name="workbench-experience"></a>Arbetsstationen upplevelse
- Dra och släppa en fil till arbetsstationen orsaker arbetsstationen kraschar.
- Terminalfönster i VS kod som konfigurerats som en IDE för arbetsstationen inte känner igen _az ml_ kommandon.

#### <a name="workbench-authentication"></a>Autentisering av arbetsstation
Vi har gjort ett antal uppdateringar för att förbättra olika inloggning och autentisering problem som har rapporterats.
- Autentisering fönstret behåller poppar upp, särskilt när Internet-anslutning inte är stabil.
- Förbättrad tillförlitlighetsproblem runt autentiseringstokens.
- I vissa fall visas autentisering två gånger.
- Arbetsstationen huvudfönstret visar fortfarande ”autentisering” visas när autentiseringen har slutförts och popup-dialogrutan redan stängts.
- Om det finns ingen Internet-anslutning, visas autentiseringsdialogen med ett tomt fönster.

#### <a name="data-preparation"></a>Förberedelse av data 
- När ett specifikt värde filtreras fel och värden som saknas också filtreras bort.
- Ändra en provtagning tar bort efterföljande befintliga join-operationer.
- Ersätta saknas värdet tar transformeringen inte NaN tas med i beräkningen.
- Datum typhärledning utlöser undantag när null-värde påträffades.

#### <a name="job-execution"></a>Jobbkörningen
- Det finns inget Rensa felmeddelande när jobbkörningen inte kan överföra projektmappen eftersom den överskred storleksgränsen.
- Om användarens Python-skriptet ändras arbetskatalogen spåras inte filer som skrivits till utdata mappar. 
- Om aktiva Azure-prenumerationen är annat än det aktuella projektet tillhör resulterar jobbet 403-fel.
- Om Docker inte finns returneras inget Rensa felmeddelande om användaren försöker använda Docker som ett mål för körning.
- .runconfig filen sparas inte automatiskt när användaren klickar på _kör_ knappen.

#### <a name="jupyter-notebook"></a>Jupyter Notebook
- Bärbar dator server kan inte starta om användare använder med vissa typer av inloggningen.
- Felmeddelanden för anteckningsboken server inte identifierades i loggar som är synliga för användare.

#### <a name="azure-portal"></a>Azure Portal
- Att välja det mörka temat på Azure-portalen gör modellen hanteringsblad ska visas som en svart ruta.

#### <a name="operationalization"></a>Operationalization
- Återanvända ett manifest för att uppdatera en webbtjänst gör en ny Docker-avbildning som skapats med ett slumpmässigt namn.
- Tjänsten webbloggar kan inte hämtas från Kubernetes kluster.
- Missvisande felmeddelandet skrivs ut när användare försöker skapa en modell Management-konto eller ett ML beräkning och påträffar behörighetsproblem.

## <a name="next-steps"></a>Nästa steg

Läs översikt för [Azure Machine Learning](overview-what-is-azure-ml.md).