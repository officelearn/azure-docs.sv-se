---
title: Azure ML-arbetsstationen viktig information för sprint den 1 November 2017
description: Det här dokumentet beskriver uppdateringar för sprint 1-versionen av Azure ML
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/06/2017
ms.openlocfilehash: a4945c77be5763ffeda328184149f712572937c0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2018
---
# <a name="sprint-1---november-2017"></a>Sprint 1 – november 2017 

**Versionsnummer: 0.1.1710.31013**

Välkommen till den andra uppdateringen Azure Machine Learning-arbetsstationen. Vi fortsätter att göra förbättringar för säkerhet, stabilitet och underhålla i appen arbetsstationen, CLI och backend-tjänster lagret. Tack för att mycket Skicka oss leenden och frowns. Många av de nedan uppdateringar görs direkt resultatet av din feedback. Skriv ned dem kommer!

## <a name="notable-new-features"></a>Viktiga nya funktioner
- Azure ML finns nu i två nya Azure-regioner: **Västeuropa** och **Sydostasien**. De ansluta till de föregående områdena av **östra USA 2**, **Väst centrala oss**, och **östra**, när det totala antalet distribueras regioner till fem.
- Vi har aktiverat Python-kod för syntaxmarkering i appen arbetsstationen för att göra det lättare att läsa och redigera Python källkod. 
- Nu kan du starta din favorit IDE direkt från en fil i stället för från hela projektet.  Öppna en fil i arbetsstationen och sedan klicka på ”Redigera” startar din IDE (för närvarande VS-kod och PyCharm stöds) till den aktuella filen och projektet.  Du kan också klicka på pilen bredvid knappen Redigera för att redigera filen i textredigeraren arbetsstationen.  Filer är skrivskyddade förrän du klickar på Redigera, att förhindra oavsiktliga ändringar.
- Populära ritområdet biblioteket `matplotlib` version 2.1.0 nu levereras med appen arbetsstationen.
- Vi uppgraderade .NET Core till 2.0 data prep-motorn. Detta bort behovet av brew installera openssl under installationen av appen på macOS. Den banar också vägen för mer spännande data prep funktioner som finns inom en snar framtid. 
- Vi har aktiverat ett versionsspecifikt app webbsida, så att du kan få mer relevanta viktig information och uppdatera frågor baserat på din nuvarande version av appen.
- Om din lokala användarnamnet har ett utrymme i den, kan programmet nu installeras. 

## <a name="detailed-updates"></a>Detaljerad uppdateringar
Nedan visas en lista över detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

### <a name="installer"></a>Installer
- Appinstallationsprogram rensar nu installationskatalogen som skapats av en äldre version av appen.
- Fast ett programfel som leder installer fastnar i macOS hög Sierra 100%.
- Det finns nu en direktlänk till katalogen installer för användaren att granska installer loggar om installationen misslyckas.
- Installera nu fungerar för användare som har utrymme i sitt användarnamn.

### <a name="workbench-authentication"></a>Autentisering av arbetsstation
- Stöd för autentisering i Proxy-hanteraren.
- Logga in nu lyckas om användare som finns bakom en brandvägg. 
- Om användaren har experiment konton i Azure-regioner, och om en region händer blir otillgänglig, avbryter appen inte längre.
- När autentisering inte har slutförts och dialogrutan autentisering visas fortfarande försöker appen inte längre läsa arbetsytan från lokala cacheminne.

### <a name="workbench-app"></a>Workbench App
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

### <a name="data-preparation"></a>Förberedelse av data 
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

### <a name="job-execution"></a>Jobbkörningen
- Argumentet ingår nu i körningshistorik post.
- Jobb som har inletts CLI nu visas i panelen för kör händelser jobbet automatiskt.
- Jobbet panelen visas nu jobben som skapats av gästanvändare som lagts till i AAD-klient.
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

### <a name="jupyter-notebook"></a>Jupyter Notebook
- Bärbar dator namnsökning fungerar nu korrekt i vyn bärbara datorer.
- Nu kan du ta bort en bärbar dator i vyn bärbara datorer.
- Nya magic `%upload_artifact` har lagts till för att överföra filer skapas i anteckningsboken körningsmiljön i körningshistorik datalagret.
- Kernel-fel är nu anslutna i anteckningsboken jobbstatus för enklare felsökning.
- Jupyter servern nu stängs korrekt av när användaren loggar ut från appen.

### <a name="azure-portal"></a>Azure Portal
- Experiment konto och hantering av modellen konto nu kan skapas i två nya Azure-regioner: västra Europa och Sydostasien.
- Modellen Management konto DevTest plan nu är endast tillgängligt när det är den första som ska skapas i prenumerationen. 
- Hjälplänken i Azure-portalen har uppdaterats för att peka på sidan rätt dokumentation.
- Beskrivningsfält tas bort från informationssidan för Docker-avbildningen eftersom den inte är tillämplig.
- Information, inklusive inställningar för AppInsights och Autoskala läggs till sidan information om tjänsten.
- Sidan för hantering av modellen återger nu även om cookies från tredje part är inaktiverade i webbläsaren. 

### <a name="operationalization"></a>Operationalization
- Webbtjänsten med ”resultat” i namnet inte längre.
- Användare kan nu skapa en distributionsmiljö med bara deltagare åtkomst till en Azure-resursgrupp eller prenumerationen. Ägaråtkomst till hela prenumerationen behövs inte längre.
- Operationalization CLI nu gillar fliken automatisk komplettering på Linux.
- Konstruktionen bildhanteringstjänst stöder nu skapa avbildningar för Azure IoT-tjänster/enheter.

### <a name="sample-projects"></a>Exempelprojekt
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
