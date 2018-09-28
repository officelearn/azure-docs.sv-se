---
title: Skapa en edge-machine learning-lösning med Azure och Azure Stack | Microsoft Docs
description: Lär dig hur du skapar en edge machine learning-lösning med hjälp av Python med Azure och Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: c195f2ee24b61a57c098d5214a37f65e80845074
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410212"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>Självstudier: Skapa en edge-machine learning-lösning med Azure och Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Lär dig hur du skapar en edge-machine learning-lösning med Azure och Azure Stack.

I den här självstudien skapar du en exempel-miljö för att:

> [!div class="checklist"]
> - Skapa ett lagringskonto och en behållare för rensa data som ska finnas.
> - Skapa en Ubuntu Data Science Virtual Machine (DSVM) i Azure-portalen.
> - Distribuera Azure Machine Learning Services i Azure för att skapa och träna modeller.
> - Skapa Azure Machine Learning services-konton.
> - Distribuera och använda Azure Container Registry.
> - Distribuera ett Kubernetes-kluster till Azure Stack.
> - Skapa ett Azure Stack-lagringskonto och Storage-kö för data.
> - Skapa en ny Azure Stack-funktion för att flytta Rensa Data från Azure Stack till Azure.

**När du ska använda den här lösningen**

 -  Din organisation använder en DevOps-metod eller har en planerad för en nära framtid.
 -  Du vill implementera CI/CD-metoderna i din Azure Stack-implementering och det offentliga molnet.
 -  Vill du konsolidera CI/CD-pipeline i molnet och lokala miljöer.
 -  Du kan utveckla program med hjälp av molntjänster eller lokala tjänster.
 -  Du använder konsekvent utvecklarnas färdigheter mellan molnet och lokala program.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack är en utökning av Azure. Azure Stack får du flexibilitet och utvecklingsmöjligheterna med molnberäkning i din lokala miljö och aktivera det enda hybridmolnet som hjälper dig att skapa och distribuera hybridappar var som helst.  
> 
> Faktabladet [designöverväganden för Hybridtillämpningar](https://aka.ms/hybrid-cloud-applications-pillars) granskar grundpelare för programkvalitet (placering, skalbarhet, tillgänglighet, återhämtning, hantering och säkerhet) för att utforma, distribuera och driva hybridprogram. Designöverväganden kan optimera hybrid programdesign, minimerar utmaningar i produktionsmiljöer.

## <a name="prerequisites"></a>Förutsättningar

Några komponenter krävs för att skapa det här användningsfallet och kan ta lite tid att förbereda:

 -  En Azure-Partner för OEM/maskinvara kan distribuera ett Azure Stack för produktion och alla användare kan distribuera en ASDK

 -  En Azure Stack-Operator måste också distribuera App Service, skapa planer och erbjudanden, skapa en klientprenumeration och lägga till Windows Server 2016-bild

 -  Ett hybridnätverk och App Service som behöver ställas (Läs mer om [appintegrering med virtuella nätverk.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  Privat [Build and Release agenten](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) för VSTS-integrering måste ställas in före distributionen (se till att alla befintliga komponenter används uppfylla krav innan du börjar.)

Tidigare erfarenhet av Azure och Azure Stack är obligatoriskt. Mer information innan du fortsätter, börjar du med följande avsnitt:

 -  [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Viktiga begrepp för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Azure Stack Hybrid CI/CD-lösningsguide](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  En Azure-prenumeration (skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  En ny Web App-URL som skapats av [Webbapp](https://docs.microsoft.com/azure/app-service/app-service-web-overview) i Azure

 -  Distribution av [Azure Container Services (ACS) Kubernetes på Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Distribution av Azure Machine Learning-tjänsten (förhandsversion) [4 delar självstudien](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)

 -  Azure Machine Learning-experimentering [konto](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template)

**Azure Stack**

 -  Ett integrerat Azure Stack-System eller distribution av Azure Stack Development Kit.

    - Du hittar anvisningar för att installera Azure Stack på [installera Azure Stack Development Kit](/articles/azure-stack/asdk/asdk-install).
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Den här installationen kan kräva ett par timmar att slutföra.

 -  Distribution av [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure Stack

 -  En [Plan/erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure Stack-miljön

 -  En [klient prenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure Stack-miljön

 -  En Ubuntu Server VM-avbildning (tillgänglig i [Azure Stack Marketplace](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)) den virtuella datorn skapas i klientprenumeration på Azure Stack som privat skapandeagent samt Kubernetes-datorer. Om den här bilden inte är tillgänglig, kan det hjälpa Azure Stack-operatör, så det här läggs till i miljön. (Använd inte den 18.04 versionen av ubuntu, eftersom det för närvarande inte stöds.)

 -  En Webbapp i klientprenumeration (Observera att den nya Web App-URL för senare användning.)

 -  Distribution av VSTS Linux-baserade privata skapa agenten Virtual Machine, inom samma klientprenumeration

 -  Distribution av en [Azure Container Services (ACS) Kubernetes på Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**Utvecklarverktyg**

 -  [VSTS-arbetsytan](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (registreringen skapas ett projekt med namnet ”MyFirstProject”)

 -  [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) installation och [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) inloggning

 -  [Lokal klon](https://www.visualstudio.com/docs/git/gitquickstart) för projekt

 -  [Linux-undersystem för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) installation (för BASH och SSH)

 -  [Docker för Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) installation

 -  [Azure Machine Learning Workbench (förhandsgranskning)](https://aka.ms/azureml-wb-msi) installation

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) miljö installation

**VSTS**

 -  **VSTS-konto.** Konfigurera snabbt kontinuerlig integrering för att skapa, testa och distribution. Mer information om VSTS-konton finns i [skapa VSTS-kontot](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts).

 -  **Kodcentrallager.** Med hjälp av befintliga lagringsplatser i GitHub, BitBucket, DropBox, Onedrive och TFS, kan VSTS-plattformen utnyttja flera lagringsplatser för effektiv utveckling av pipelinen. Mer information om koden databaser finns i [Kom igång med Git och VSTS](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) självstudien.

 -  **Tjänstanslutning.** Ansluta till externa och fjärranslutna tjänsterna för att köra aktiviteter för testning, version eller distribution. Läs mer om VSTS Tjänstanslutningar i [Tjänsteslutpunkter för att skapa och släpp](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts).

 -  **Skapa definitioner.** Definiera automatisk build-processer och sätt ihop en uppsättning aktiviteter med hjälp av uppgiften katalogen. Läs mer om build definitioner i [skapa en Build-Definition](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentation.

 -  **Versionsdefinitioner.** Definiera distributionsprocessen för en samling av miljöer där programartefakter har distribuerats. Mer information om versionen definitioner finns [skapa en Definition för versionen](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentation.

 -  **Värdbaserade VSTS Linux skapa Agentpoolen.** Snabbt bygga, testa och distribuera program med hjälp av en Microsoft hanteras och underhållas värd för agenten. Mer information om värdbaserade VSTS build agenter finns i [värdbaserade agenter](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts) dokumentation.

## <a name="step-1-create-a-storage-account"></a>Steg 1: Skapa ett lagringskonto

Skapa ett lagringskonto och en behållare för rensa data som ska finnas.

1.  Logga in på den [ *Azure-portalen*](https://portal.azure.com/).

2.  I Azure-portalen, expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **alla tjänster**. Rulla ned till **Storage** och välj **lagringskonton**. I den ** Lagringskonton ** fönstret Välj **Lägg till**.

3.  Ange ett namn för lagringskontot.

    > [!Note]  
    > Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Lagringskontonamnet måste vara unikt i Azure. Azure portal anger om namnet på lagringskontot valt används redan.

4.  Ange distributionsmodellen som ska användas: **Resource Manager**.

5.  Välj typ av lagringskonto: **generell användning V1**, anger du prestandanivån: **Standard**.

6.  Välj replikeringsalternativ för lagringskontot: **GRS**.

7.  Välj prenumeration för storage-konto.

8.  Ange en ny resursgrupp eller välj en befintlig resursgrupp.

9.  Välj den geografiska platsen för lagringskontot.

10. Skapa lagringskontot genom att välja **Skapa**.

    ![Alternativ text](\media\azure-stack-solution-machine-learning\image1.png)

11.  Välj nyligen skapade lagringskontot.

12.  Välj på **Blobar**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image2.png)

13.  Välj på **+ behållare** och välj på **behållare**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image3.png)

14.  Ge behållaren namnet **uploadeddata** och välj åtkomsttyp av **behållare**.

15.  Välj på **skapa**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>Steg 2: Skapa en virtuell dator för datavetenskap

Skapa en Ubuntu Data Science Virtual Machine (DSVM) i Azure-portalen.

1.  Logga in på Azure portal från [https://portal.azure.com](https://portal.azure.com/)

2.  Välj på den **+ ny** länk och Sök efter ”Data Science Virtual Machine för Linux Ubuntu CSP

    ![Alternativ text](media\azure-stack-solution-machine-learning\image5.png)

1.  Välj **Data Science Virtual Machine för Linux (Ubuntu)** i listan och följ de anvisningarna på skärmen instruktioner för att skapa DSVM.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image6.png)

> ! [Viktigt]  
> **Välj** lösenord ** som den*autentiseringstyp*.

Placera den nya DSVM i samma resursgrupp som det nyligen skapade lagringskontot. Alla objekt i Edge-ML distribueras i Azure i den här resursgruppen.

1.  I inställningar konfigurera valfria funktioner

    a.  Välj den **Lagringskonto** skapade tidigare.

    b.  Skapa en ny **virtuellt nätverk**, **undernät**, och **offentlig IP-adress** ska som standard skapas ett namn baserat på resursgruppens namn.

    c.  Skapa en ny **NSG** bör den automatiskt skapar detta med rätt regler som redan används.

    d.  För den **Diagnostiklagringskonto**, Välj det lagringskonto som skapades tidigare.

    e.  Obs: Med AAD aktiverats och konfigurerats för Azure-prenumeration, hanterad tjänstidentitet aktiveras också.

2.  Välj **OK**.

### <a name="connect-to-the-dsvm"></a>Ansluta till DSVM

När du har skapat DSVM ansluter du till den virtuella datorn från Windows-undersystem för Linux.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  Ange Ja när du uppmanas att bekräfta VM-anslutning.

2.  Ange lösenordet som skapats för DSVM.

### <a name="update-the-dsvm"></a>Uppdatera DSVM 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>Steg 3: Distribuera Azure Machine Learning-tjänster

Distribuera Azure Machine Learning-tjänster i Azure.

Azure Machine Learning-tjänsterna (förhandsversion) är en integrerad lösning från slutpunkt till slutpunkt för avancerade analyser inom dataforskning. Med den här lösningen kan professionella dataforskare få hjälp med att förbereda data, utveckla experiment och distribuera modeller i molnskala.

Det här avsnittet beskrivs:

> [!div class="checklist"]
> - Skapa tjänstkonton för Azure Machine Learning-tjänster
> - Installera och logga in på Azure Machine Learning Workbench.
> - Skapa ett projekt i Workbench
> - Köra ett skript i projektet
> - Få åtkomst till kommandoradsgränssnittet (CLI)

Som en del av Microsoft Azure-portföljen kräver Azure Machine Learning-tjänster en Azure-prenumeration. För att skaffa en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Behörighet krävs för att skapa tillgångar som resursgrupper, virtuella datorer och andra tillgångar.

Azure Machine Learning Workbench-programmet kan installeras på följande operativsystem:

 -  Windows 10 eller Windows Server 2016
 -  macOS Sierra eller High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>Steg 4: Skapa Azure Machine Learning-tjänster

Skapa Azure Machine Learning services-konton.

Använd Azure-portalen för att etablera Azure Machine Learning-konton:

1.  Logga in på den [Azure-portalen](https://portal.azure.com/) med autentiseringsuppgifter för Azure-prenumerationen som ska användas. För att skaffa en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    ![Alternativ text](media\azure-stack-solution-machine-learning\image7.png)

1.  Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

    ![Skapa en resurs i Azure-portalen](media\azure-stack-solution-machine-learning\image8.png)

1.  Skriv **Machine Learning** i sökfältet. Välj **Machine Learning Experimentation (preview)** i sökresultatet.

    ![Azure Machine Learning-sökning](media\azure-stack-solution-machine-learning\image9.png)

1.  I den **Machine Learning-experimentering** rutan, rulla ned till botten och välj **skapa** att börja definiera experimenteringskontot.

    ![Azure Machine Learning – Skapa ett experimenteringskonto](media\azure-stack-solution-machine-learning\image10.png)

1.  I den **ML-experimentering** fönstret Konfigurera Machine Learning-experimenteringskonto.

    | Inställning | Föreslaget värde för självstudien | Beskrivning |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Namn på experimenteringskonto | Unikt namn | Ange ett unikt namn som identifierar kontot. Använda en avdelning eller projekt som bäst identifierar experimentet. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). |
    | Prenumeration | Prenumerationen | Välj den Azure-prenumerationen ska användas för experimentet. Om det finns flera prenumerationer, väljer du lämplig prenumeration där resursen ska debiteras. |
    | Resursgrupp | Resursgruppen | Använd en befintlig resursgrupp i prenumerationen eller ange ett namn för att skapa en ny resursgrupp för det här experimenteringskontot. |
    | Plats | Regionen som är närmast användarna | Välj platsen som är närmast användarna och dataresurserna. |
    | Antal platser | 2 | Ange antalet platser. Lär dig hur [platserna påverkar prissättningen](https://azure.microsoft.com/pricing/details/machine-learning/).<br><br>Den här snabbstarten krävs bara två platser. Platser kan läggas till eller tas bort efter behov i Azure-portalen. |
    | Lagringskonto | Unikt namn | Välj **Skapa nytt** och ange ett namn för att skapa ett [Azure-lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal). Namnet ska innehålla 3–24 tecken och får endast innehålla alfanumeriska tecken. Du kan också välja **Använd befintlig** och välj det befintliga lagringskontot i listan. Lagringskontot är obligatoriskt. Det används för att lagra projektartefakter och köra historikdata. |
    | Arbetsyta för experimenteringskonto | IrisGarden<br>(namnet som används i självstudier) | Ange ett namn på en arbetsyta för det här kontot. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). Den här arbetsytan innehåller verktyg som behövs för att skapa, hantera och publicera experiment. |
    | Tilldela ägare för arbetsytan | Kontot | Välj det egna kontot som ägare till arbetsytan. |
    | Skapa modellhanteringskonto | **markering** | Skapa ett modellhanteringskonto. Detta används för att distribuera och hantera modeller som realtidswebbtjänster. <br><br>När det är valfritt, rekommenderas skapar modellhanteringskontot samtidigt som experimenteringskontot. |
    | Kontonamn | Unikt namn | Välj ett unikt namn som identifierar modellhanteringskontot. Använd de avdelningar eller projekt som bäst identifierar experimentet. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). |
    | Prisnivå för modellhantering | **DEVTEST** | Välj **ingen prisnivå har valts** att ange prisnivån för det nya modellhanteringskontot. Spara pengar genom att markera DEVTEST-prisnivå om det är tillgängligt för prenumeration (begränsad tillgänglighet). I annat fall väljer du prisnivån S1. Välj Välj för att spara valet prisnivån. |
    | Fäst vid instrumentpanelen | check | Välj den **fäst på instrumentpanelen** så att du enkelt kan spåra Machine Learning-experimenteringskontot på den främre instrumentpanelen på Azure-portalen. |

    ![Konfiguration av Machine Learning-experimenteringskonto](media\azure-stack-solution-machine-learning\image11.png)

1.  Välj **Skapa** för att starta processen att skapa ett experimenteringskonto tillsammans med modellhanteringskontot.

    ![Konfiguration av Machine Learning-experimenteringskonto](media\azure-stack-solution-machine-learning\image12.png)

    Det kan ta en stund att skapa ett konto. Kontrollera status för distributionsprocessen genom att välja meddelandeikonen (klockan) på verktygsfältet i Azure portal.

    ![Meddelanden i Azure Portal](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>Installera och logga in på workbench 

Azure Machine Learning Workbench är tillgänglig för Windows eller macOS. Visa en lista med [plattformar som stöds](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation).

**Varning:** installationen kan ta cirka en timme att slutföra.

1.  Ladda ned och starta det senaste installationsprogrammet för Workbench.

    > [!Important]  
    > Ladda ned installationsprogrammet till hårddisken och kör programmet därifrån. Kör inte det direkt från webbläsarens download widget.<br>**På Windows:<br>**  en. Ladda ned [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).<br>b. Dubbelklicka på det nedladdade installationsprogrammet i Utforskaren.

1.  Följ den på skärmen instruktioner i installationsprogrammet för att slutföras.

    ** Installationen kan ta upp till 30 minuter att slutföra. **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    Installationsprogrammet hämta och konfigurera alla nödvändiga beroenden som Python, Miniconda och andra relaterade bibliotek. Den här installationen innehåller också Azure kommandoradsverktyg för flera plattformar eller Azure CLI.

1.  Starta Workbench genom att välja knappen **Launch Workbench** (Starta Workbench) på den sista skärmen i installationsprogrammet.

    Om installationsprogrammet stängs måste starta med hjälp av den **Machine Learning Workbench** genvägen på skrivbordet.

1.  Välj **logga in med Microsoft** att autentisera med Azure Machine Learning Workbench. Använd samma autentiseringsuppgifter som används i Azure-portalen för att skapa konton för experimentering och modellhantering.

    När du loggat in använder Workbench det första experimenteringskontot det hittar i Azure-prenumerationer och visar alla arbetsytor och projekt som är kopplade till kontot.

    > [!Tip]  
    > Växla till ett annat experimenteringskonto med hjälp av ikonen i det nedre vänstra hörnet av Workbench-fönstret.

### <a name="create-a-new-project-in-workbench"></a>Skapa ett nytt projekt i workbench

1.  Öppna appen Azure Machine Learning Workbench och logga in om det behövs.

    - Starta Windows, med hjälp av den **Machine Learning Workbench** genvägen på skrivbordet.

    - I macOS väljer du **Azure ML Workbench** i startfönstret.

1.  Välj plustecknet (+) i fönstret **PROJEKT** och välj **Nytt projekt**.

    ![Ny arbetsyta](media\azure-stack-solution-machine-learning\image14.png)

1.  Fyll i formulärfälten och välj knappen **Skapa** för att skapa ett nytt projekt i Workbench.

    | Fält | Föreslaget värde för självstudien | Beskrivning |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Projektnamn | myIris | Ange ett unikt namn som identifierar kontot. Använd de avdelningar eller projekt som bäst identifierar experimentet. Namnet ska bestå av 2 till 32 tecken. Det får endast innehålla alfanumeriska tecken och streck (-). |
    | Projektkatalog | c:\Temp\ | Ange i vilken katalogen projektet ska skapas. |
    | Projektbeskrivning | Lämna tomt | Valfritt fält som kan användas för att beskriva projekten. |
    | URL till GIT-lagringsplats för Visualstudio.com | Lämna tomt | Valfritt fält. Associera ett projekt med en Git-lagringsplats i Visual Studio Team Services för källkontroll och samarbete. [Lär dig hur du ställer in en lagringsplats](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project). |
    | Vald arbetsyta | IrisGarden (om det finns) | Välj en arbetsyta som skapats för experimenteringskonto i Azure-portalen. <br>Med snabbstarten visas arbetsytan med namnet IrisGarden. Annars kan använda arbetsytan med namnet på experimenteringskontot eller en önskad kontonamn. |
    | Projektmall | Klassificera Iris | Mallar innehåller skript och data som används för att utforska produkten. Den här mallen innehåller skript och data som behövs för den här snabbstarten och andra självstudier i den här på dokumentationsplatsen. |

    ![Nytt projekt](media\azure-stack-solution-machine-learning\image15.png)

1.  Ett nytt projekt skapas och instrumentpanelen för projektet öppnas med projektet. Utforska projektets startsida, datakällor, anteckningsböcker och källkodsfiler.

    ![Öppna projekt](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>Bifoga beräkningsmål DSVM

När DSVM har skapats kan du fästa den i Azure ML-projektet.

1.  Från i Azure ML Workbench-appen startar Azure ML Workbench CLI genom att välja **filen**->**öppna PowerShell**

    ![Alternativ text](media\azure-stack-solution-machine-learning\image17.png)

1.  När PowerShell prompten har öppnat använder du följande kommando:

    ```PowerShell  
        az login
    ```

1.  Följande meddelande:

     ![Alternativ text](media\azure-stack-solution-machine-learning\image18.png)

1.  Gå till webbplatsen som beskrivs i Kommandotolken och ange koden som har angetts.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image19.png)

1.  Välj Fortsätt när du uppmanas och sedan Azure ML experimentella kontot är kopplat till det Azure-kontot.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image20.png)

1.  Azure ML Workbench CLI skickar sedan följande meddelande:

    ![Alternativ text](media\azure-stack-solution-machine-learning\image21.png)

1.  När inloggningen för ML-konto och en arbetsyta visas som slutförd kan du bifoga DSVM.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    Följande meddelande visas:

    ![Alternativ text](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

Den här processen kan ta lite tid och genererar en betydande mängd text som den hämtar olika docker-avbildningar, registrerar dem och tillämpar sedan de nödvändiga kod och program till dem.

Experiment kan nu köras på den här DSVM.

### <a name="create-a-data-preparation-package"></a>Skapa ett databearbetningspaket

Därefter kan du börja förbereda data i Azure Machine Learning Workbench. Varje transformering som utförs i Workbench lagras i en JSON-format i ett paket för förberedelse av lokala data (\*.dprep-fil). Det här paketet för förberedelse av data är den primära behållaren för förberedelse av data i Workbench.

Det här paketet för förberedelse av data kan lämnas senare till en runtime, till exempel local-C\#/CoreCLR, Scala/Spark eller Scala/HDI.

1.  Välj mappikonen för att öppna filvyn, välj sedan **iris.csv** för att öppna filen.

    Filen innehåller en tabell med 5 kolumner och 50 rader. Fyra kolumner är numeriska kolumner. Den femte kolumnen är en strängmålkolumn. Ingen av kolumnerna har rubriknamn.

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  I **datavyn** klickar du på plustecknet (**+**) för att lägga till en ny datakälla. Sidan **Lägg till datakälla** öppnas.

    ![Datavy i Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image24.png)

1.  Välj **textfiler (\*.csv, \*.json, \*.txt.,...)** .

    ![Datakällan i Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image25.png)

1.  Välj **Nästa**.

2.  Bläddra till filen **iris.csv**, och välj **Slutför**. Här används standardvärden för parametrar som till exempel avgränsare och datatyper.

    > [!Important]  
    > Välj den **iris.csv** fil från den aktuella projektkatalogen för den här övningen. Annars kan misslyckas senare steg.

    ![Välj iris](media\azure-stack-solution-machine-learning\image26.png)

1.  En ny fil med namnet `*iris-1.dsource` skapas. Filens namn är unikt med `-1` eftersom exempelprojektet redan har en onumrerad **iris.dsource** fil.

    Filen öppnas och data visas. En serie med kolumnrubriker, från **Column1** till **Column5**, läggs automatiskt till i datauppsättningen. Rulla till slutet och Lägg märke till att den sista raden i datauppsättningen är tom. Raden är tom på grund av den extra radbrytningen i CSV-filen.

    ![Datavy för iris](media\azure-stack-solution-machine-learning\image27.png)

1.  Välj knappen **Mått**. Histogram genereras och visas.

    Gå tillbaka till datavyn genom att välja den **Data** knappen.

    ![Datavy för iris](media\azure-stack-solution-machine-learning\image28.png)

1.  Här visas histogram. En komplett uppsättning statistik som har beräknats för varje kolumn.

    ![Datavy för iris](media\azure-stack-solution-machine-learning\image29.png)

1.  Börja skapa ett dataförberedningspaket genom att välja knappen **Förbered**. Dialogrutan **Förbered** öppnas.

    Exempelprojektet innehåller en **iris.dprep** dataförberedningsfil som är valt som standard.

    ![Datavy för iris](media\azure-stack-solution-machine-learning\image30.png)

1.  Skapa ett nytt dataförberedningspaket genom att välja **+ nytt Dataförberedningspaket** på menyn.

    ![Datavy för iris](media\azure-stack-solution-machine-learning\image31.png)

1.  Ange ett nytt värde för paketets namn (med **iris-1**) och välj sedan **OK**.

    Ett nytt dataförberedningspaket med namnet **iris-1.dprep** skapas och öppnas i Redigeraren för dataförberedning.

    ![Datavy för iris](media\azure-stack-solution-machine-learning\image32.png)

    Därefter krävs förberedelse av data.

1.  Markera varje kolumnrubrik för att redigera rubriktexten. Byt sedan namn varje kolumn på följande sätt:

    Måste ange **Sepal Length**, **Sepal Width**, **Petal Length**, **Petal Width**, och **Species** för de fem kolumnerna respektive.

    ![Byta namn på kolumner](media\azure-stack-solution-machine-learning\image33.png)

1.  Räkna distinkta värden:

    1.  Välj kolumnen **Species** (Typer)

    2.  Högerklicka för att välja den.

    3.  Välj **antal värden** på menyn.

        Rutan **Inspectors** (Kontroller) öppnas nedanför data. Ett histogram med fyra staplar visas. Målkolumnen har fyra separata värden: **Iris-virginica**, **Iris-versicolor**,**Iris-setosa**, och en **(null)** värde.

    ![Välj Antal värden](media\azure-stack-solution-machine-learning\image34.png)

    ![Histogram över antal värden](media\azure-stack-solution-machine-learning\image35.png)

1.  Om du vill filtrera bort null-värden väljer du fältet ”(null)” och sedan minustecknet (**-**).

    Sedan blir (null)-raden grå för att visa att den har filtrerats bort.

    ![Filtrera bort null-värden](media\azure-stack-solution-machine-learning\image36.png)

1.  Lägg märke till de enskilda stegen för dataförberedelse som beskrivs i fönstret **STEG**. När är byta namn på kolumner och rader med null-värden är filtrerade, registreras varje åtgärd som ett dataförberedningssteg. Redigera enskilda steg för att justera inställningarna, ändra ordning på stegen och ta bort steg.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image37.png)

1.  Stäng redigeraren för dataförberedning. Stäng fliken genom att välja **x**-ikonen på fliken **iris-1** med grafikonen. Arbetet sparas automatiskt i den **iris-1.dprep** visas under den **Dataförberedelser** rubrik.

    ![Stäng](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>Generera Python-kod som anropar ett dataförberedningspaket

Ett dataförberedningspakets utdata kan utforskas direkt en Python eller Jupyter Notebook. Paketen kan köras i flera körningar inklusive lokal Python, Spark (även i Docker) och HDInsight.

1.  Hitta filen **iris 1.dprep** under fliken Dataförberedelser.

2.  Högerklicka på filen **iris 1.dprep** och välj alternativet för att **generera fil med kod för dataåtkomst** på snabbmenyn.

    ![Generera kod](media\azure-stack-solution-machine-learning\image39.png)

    En ny fil med namnet **iris 1. PY** öppnas med följande rader med kod för att anropa logiken som skapade som ett paket för förberedelse av data:

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    Beroende på kontexten där den här koden körs representerar drep en olika typer av dataramar:

    -  Vid Python-körningar används en [pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -  Vid körning i ett Spark-sammanhang används en [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html).

### <a name="review-irissklearnpy-and-the-configuration-files"></a>Granska iris_sklearn.py och konfigurationsfilerna

1.  I projektet är öppet väljer du den **filer** knappen (mappikonen) längst till vänster i fönstret att öppna fillistan i projektmappen.

    ![Öppna ett projekt i Azure Machine Learning Workbench](media\azure-stack-solution-machine-learning\image40.png)

1.  Välj Python-skriptfilen **iris_sklearn.py**.

    ![Välj ett skript](media\azure-stack-solution-machine-learning\image41.png)

    Koden öppnas på en ny textredigeringsflik i Workbench.

    > [!Note]  
    > Den kod som visas kanske inte exakt samma som föregående kod eftersom det här exempelprojektet uppdateras ofta.

    ![Öppna en fil](media\azure-stack-solution-machine-learning\image42.png)

1.  Granska Python-skriptkoden så att du bekantar dig med kodningsstilen.

    Skriptet **iris_sklearn.py** utför följande uppgifter:  

    -  Standardversionen av dataförberedningspaketet **iris.dprep** läses in för att skapa en [pandas-DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -   Slumpvis utvalda funktioner läggs till som gör det svårare att lösa problemet. Slumpmässigheten behövs eftersom Iris är en liten datamängd som enkelt kan klassificeras med nästan 100 % noggrannhet.

    -  Använder thescikit learnmachine learning-biblioteket för att skapa en logistic regressionsmodell. Det här biblioteket innehåller Azure Machine Learning Workbench som standard.

    -  Serialiserar modellen genom att använda biblioteket [pickle](https://docs.python.org/3/library/pickle.html) i en fil i mappen `outputs`.

    -  Läser in den serialiserade modellen och deserialiserar den tillbaka till minnet.

    -  Den deserialiserade modellen används till att göra en förutsägelse för en ny post.

    -  Ritar två diagram, en felmatris och en receiver driva egenskap (ROC) kurvan, med hjälp av den [matplotlib](https://matplotlib.org/) biblioteket, och sparar dem i theoutputsfolder. Installera det här biblioteket i miljön om inte tydligt.

    -  Ritar regularisering regulariseringshastigheten och korrektheten i körningshistoriken automatiskt. Objektet `run_logger` används kontinuerligt för att lagra regulariseringshastigheten och modellnoggrannheten i loggarna.

### <a name="run-irissklearnpy-in-the-local-environment"></a>Kör iris_sklearn.py i den lokala miljön

1.  Starta kommandoradsgränssnittet (CLI) för Azure Machine Learning:

    1.  Starta Azure Machine Learning Workbench.

    2.  Gå till menyn i Workbench och välj **Arkiv**> **Öppna kommandotolken**.

    Fönstret Azure Machine Learning-kommandoradsgränssnittet (CLI) startar i projektmappen `C:\Temp\\myIris\` på Windows. Det här projektet är detsamma som det som du skapade i del 1 av självstudiekursen.

    > [!Important]  
    > Använd det här CLI-fönstret för att slutföra de nästkommande stegen.

1.  Installera Python ritområdet biblioteket i CLI-fönstret **matplotlib**, om inte redan är installerat.

    Skriptet **iris_sklearn.py** är beroende av två Python-paket: **scikit-learn** och **matplotlib**. Den **scikit-Läs** paketet har installerats av Azure Machine Learning Workbench för att underlätta. Ändå har installationen av **matplotlib** kan krävas.

    Om du fortsätter utan att installera **matplotlib**, koden i den här självstudiekursen kan du ändå köra. Däremot kan koden inte producera felmatrisen eller ROC-kurvan för flera klasser i visualiseringen av historiken.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    Den här installationen tar ungefär en minut.

1.  Gå tillbaka till Workbench-programmet.

2.  Hitta fliken med namnet **iris_sklearn.py**.

    ![Hitta flik med skript](media\azure-stack-solution-machine-learning\image43.png)

1.  I verktygsfältet på fliken väljer **lokala** som körningsmiljö andiris_sklearn.pyas skriptet att köra. De kan redan vara förvalda.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image44.png)

1.  Flytta till höger i verktygsfältet och enter0.01in den **argument** fält.

    Det här värdet motsvarar regulariseringshastigheten för logistic regression-modellen.

    ![Val mellan lokal och skript](media\azure-stack-solution-machine-learning\image45.png)

1.  Klicka på knappen **Kör**. Ett jobb schemaläggs omedelbart. Jobbet står med i fönstret **Jobb** till höger i Workbench-fönstret.

    ![Val mellan lokal och skript](media\azure-stack-solution-machine-learning\image46.png)

    Efter en liten stund status för jobbet övergår från **skickar**till **kör**, och slutligen till **slutförd**.

1.  Välj **Slutfört** i jobbstatustexten i fönstret **Jobb**.

    ![Kör sklearn](media\azure-stack-solution-machine-learning\image47.png)

    Ett popup-fönster öppnas och visar standardutdata (stdout) texten för körningen. Om du vill stänga stdout-texten, Välj den **Stäng** (**x**) längst upp till höger i popup-fönstret.

    ![Standardutdata](media\azure-stack-solution-machine-learning\image48.png)

1.  I samma Jobbstatus i den **jobb** fönstret väljer du den blå texten **iris_sklearn.py \[n\] **(* n * är körningsnumret) precis ovanför den  **Slutfört** status och starttiden. Fönstret **Körningsegenskaper** öppnas och visar följande information för den specifika körningen:

    -  Information om **Körningsegenskaper**

    -  **Utdata**

    -  **Mått**

    -  Eventuella **Visualiseringar**

    -  **Loggar**

    När körningen har slutförts visas följande resultat i popup-fönstret:

    > [!Note]  
    > Eftersom självstudiekursen introducerade vissa slumpmässigheter i inlärningsuppsättningen kommer de exakta resultaten att variera från de resultat som visas här.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  Stäng fliken **Körningsegenskaper** och återgå sedan till fliken **iris_sklearn.py**.

1.  Upprepa för flera körningar.

Ange en serie med värden i fältet **Argument**, från `0.001` till `10`. Välj **Kör** för att köra koden några gånger till. Argumentvärdet ändras varje gång skickas till logistic regression-modellen i koden, vilket ger olika resultat varje gång.

### <a name="review-the-run-history-in-detail"></a>Granska körningshistoriken i detalj

I Azure Machine Learning Workbench avbildas varje skriptkörning som en post i körningshistoriken. Visa körningshistoriken för ett visst skript genom att öppna den **körs** vy.

1.  Välj knappen **Körningar** (klockikonen) i det vänstra verktygsfältet för att öppna listan över **Körningar**. Välj sedan **iris_sklearn.py** att visa den **instrumentpanelen för körningar** ofiris_sklearn.py.

    ![Körningsvy](media\azure-stack-solution-machine-learning\image49.png)

1.  Fliken med **Instrumentpanelen för körningar** öppnas.

    Granska statistiken som sparats från de många körningarna. Diagram återges längst upp på fliken. Varje körning har ett på varandra efterföljande nummer och körningsinformation visas i tabellen längst ned på skärmen.

    ![Instrumentpanelen för körningar](media\azure-stack-solution-machine-learning\image50.png)

1.  Filtrera tabellen och välj sedan något av diagrammen för att visa status, längd, noggrannhet och regulariseringshastighet för varje körning.

2.  Markera kryssrutorna bredvid två eller flera körningar i tabellen **Körningar**. Klicka på knappen **Jämför** för att öppna ett fönster med en detaljerad jämförelse. Granska jämförelsen.

3.  Välj bakåtknappen **Körningslista** uppe till vänster i **Jämförelsefönstret** så att du återgår till **Instrumentpanelen för körningar**.

    ![Gå tillbaka till körningslistan](media\azure-stack-solution-machine-learning\image51.png)

1.  Välj en enskild körning så att detaljvyn visas. Observera att statistiken för den valda körningen visas i avsnittet **Körningsegenskaper**. Filerna som skrivits till Utdatamappen visas i den **utdata** avsnittet och ladda ned filerna därifrån.

    ![Körningsinformation](media\azure-stack-solution-machine-learning\image52.png)

De två diagrammen, felmatrisen och ROC-kurvan för flera klasser, återges i avsnittet **Visualisering**. Du hittar även alla loggfiler i avsnittet **Loggar**.

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Köra skript i Azure Machine Learning (ML) Workbench CLI-fönstret

1.  Starta kommandoradsgränssnittet (CLI) för Azure Machine Learning:

    1.  Starta Azure Machine Learning Workbench.

    2.  Gå till menyn i Workbench och välj **Arkiv** > **Öppna kommandotolken**.

    Kommandotolken startar i projektmappen `C:\\Temp\\myIris` på Windows. Det här är projektet skapade i del 1 av självstudiekursen.

    > [!Important]  
    > Använd det här CLI-fönstret för att slutföra de nästkommande stegen.

1.  Logga in på Azure i CLI-fönstret. [Läs mer om az-inloggning](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    Hoppa över det här steget om du redan har loggat in.

1.  Skriv följande i kommandotolken:

    ```CLI
        az login
    ```

    Det här kommandot returnerar en kod som ska användas när webbläsaren [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin).

1.  Gå till [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) i webbläsaren. Ange koden som du har fått från den mottagna i CLI.

    Workbench-appen och CLI använder oberoende cacheminnen vid autentisering mot Azure-resurser. Autentisering krävs inte igen tills token i cacheminnet upphör att gälla.

1.  Om organisationen har flera Azure-prenumerationer (företagsmiljö), anger du prenumerationen som ska användas. Hitta prenumerationen, ange den med hjälp av prenumerations-ID och sedan testa den.

1.  Lista över alla används Azure-prenumerationer med hjälp av det här kommandot:

    ```CLI
        az account list -o table 
    ```

    Den **az kontolista** kommando returnerar listan över tillgängliga prenumerationer för inloggningen. Om det finns fler än en identifiera prenumerationens ID-värde för den prenumeration som används.

1.  Ange Azure-prenumeration används som standardkonto:

    ```CLI
        az account set -s <the-subscription-id
    ```

    Där < the – prenumerations-id > är ID: t för den prenumeration som används. Ta inte med hakparenteserna.

1.  Bekräfta de nya inställningarna för prenumerationen genom att begära ut information om den aktuella prenumerationen.

    ```CLI
        az account show
    ```

1.  Gå till CLI-fönstret och skicka skriptet **iris_sklearn.py** som ett experiment.

    Körningen av iris_sklearn.py körs mot den lokala beräkningskontexten.

1.  I Windows:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  I macOS:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    Utdata bör se ut: 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  Granska utdata. Alla bör ha samma utdata och resultat som när arbetsstationen kör skriptet.

7.  Gå tillbaka till Workbench och gör följande:

    Välj mappikonen i den vänstra panelen för att visa en lista med alla projektfiler.  Öppna Python-skriptet med namnet **run.py**. Det här skriptet är användbart om du vill loopa över olika regulariseringshastigheter. 

    ![Gå tillbaka till körningslistan](media\azure-stack-solution-machine-learning\image53.png)

1.  Kör experimentet flera gånger med dessa hastigheter.

    Det här skriptet startar` aniris_sklearn.pyjob` med en regulariseringshastighet rate-o `10.0` (ett löjligt stort tal). Skriptet halverar sedan till hälften i följande körning och så vidare tills hastigheten är mindre än `0.005`. Skriptet innehåller följande kod:

    ![Gå tillbaka till körningslistan](media\azure-stack-solution-machine-learning\image54.png)

1.  Kör skriptet **run.py** från kommandoraden enligt följande:

    ```CLI
        python run.py
    ```
Det här kommandot skickar `iris_sklearn.py` flera gånger med olika regulariseringshastigheter.

När `run.py` har slutförts diagram med olika värden visas i listvyn för körningshistoriken i workbench.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Köra skript i en Ubuntu-baserad Data Science Virtual Machine (DSVM) på Azure

Om du vill köra skriptet i en Docker-behållare på en fjärrdator med Linux, behövs SSH-åtkomst (användarnamn och lösenord) för att köra fjärrdatorn. Dessutom måste datorn ha en Docker-motor installerad och igång.

1.  Redigera den genererade<your DSVM Name>.runconfigfile underaml_configand ändra ramverket från standardvärdet valuePySparktoPython:

    ```yaml  
    Framework: Python
    ```
1.  Kör samma kommando som tidigare i CLI-fönstret, med målet*<DSVM>* den här körningen av iris_sklearn.PY i en fjärransluten dockerbehållare: (Ersätt den <DSVM> med Data Science VM-namnet, utan hakparenteserna).

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

Kommandot körs som om du är i adocker-pythonenvironment, förutom att körningen sker på den fjärranslutna Linux-VM. Samma utdata visas i CLI-fönstret.

### <a name="download-the-model-pickle-file"></a>Ladda ned pickle-modellfilen

I föregående del av självstudien kördes skriptet **iris_sklearn.py** i Machine Learning Workbench lokalt. Den här åtgärden serialiserade Logistic Regression-modellen med hjälp av det populära Python-paketet för objektserialisering [pickle](https://docs.python.org/3/library/pickle.html).

1.  Öppna programmet Machine Learning Workbench. Öppna sedan den **myIris** projektet har skapats i de tidigare delarna av självstudieserien.

2.  När projektet är öppet väljer du den **filer** knappen (mappikonen) i det vänstra fönstret för att öppna fillistan i projektmappen.

3.  Välj filen **iris_sklearn.py**. Python-koden öppnas på en ny textredigeringsflik i Workbench.

4.  Granska filen **iris_sklearn.py** och se efter var pickle-filen genererades. Använd CTRL + F för att öppna dialogrutan **Sök** och leta efter ordet **pickle** i Python-koden.

Det här kodfragmentet visar hur pickle-utdatafilen skapades. Pickle-utdatafilen heter **model.pkl** på disken.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  Leta rätt på pickle-modellfilen bland utdatafilerna från en tidigare körning.

    När den **iris_sklearn.py** skriptet körs, modellfilen skrivs till den **matar ut** mapp med namnet **model.pkl**. Den här mappen finns i körningsmiljön valt att köra skriptet och inte i den lokala projektmappen. 

    1. Om du vill leta reda på filen, Välj den **körs** (klockikonen) i det vänstra fönstret för att öppna listan över **alla körningar**.  

    2. Fliken **Alla körningar** öppnas. I tabellen, väljer du en av de senaste körningarna där målet var **lokala** och namnet på skriptet var **iris_sklearn.py**.  

    3. Fönstret **Körningsegenskaper** öppnas. I den övre högra delen av fönstret lägga märke till de **utdata** avsnittet. d\. Ladda ned pickle-filen genom att markera kryssrutan bredvid den **model.pkl** och väljer sedan **hämta**. Spara filen i roten för projektmappen. Filen behövs i kommande steg.  

    ![Ladda ned pickle-filen](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>Hämta bedömning och filer

Om du vill distribuera webbtjänsten tillsammans med modellfilen krävs bedömning skriptet. Du kan också krävs ett schema för indata till web service. Bedömningsskriptet läser in filen **model.pkl** från den aktuella mappen och använder den för att skapa nya förutsägelser.

1.  Öppna programmet Machine Learning Workbench. Öppna sedan den **myIris** projektet har skapats i den förra delen av självstudieserien.

2.  När projektet är öppet väljer du den **filer** knappen (mappikonen) i det vänstra fönstret för att öppna fillistan i projektmappen.

3.  Välj filen **iris_score.py**. Python-skriptet öppnas. Den här filen används som bedömningsfil.

    ![Bedömningsfil](media\azure-stack-solution-machine-learning\image56.png)

1.  Kör skriptet för att skapa schemafilen. Välj miljön **lokalt** och skriptet **score_iris.py** i kommandofältet och välj sedan **Kör**.

    Det här skriptet skapar en JSON-fil i den **utdata** avsnittet, som fångar det indataschema som behövs i modellen.

1.  Notera fönstret **Jobb** till höger om fönstret **Project Dashboard** (Instrumentpanel för projekt). Vänta tills det senaste ** score_iris.py** jobbet visar gröna **slutförd** status. Välj hyperlänken **score_iris.py** för den senaste jobbkörningen så att du ser körningsdetaljerna för körningen.

2.  I avsnittet **Utdata** på sidan **Körningsegenskaper** väljer du den nya filen **service_schema.json**. Markera kryssrutan bredvid namnet på filen och välj sedan **Ladda ned**. Spara filen i rotmappen för projektet.

3.  Gå tillbaka till den föregående fliken och **score_iris.py** skript. Modellindata och förutsägelser från webbtjänsten kan hämtas med hjälp av insamling av data. Följande steg är intressanta vid datainsamling.

4.  Granska koden längst upp i filen, som importerar klassen **ModelDataCollector**. Den innehåller modellens datainsamlingsfunktioner:

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  Granska följande kodrader i funktionen **init()** som skapar en instans av **ModelDataCollector**:

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  Granska följande kodrader i funktionen **run(input_df)** eftersom den samlar in indata och förutsägelser:

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

Förbered nu miljö för att operationalisera modellen.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>Steg 5: Distribuera och använda Azure Container Registry

Distribuera och använda Azure Container Registry.

Skapa ett Azure-containerregister med kommandot **az acr create**. Registernamnet måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Resursgruppen är samma.

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>Logga in på containerregistret

Använd kommandot **az acr login** och logga in på ACR-instansen. Ange det unika namn du angav för containerregistret när det skapades.

    ```CLI
        az acr login --name <acrName>
    ```

Kommandot returnerar en ' inloggningen har slutförts meddelande när det har slutförts.

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>Förbereda för att operationalisera lokalt för utveckling och testning av tjänsten

Använd *lokalt läge* distribution för att köra i Docker-behållare på den lokala datorn och för utveckling och testning.

Docker-motorn måste köras lokalt när du ska utföra följande steg och operationalisera modellen. Använd den `-h` flaggan i slutet av varje kommando för att visa motsvarande hjälpmeddelande.

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  Öppna kommandoradsgränssnittet (CLI). Välj på **Öppna kommandotolken** i menyn **Arkiv** i Machine Learning Workbench.

    Kommandotolken öppnas i den aktuella projektmappen **c:\\temp\\myIris**.

1.  Kontrollera att Azure-resursprovidern **Microsoft.ContainerRegistry** är registrerad i prenumerationen. Registrera den här resursprovidern innan du skapar en miljö i steg 3. Kontrollera om den redan är registrerad med hjälp av följande kommando:

    ```CLI
        az provider list --query "\[\].{Provider:namespace, Status:registrationState}" --out table
    ```

    Visa dessa utdata:

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    Om **Microsoft.ContainerRegistry** är inte registrerad att använda följande kommando:

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    Registreringen kan ta några minuter. Kontrollera registreringsstatus med hjälp av den tidigare **az provider list** kommando eller med följande kommando:

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    Den tredje utdataraden visar **"registrationState": "Registering"**. Vänta en stund och upprepa den **visa** tills utdata visar **”registrationState”: ”Registered.**

1.  Skapa miljön. Kör det här steget en gång per miljö.

    Följande installationskommando kräver deltagarbehörighet för prenumerationen. Deltagaråtkomst till distribution till resursgruppen krävs. Ange resursgruppens namn som en del av installationskommandot med hjälp av the gflag.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    Följ anvisningarna på skärmen för att etablera ett lagringskonto för lagring av Docker-avbildningar, en Azure Container Registry för listning av Docker-avbildningar och ett Azure Application Insights-konto som samlar in telemetri. **Om du använder den cswitch kommandot dessutom skapar ett Container Service-kluster**.

    Klusternamnet är ett sätt att identifiera miljön. Platsen måste vara samma som platsen för modellhanteringskontot som skapats från Azure-portalen.

    Kontrollera statusen med följande kommando för att se om miljön är rätt konfigurerad:

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    Se till att ”Provisioning State” har värdet ”Succeeded”, som visas innan du ställer in miljön i steg 5:

    ![Etableringsstatus](media\azure-stack-solution-machine-learning\image57.png)

1.  Konfigurera miljön.

    När installationen är klar använder du följande kommando för att ställa in de miljövariabler som behövs för operationalisering av miljön. Använd samma miljönamn som använde tidigare i steg 3. Använd samma resursgruppnamn som angavs i kommandofönstret när installationen slutfördes.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  Ange följande kommando för att verifiera korrekt konfiguration av produktionsslutpunkt miljön för distribution av lokala webbtjänster:

    ```CLI
    az ml env show
    ```

    Nu kan skapa realtidswebbtjänsten.

    > [!Note]  
    > Återanvända modellhanteringskontot och miljö för efterföljande webbtjänstdistributioner. Det finns inget behov att skapa dem för varje webbtjänst. Ett konto eller en miljö kan ha flera webbtjänster kopplade till sig.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Skapa en realtidswebbtjänst med separata kommandon

Som ett alternativ till den **az ml service skapa realtime** tidigare visades dessa åtgärder kan också utföras separat.

Registrera först modellen. Generera sedan manifestet, skapa Docker-avbildningen och skapa webbtjänsten. Den här stegvisa metoden får mer flexibilitet vid varje steg. Dessutom kan entiteterna som genererats i föregående steg återanvändas.

1. Registrera modellen genom att ange pickle-filnamnet

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    Det här kommandot genererar ett modell-ID.

2.  Skapa ett manifest.

    Använd följande kommando för att skapa ett manifest, och ange modell-ID: t från föregående steg: 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    Det här kommandot genererar ett manifest-ID.

3.  Skapa en Docker-avbildning.

    Om du vill skapa en Docker-avbildning, använder du följande kommando och ange manifest ID: t från föregående steg. Conda-beroenden kan även användas den `-c` växla. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    Det här kommandot genererar ett Docker-avbildnings-ID.

2.  Skapa tjänsten.

    Skapa en tjänst, använder du följande kommando och ange avbildnings-ID: T t från föregående steg: 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    Det här kommandot genererar ett webbtjänst-ID.
    
    Kör webbtjänsten.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>Steg 6: Distribuera ett Kubernetes-kluster i Azure Stack

Distribuera ett Kubernetes-kluster till Azure Stack.

Kubernetes kan installeras med hjälp av Azure Resource Manager-mallar som genereras av Azure Container Services (ACS)-motorn på Azure Stack. [*Kubernetes* ](https://kubernetes.io/) är ett system med öppen källkod för att automatisera distribution, skalning och hantering av program i behållare. En [ *behållare* ](https://www.docker.com/what-container) finns i en avbildning, som en virtuell dator. Till skillnad från en virtuell dator innehåller behållaravbildningen de resurser den behöver för att köra ett program, till exempel kod, runtime för att köra koden, specifika bibliotek och inställningar.

Använd Kubernetes:

 -  Utveckla mycket skalbar och kan uppgraderas, program som kan distribueras på några sekunder.

 -  Förenkla utformningen av programmet och förbättra tillförlitligheten genom olika Helm-program. [*Helm* ](https://github.com/kubernetes/helm) är ett verktyg med öppen källkod paketering som hjälper dig att installera och hantera livscykeln för Kubernetes-program.

 -  Enkelt övervaka och diagnostisera hälsotillståndet för program med skala och uppgradera funktioner.

> [!Note]  
> Installation av klustret tar ungefär en timme planera på lämpligt sätt.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Krav för distribution av Kubernetes-kluster

Kontrollera behörigheter och Azure Stack-beredskap för att komma igång:

1.  Kontrollera att den **skapa Kubernetes-kluster (förhandsversion)** objektet är tillgängligt i Azure Stack Marketplace. Om det här objektet saknas, fungerar med en Azure Stack-operatör att lägga till det här objektet i Azure Stack-miljön.

2.  Kontrollera möjlighet att skapa program i Azure Active Directory (Azure AD)-klient. Behörigheter som krävs för Kubernetes-distribution.

    Anvisningar om hur du felsöker behörigheterna som finns i [ *Kontrollera Azure Active Directory-behörigheter*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

3.  Skapa en SSH offentlig och privat nyckel att logga in på Linux VM på Azure Stack. Den offentliga nyckeln behövs när du skapar klustret. Anvisningar finns i [Generera en autentiseringsnyckel för SSH](#generate-an-authenticatio-key-for-ssh).

4.  Kontrollera att prenumerationen på Azure Stack-klientportal är giltig och att det finns tillräckligt med offentliga IP-adresser tillgängliga att lägga till nya program.

    Klustret kan inte distribueras till ett Azure Stack **administratör** prenumeration. Använd en **användaren** prenumeration.

###  <a name="generate-an-authentication-key-for-ssh"></a>Generera en autentiseringsnyckel för SSH

Använd följande kommandon för att generera en autentiseringsnyckel i Windows-undersystem för Linux-Session från: 

1. Ange:

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. Välj `id_rsa` när du tillfrågas. 
3. Skapa en lösenfras när du tillfrågas. Det är viktigt att Observera det här lösenordet för senare användning. 
    Utdata ser ut ungefär som nedan: 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Alternativ text](media\azure-stack-solution-machine-learning\image58.png)

4. Klistra in viktig information med hjälp av följande kommandon när du har genererat nyckeln: 
    ```Bash
    cat id_rsa.pub
    ```
    Utdata ser ut ungefär som nedan: 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. Kopiera den genererade nyckeln till SSH offentlig nyckelfältet.

### <a name="create-a-service-principal-in-azure-ad"></a>Skapa ett huvudnamn för tjänsten i Azure AD

1.  Logga in på den globala [ *Azure-portalen*](http://www.poartal.azure.com/).

2.  Logga in med Azure AD-klient som är associerade med Azure Stack-instans.

3.  Skapa ett Azure AD-program.

    1. Välj **Azure Active Directory** > **+ Appregistreringar**> **ny programregistrering**.
    2. Ange en **namn** av programmet. 
    3. Välj **webbapp / API**. 
    4. Ange `http://localhost` för den **inloggnings-URL**. 
    5. Välj **Skapa**.

1.  Anteckna **Program-ID**. Detta ID behövs när du skapar klustret och refereras till som **klient-ID för tjänstens huvudnamn**.

2.  Välj **inställningar** > **nycklar**.

    1. Ange den **beskrivning**. 
    2. Välj **upphör aldrig att gälla** för **förfaller**. 
    3. Välj **Spara**. Kontrollera att Observera viktiga strängen. Strängen som nyckel krävs när du skapar klustret och refereras till som den **Klienthemlighet för tjänstens huvudnamn**.

### <a name="give-the-service-principal-access"></a>Ge tjänstens huvudnamn åtkomst

Ge tjänstens huvudnamn åtkomst till prenumerationen så att den kan skapa resurser.

1.  Logga in på den [administrationsportalen](https://adminportal.local.azurestack.external/).

2.  Välj **fler tjänster** > ** användarprenumerationer ** > **+ Lägg till**.

3.  Välj den prenumeration som skapats.

4.  Välj **åtkomstkontroll (IAM)** > Välj **+ Lägg till**.

5.  Välj den **ägare** roll.

6.  Välj namnet på programmet som skapats för tjänsten huvudnamn. Ange namnet i sökrutan om det behövs.

7.  Välj **Spara**.

8.  Öppna den [Azure Stack-portalen](https://portal.local.azurestack.external).

9.  Välj **+ ny** > **Compute** > **Kubernetes-kluster**. Välj **Skapa**.

    ![Distribuera lösningsmall](media\azure-stack-solution-machine-learning\image59.png)

10\. Välj **grunderna** i den skapa Kubernetes-kluster.

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. Ange den **Linux VM-administratörsanvändarnamn**. Användarnamn för Linux-datorer som ingår i Kubernetes-klustret och DVM.

12. Ange den **offentlig SSH-nyckel** används för alla Linux-datorer som skapats som en del av Kubernetes-kluster och DVM godkännande.

13. Ange den **Master profil DNS-Prefix** som är unikt för regionen. Det här måste vara ett region – unikt namn, till exempel `ask8s-12345`. Försök att välja det samma som resursgruppen namn som bästa praxis.

    > [!Note]  
    > Använda en DNS-prefix för nya och unika master profil för varje kluster.

14. Ange den **Agentantal Pool profil**. Antalet innehåller antalet agenter i klustret. Det kan vara från 1 till 4.

15. Ange den **tjänstens huvudnamn ClientId** används av providern för Kubernetes Azure-molnet.

16. Ange den **Klienthemlighet för tjänstens huvudnamn** skapas när du skapar service principal program.

17. Ange den **Kubernetes Azure Cloud-providerversion**. Det här är version för Kubernetes Azure-providern. Azure Stack släpper en anpassad Kubernetes-version för varje Azure Stack-version.

18. Välj den **prenumeration** -ID.

19. Ange namnet på en ny resursgrupp eller välj en befintlig resursgrupp. Resursnamnet måste vara alfanumeriskt och gemener.

20. Välj den **plats** för resursgruppen. Det här är den region som valts för installationen av Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Ange inställningar för Azure Stack

1.  Välj den **inställningar för Azure Stack-stämpel**.

    ![Distribuera lösningsmall](media\azure-stack-solution-machine-learning\image61.png)

2.  Ange den **klient-Azure Resource Manager-slutpunkten**. Det här är Azure Resource Manager-slutpunkt att ansluta för att skapa resursgruppen för Kubernetes-klustret. Slutpunkten från Azure Stack-operatör krävs för ett integrerat system. För de Azure Stack Development Kit (ASDK), Använd `https://management.local.azurestack.external`.

3.  Ange den **klient-ID** för klienten. Se [ *hämta klient-ID* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) att hitta innehavaren ID-värdet.

### <a name="install-kubectl-on-windows-powershell-environment"></a>Installera kubectl på Windows PowerShell-miljö

Kör följande kommandon för att installera kubectl i WSL-miljö i WSL miljön från.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Installera kubectl i Windows-undersystem för Linux-miljö

Kör följande kommandon för att installera kubectl i WSL miljön från i WSL-miljön.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>Konfigurera kubectl

För att kubectl att hitta och komma åt ett Kubernetes-kluster ett*kubeconfig filen* krävs. Detta skapas automatiskt när ett kluster skapas med hjälp av kube-up.sh eller distribuera ett Minikube-kluster. Se den [ *komma igång guider* ](https://kubernetes.io/docs/setup/) mer information om hur du skapar kluster. Åtkomst till ett kluster som skapats av en annan användare kan se den [ *delning Klusteråtkomst dokumentet*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). Som standard kubectl konfiguration finns i **~.kube/config**.

### <a name="check-the-kubectl-configuration"></a>Kontrollera konfigurationen för kubectl

Kontrollera att kubectl har konfigurerats korrekt genom att hämta status för klustret:

```Bash  
kubectl cluster-info
```

kubectl är korrekt konfigurerad för att få åtkomst till klustret när svaret visas.

kubectl är inte korrekt konfigurerad eller går inte att ansluta till ett Kubernetes-kluster om följande meddelande visas:

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

Till exempel när du kör ett Kubernetes-kluster på en lokal bärbar dator, ett verktyg som kan behövas (minikube eller liknande) köra kommandon som anges ovan.

Om kubectl-klusterinformation returnerar svaret url, men klustret är fortfarande inte komma åt, Sök efter korrekt konfiguration med hjälp av:

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>Aktivera shell kommandofönstret Slutför automatiskt

kubectl omfattar stöd för kommandofönstret Slutför automatiskt, vilket gör shell aktivering snabbt och enkelt.

Skriptet slutförande genereras av kubectl och kan nås från profilen.

### <a name="connect-to-the-kubernetes-cluster"></a>Ansluta till Kubernetes-kluster

Att ansluta till klustret, Kubernetes kommandoradsklient (**kubectl**) krävs. Anvisningar för att ansluta till och hantera klustret finns i [dokumentation om Azure Container Services.](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

Alla SSH-klienten kan användas för att ansluta Kubernetes-klustret. Windows-undersystem för Linux (WSL) rekommenderas. Den dator som ansluter till Kubernetes-kluster i resursgruppen som skapades för klustret och börjar med prefixet för vmd edge-ml-stack.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

När du är ansluten till Kubernetes-dator, kör du följande datorn för att hämta Kubernetes-konfigurationsfil.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

Utdata kommer ser ut ungefär så här:

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Kopiera filer sökväg informationen och kör sedan följande kommando, klistra in sökvägen till kubeconfig kopieras från ovan:

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Följande utdata returneras ett stort block med text som är det råa JSON-innehållet. Kopiera utdata texten och klistra in den här koden i en Visual Studio-fil, Spara som en JSON-fil. Detta resulterar i en lokalt lagrad kubeconfig.json-fil. (sparatill/mnt/c/användare/ <current user> /dokument/Kube-katalog som kubeconfig.json)

### <a name="configure-the-kubernetes-cluster"></a>Konfigurera Kubernetes-kluster

När den lokala JSON-filen hämtas i en ny WSL-session använder du följande kommandon för att konfigurera klustret.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

Konfigurationsinställningar för Kubernetes definieras (se utdata nedan).

![Alternativ text](media\azure-stack-solution-machine-learning\image62.png)

Starta proxytjänsten lokala:

```Bash  
kubectl proxy
```

Bläddra till kubernetes-kluster Användargränssnittet på följande adress: `https://localhost:8001`.

![Alternativ text](media\azure-stack-solution-machine-learning\image63.png)

Nu har du en plats för att distribuera behållare och en behållare som finns i molnet som du kan se på plats.

![Alternativ text](media\azure-stack-solution-machine-learning\image64.png)

Anpassa den **iris_deployment.yaml** fil (i /*mnt/c/användare/<current user>/dokument/Kube directory*) så **webservicename** och behållare  **Bild** och **namn** matchar distribution, med valfri Kodredigerare val.

![Alternativ text](media\azure-stack-solution-machine-learning\image65.png)

Ange porten behållare som **5001.**

![Alternativ text](media\azure-stack-solution-machine-learning\image66.png)

Och sedan skapa den **imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>Skapa en hemlighet i klustret som innehåller autentiseringstoken

Ett Kubernetes-kluster använder hemligheten för **docker-registry** Skriv för att autentisera med ett behållarregister för att hämta en privat avbildning.

Skapa den här hemligheten namnger det. **azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

Hitta:

- **< your-registry-server >** är Azure Container Registry **inloggningsserver**.
- **< ditt namn >** är Azure Container Registry **användarnamn**.
- **< your pword >** är Azure Container Registry **lösenord**. Kontrollera att lösenordet är inom citattecken.
- **< din e-postadress >** är den användare som har R/W åtkomst till behållaren.
- Hitta den här informationen på den **Azure Container** **registret** under **åtkomstnycklar**.
- Docker-autentiseringsuppgifter anges nu i klustret som en hemlighet **azuremlcr**.

Spara den **iris_deployment.yaml** fil (i /*mnt/c/användare/<current user>/dokument/Kube directory*).

### <a name="create-the-kubernetes-container"></a>Skapa Kubernetes-behållare

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

Kontrollera status för distributionen:

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

Distributionen kan ta lite tid.

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>Konfigurera Visual Studio Team Services distribueras automatiskt

#### <a name="create-a-team-project"></a>Skapa ett teamprojekt

1.  Se till att [gruppmedlemskap för projektet samling administratörer.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) Skapa grupprojekt, **skapa nya projekt** behörighet måste anges till **Tillåt**.

2.  Välj sidan projekt **nytt projekt**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image69.png)

1.  Ge projektet namnet **HybridMLIris**.

2.  Välj dess inledande källkontrolltyp som **Git**

3.  Välj en process och välj **skapa**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>Importera kod skapa lagringsplatsen

En Git-lagringsplats för YAML-kod krävs.

#### <a name="add-user-to-the-git-repo"></a>Lägga till användare till GIT-lagringsplats

1.  Standard-instrumentpanelen för projektet väljer du generera Git-autentiseringsuppgifter.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image71.png)

1.  Ange lösenord där det krävs för att spara autentiseringsuppgifter för Git.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image72.png)

1.  Initiera databasen genom att välja den **initiera** knappen och skapa en **README** fil.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Klona Git-lagringsplatsen lokalt och överföra koden. 

1.  Skapa en katalog på datorn vid `c:\\users\\<User>\\source\\repos\\hybridMLIris`, och klonar lagringsplatsen

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Alternativ text](media\azure-stack-solution-machine-learning\image74.png)

1.  Gå till nyligen klonad lagringsplats:

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Alternativ text](media\azure-stack-solution-machine-learning\image75.png)

1.  Kopiera den **iris_deployment.yaml** -filen i databasen.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  Spara ändringen i GIT

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Alternativ text](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>Förbereda privata Infrastruktursgenereringsinställningar och versionen Agent för VSTS-integrering

#### <a name="prerequisites"></a>Förutsättningar

VSTS autentiseras mot Azure Resource Manager med ett huvudnamn för tjänsten. För VSTS för att kunna etablera resurser i en Azure Stack-prenumeration krävs deltagare status. \ **följande är de övergripande stegen som måste konfigureras för att aktivera sådana autentisering:**

1.  Tjänstens huvudnamn ska skapas eller en befintlig kan användas.

2.  Autentiseringsnycklar måste skapas för tjänstens huvudnamn.

3.  Azure Stack-prenumeration måste verifieras via rollbaserad åtkomstkontroll för att tillåta SPN-namnet vara en del av rollen deltagare.

4.  En ny tjänstdefinition i VSTS måste skapas med hjälp av Azure Stack-slutpunkter samt SPN-information.

#### <a name="service-principal-creation"></a>Skapa tjänstens huvudnamn

Referera till den [tjänstens huvudnamn skapas instruktioner](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) att skapa ett huvudnamn för tjänsten och välj Web App/API som programtyp.

**Skapa nyckel för åtkomst**

Ett huvudnamn för tjänsten krävs en nyckel för autentisering, följer du stegen i det här avsnittet för att generera en nyckel.

1.  Från **appregistreringar** i Azure Active Directory, väljer du programmet.

    ![välj program](media\azure-stack-solution-machine-learning\image77.png)

1.  Anteckna värdet för **program-ID. Värdet används när du konfigurerar tjänstslutpunkten i VSTS.**

    ![Alternativ text](media\azure-stack-solution-machine-learning\image78.png)

1.  Välj **Inställningar** om du vill generera en autentiseringsnyckel.

    ![välj inställningar](media\azure-stack-solution-machine-learning\image79.png)

1.  Välj **Nycklar**.

    ![välj nycklar](media\azure-stack-solution-machine-learning\image80.png)

1.  Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.

    ![spara nyckel](media\azure-stack-solution-machine-learning\image81.png)

När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet när det behövs senare. Den **nyckelvärdet** med programmet ID krävs för att logga in som programmet. Store nyckelvärdet där programmet kan hämta den.

![Alternativ text](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>Hämta klient-ID

Som en del av konfigurationen av slutpunkten, VSTS kräver den **klient-ID** som motsvarar den AAD-katalogen som Azure Stack-stämpel har distribuerats till. Följ stegen i det här avsnittet för att samla in klient-Id.

1.  Välj **Azure Active Directory**.

    ![välj azure active directory](media\azure-stack-solution-machine-learning\image83.png)

1.  Om du vill hämta klient-ID, Välj **egenskaper** för Azure AD-klient.

    ![välj Azure AD-egenskaper](media\azure-stack-solution-machine-learning\image84.png)

1.  Kopiera **katalog-ID:t**. Det här värdet är klient-ID.

    ![klientorganisations-ID](media\azure-stack-solution-machine-learning\image85.png)

Ge tjänstens huvudnamn behörighet att distribuera resurser i Azure Stack-prenumeration

Tilldela programmet till en roll för att komma åt resurser i prenumerationen. Bestäm vilken roll representerar rätt behörigheter för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre nivåer av omfång. Lägga till ett program till rollen Läsare för en resursgrupp kan det läsa resursgruppen och alla resurser som den innehåller.

1.  Navigera till önskad nivå av omfång för att tilldela programmet. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **prenumerationer**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  Välj den **prenumeration** (resursgrupp eller resurs) för att tilldela programmet.

    ![Välj prenumeration för tilldelning](media\azure-stack-solution-machine-learning\image87.png)

1.  Välj **åtkomstkontroll (IAM)**.

    ![Välj åtkomst](media\azure-stack-solution-machine-learning\image88.png)

1.  Välj **Lägg till**.

    ![Välj Lägg till](media\azure-stack-solution-machine-learning\image89.png)

1.  Välj roll att tilldela programmet. Följande bild visar den **ägare** roll.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image90.png)

1.  Som standard visas inte Azure Active Directory-program i de tillgängliga alternativen. Du hittar programmet, **anger du namnet** i sökningen fältet och markerar den.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image91.png)

1.  Välj **spara** Slutför tilldela rollen. Programmet visas i listan över användare som har tilldelats en roll för detta omfång.

### <a name="role-based-access-control"></a>Rollbaserad Access Control

Azure rollbaserad åtkomstkontroll (RBAC) möjliggör detaljerad åtkomsthantering för Azure och Azure Stack. Med RBAC kan kan endast mängden åtkomst som användarna behöver för att utföra sitt arbete beviljas. Mer information om rollbaserad åtkomstkontroll finns i [hantera åtkomst till Azure-prenumerationsresurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

**VSTS-Agentpooler**

Istället för att hantera varje agent individuellt agenter är ordnade i **agentpooler**. En agentpool definierar delningsapplikationen gränsen för alla agenter i poolen. Agentpooler är begränsade till VSTS-konto, så kan delas över projekt i VSTS. Mer information och en självstudiekurs om hur du skapar VSTS agentpooler i [skapa Agentpooler och köer.](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**Lägg till aPersonal åtkomsttoken (PAT) för Azure Stack**

 -  Logga in på VSTS-konto och välj på **kontoprofil** namn.

 -  Välj **Hantera säkerhet** till sidan för åtkomst-token skapas.

![Alternativ text](media\azure-stack-solution-machine-learning\image92.png)

![Alternativ text](media\azure-stack-solution-machine-learning\image93.jpeg)

![Alternativ text](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> Hämta token information. Det kommer inte att visas igen när du lämnar den här skärmen.

1.  Kopiera den **token**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installera VSTS skapa agenten i Azure Stack finns skapa Server

1.  Ansluta till den **skapa Server** distribueras på Azure Stack-värd.

    > [!Note]  
    > Kontrollera att Azure Stack finns skapa servern kan nås från det offentliga Internet. Annars kan du arbeta med en Azure Stack-operatör att få åtkomst.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Uppgradera Ubuntu skapa Server till senaste versionen (18.04):

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > Den här åtgärden kan ta lite tid.

2.  Installera uppfyllde program för build-servern. Från bash baserade shell av Ubuntu skapa Server kör följande kommandon:

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  Hämta och distribuera skapandeagent som en tjänst med hjälp av en **personlig åtkomsttoken (PAT)** och kör som-konto för VM-administratör.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  Gå till extraherade build-agentmappen. Kör följande kod.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Alternativ text](media\azure-stack-solution-machine-learning\image97.png)

2.  Efter **./config.sh**färdiga, kör följande kod för att aktivera tjänsten på servern start och starta tjänsten:

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

Agenten visas nu i VSTS-mapp.

#### <a name="endpoint-creation-permissions"></a>Behörighet att skapa slutpunkten

Användare kan skapa slutpunkter så VSTO versioner kan distribuera Azure Service-appar till stacken. VSTS ansluter till skapandeagent, som sedan ansluter med Azure Stack.

![Alternativ text](media\azure-stack-solution-machine-learning\image98.png)

1.  På den **inställningar** menyn och välj **Security**.

2.  I den **VSTS grupper** listan till vänster, Välj **Endpoint skapare**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image99.png)

3.  På den **fliken medlemmar,** väljer den **+ Lägg till**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image100.png)

1.  Typ **användarnamn** och välja användarnamnet i listan.

2.  Välj **spara ändringar**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image101.png)

3.  I den **VSTS grupper** listan till vänster, Välj **Endpoint administratörer**.

4.  På den **fliken medlemmar,** väljer den **+ Lägg till**.

5.  Ange ett **användarnamn** och väljer användaren i listan.

6.  Välj **spara ändringar.**

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    Build-agenten i Azure Stack får anvisningar från VSTS som meddelar sedan-slutpunktsinformation för kommunikation med Azure Stack.

    VSTS till Azure Stack-anslutningen är nu klar.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>Konfigurera skapa och släpp-definitioner

Nu när anslutningar upprättas du manuellt mappa skapade Azure-slutpunkten, AKS och Azure Container Registry för att bygga och versionsdefinitioner.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>Skapa Build-Definition för det YAML-kod

1.  Välj avsnittet bygger under hubben skapa och släpp och skapa en ny definition.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image104.png)

1.  Välj VSTS Git och den lagringsplats som skapades tidigare.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image105.png)

1.  Välj tomma pipelinen som mall

    ![Alternativ text](media\azure-stack-solution-machine-learning\image106.png)

1.  Namnge bygget **kopia artefakt** och välj den Azure Stack skapa Server för agenten kön.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image107.png)

1.  Välj fas 1 i processerna och Byt namn på den till **kopia artefakt**, sedan **Lägg till en aktivitet** till fasen:

    ![Alternativ text](media\azure-stack-solution-machine-learning\image108.png)

1.  Välj **publicera skapa artefakter** från den **Utility** listan och välj **Lägg till**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image109.png)

1.  Välj den **sökvägen till publicera** och välj den **iris_deployment.yaml** fil.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image110.png)

1.  Namnge artefakten **iris_deployment** och väljer att publiceringsplatsen är **Visual Studio Team Services/TFS**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image111.png)

1.  Välj **spara och köa**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image112.png)

1.  Kontrollera status för build genom att välja build-ID.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image113.png)

Framgång ser ut ungefär så här:

![Alternativ text](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>Skapa Versionsdefinition YAML-kod

1.  Välj avsnittet versioner under Skapa och släpp hubben, en ny definition

    ![Alternativ text](media\azure-stack-solution-machine-learning\image115.png)

1.  Välj tomma pipelinen som en mall.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image106.png)

1.  Namn på miljön Azure Stack.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image116.png)

1.  Lägg till en ny artefakt genom att välja **artefakter** och **+ Lägg till**

2.  Välj Build som källtyp och **HybridMLIris** som projektet.

3.  Välj sedan den byggesdefinition som du skapade tidigare för källan.

4.  Välj sedan **lägga till**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image117.png)

1.  Välj Azure Stack från miljöer och lägger till en ny uppgift i Azure Stack

    ![Alternativ text](media\azure-stack-solution-machine-learning\image118.png)


1.  Ange kön agenten till Azure Stack finns skapa Server på agenten-fasen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image119.png)

1.  Lägga till en ny uppgift i den här fasen, väljer att distribuera till Kubernetes uppgift under distribuera och väljer Lägg till.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image120.png)


1.  Ge den namnet **Kubectl gäller** (standardnamnet) och välj kommandot Använd.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image121.png)

    Nu ska du skapa en ny anslutning för Kubernetes-tjänsten.

#### <a name="create-kubernetes-service-endpoint"></a>Skapa Kubernetes-tjänstslutpunkt

1.  Under Kubernetes Service-anslutning, väljer du den **+ ny** och välj**Kubernetes**i listan. Du kan använda den här slutpunkten för att ansluta den**VSTS**och**Azure Container Service (AKS)**.

2.  **Anslutningsnamn**: Ange anslutningsnamn.

3.  **Serveradress**: Ange container service-adress i formathttp: / / {API-serveradress}

4.  **Kubeconfig**: Om du vill få Kubeconfig, kör du följande kommandon för Azure i en kommandotolk med administratörsbehörigheter så startas.

    > [!Important]  
    > Använd det här CLI-fönstret för att slutföra de nästkommande stegen.

6.  Logga in på Azure i CLI-fönstret. [Läs mer om az-inloggning](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

7.  Skriv följande i kommandotolken:

    ```CLI
        az login
    ```

10. Det här kommandot returnerar en kod som ska användas när webbläsaren <https://aka.ms/devicelogin>.

11. Gå till <https://aka.ms/devicelogin> i webbläsaren. När du uppmanas, anger du den kod som tas emot i CLI i webbläsaren.

    ![Kubernetes Service-slutpunkt](media\azure-stack-solution-machine-learning\image122.png)

1.  Skriv följande kommando i Kommandotolken för att hämta autentiseringsuppgifterna för Kubernetes-klustret.

### <a name="azure-ml-workbench-cli"></a>Azure ML Workbench CLI

AZ aks get-credentials resursgrupp <yourResourceGroup> namn <yourazurecontainerservice>

![Kubernetes Service-slutpunkt](media\azure-stack-solution-machine-learning\image123.png)

1.  Navigera till den **.kube**mapp under arbetskatalogen (t.ex.: C:\\användare\\<user>\\dokument\\Kube)

2.  Kopiera innehållet i den**config**och klistrar in den i fönstret anslutning till Kubernetes. Välj den**OK**knappen.

    ![Kubernetes Service-slutpunkt](media\azure-stack-solution-machine-learning\image124.png)
    

3.  När Kubernetes-slutpunkt skapas och markeras, väljer du Använd configuration filer kryssrutan för att lägga till en konfigurationsfil. Bläddra till filen iris_deployment.yaml i länkade artefakter.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image125.png)

    ![Alternativ text](media\azure-stack-solution-machine-learning\image126.png)

4.  Spara versionsdefinitionen.

#### <a name="check-the-status-of-the-release-definition"></a>Kontrollera status för versionsdefinitionen. 

När du har sparat bör automatiskt versionsdefinitionen i VSTS startar en version.

Kontrollera status för distributionen genom att köra en snabb kommando i Kommandotolken WSL och sedan kontrollera Kubernetes UI.

```Bash  
kubectl get deployments
```

Utdata bör se ut ungefär så här, medan i processen för distribution.

![Alternativ text](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

När kubernetes Användargränssnittet körs, bläddra till distributionen vid [ **https://localhost:8001/** ](https://localhost:8001/) gå sedan till **arbetsbelastningar -> replikuppsättningar**.

![Alternativ text](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>Distribuera YAML-tjänsten

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>Ladda upp iris_service.yaml till databasen och synkronisera ändringar

1.  Gå till nyligen klonad lagringsplats:

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Alternativ text](media\azure-stack-solution-machine-learning\image75.png)

1.  Kopiera den **iris_service.yaml** -filen i databasen.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  Spara ändringen i GIT

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Alternativ text](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>Uppdatera Build-Definition för det YAML-kod

1.  Välj avsnittet bygger under Skapa och släpp-hubben och definitionen skapade tidigare.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image130.png)

2.  Välj knappen Redigera om du vill redigera definitionen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image131.png)

3.  **Lägg till en aktivitet** till fasen. Välj **publicera skapa artefakter** från den **Utility** listan och välj **Lägg till**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image108.png)

4.  Ge den namnet **Kubectl gäller** (standardnamnet) och välj kommandot Använd.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>Uppdatera Versionsdefinitionen YAML-kod

1.  Välj theReleases avsnittet under Skapa och släpp-hubben och versionsdefinitionen skapade tidigare. Välj sedan länken Redigera.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image132.png)

1.  Välj miljön **Azure Stack** sedan lägga till en ny uppgift i Azure Stack.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image133.png)

1.  Lägg till en **ny uppgift** till det här steget, väljer den **distribuera till Kubernetes** uppgift **distribuera** och välj **Lägg till**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image134.png)

1.  Ge den namnet **Kubectl gäller** (standardnamnet) och välj kommandot Använd.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image109.png)

1.  Ange Kubernates Service-anslutning till Azure Stack-anslutning som skapades tidigare och välj sedan den **använda konfigurationsfiler** kryssrutan om du vill lägga till en konfigurationsfil. Bläddra till filen iris_service.yaml i länkade artefakter.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image135.png)


    ![Alternativ text](media\azure-stack-solution-machine-learning\image136.png)

1.  Spara versionsdefinitionen.

#### <a name="check-the-status-of-the-release-definition"></a>Kontrollera status för versionsdefinitionen

När du har sparat bör automatiskt versionsdefinitionen i VSTS startar en version.

Kontrollera status för distributionen genom att köra en snabb kommando i Kommandotolken WSL och sedan kontrollera Kubernetes UI.

```Bash  
kubectl get deployments
```

Utdata bör se ut ungefär så här, medan i processen för distribution.

![Alternativ text](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

När kubernetes Användargränssnittet körs, bläddra till distributionen vid [ **https://localhost:8001/** ](https://localhost:8001/) gå sedan till **arbetsbelastningar -> replikuppsättningar**.

![Alternativ text](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Kubernetes bedömning och validering

Starta Kubernetes-Användargränssnittet

```Bash  
kubectl proxy
```

Bläddra till Kubernetes-UI, och gå sedan till **distributioner** -> **Iris-distribution** -> **nya replikuppsättning**  ->  **Iris-distribution-xxxxxxxxx** (där xs är distributions-ID).

![Alternativ text](media\azure-stack-solution-machine-learning\image138.png)

Gå sedan till **Services** och välj den **extern slutpunkt** av tjänsten för att verifiera det fungerar.

![Alternativ text](media\azure-stack-solution-machine-learning\image139.png)

Ett verifieringsmeddelande som liknar den nedan visas:

![Alternativ text](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Skapa Azure Stack bedömning Funktionsapp i Azure Stack-portalen

En funktionsapp måste värd för körning av varje funktion. En funktionsapp kan funktionen gruppering som en logisk enhet för enklare hantering, distribution och dela resurser.

1.  Användarportalen för Azure Stack, väljer du den **+ ny** knapp hittades i det övre vänstra hörnet, väljer sedan**webb + mobilt** >**Funktionsapp**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image141.png)

1.  Namnge funktionen **datafunktioner** och placera den i samma resursgrupp med återstående Maskininlärning innehåll. Låt verktyget automatiskt – skapa en ny app service-plan för användning och använder storage-konto som tidigare har skapat för applagring.

    ![Definiera nya funktionsappinställningar](media\azure-stack-solution-machine-learning\image142.png)

1.  Välj**skapa**att etablera och distribuera funktionsappen.

2.  Välj meddelandeikonen i det övre högra hörnet i portalen och titta efter den**distributionen lyckades** meddelande.

    ![Definiera nya funktionsappinställningar](media\azure-stack-solution-machine-learning\image143.png)

1.  Välj**gå till resurs** att visa den nya funktionsappen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image144.png)

1.  Skapa en ny funktion genom att välja **Functions**, kommer **+ ny funktion** knappen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image145.png)

1.  Välj HTTP-utlösare

    ![Alternativ text](media\azure-stack-solution-machine-learning\image146.png)

1.  Välj **C\#**  som språk och namnet funktionen: **clean-poäng-data**, och ange åtkomstnivån till **anonym**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image147.png)

1.  Kopiera och klistra in innehållet i exemplet Platskod för clean-poäng-data i funktionen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Validera funktioner med hjälp av Postman

För att säkerställa att du har konfigurerat dina Kbernetes och funktioner kan på rätt sätt du använda den kostnadsfria appen Postman att testa och validera scheman och funktioner. Om du vill starta den här processen, måste du först samla in viss information från din Kubernetes-instans.

1.  Bläddra till Kubernetes-UI, och gå sedan till **distributioner** -> **Iris-distribution** -> **nya replikuppsättning**  ->  **Iris-distribution-xxxxxxxxx** (där xs är distributions-ID)

    ![Alternativ text](media\azure-stack-solution-machine-learning\image138.png)

1.  Gå sedan till **Services** och kopiera den **extern slutpunkt**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image149.png)

1.  Ladda ned och installera appen Postman [här](https://www.getpostman.com/apps) om det behövs.

2.  Logga in på den Postman-appen och Stäng dialogrutan Ny fil.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image150.png)

1.  Från postman-appen Välj POST...

    ![Alternativ text](media\azure-stack-solution-machine-learning\image151.png)

1.  Klistra in den **extern slutpunkt** URL: en i postman-appen under den **fråge-URL** att lägga till  **\\poäng** i slutet av URL: en som visas nedan.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image152.png)

1.  Välj den **brödtext** fliken och data skriver som **raw**, sedan **JSON**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image153.png)

1.  Från en webbläsare, navigerar du till **extern slutpunkt**. Att lägga till följande URL: en **/swagger.json** detta leder till tjänster Swagger-filen som används för att testa installationen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image154.png)

1.  Kopiera de exempel som visas i den **Swagger.JSON** fil.

2.  Klistra in exemplet i brödtexten i inlägget i Postman-appen och välj **skicka**. Den bör returnera ett värde liknar den som visas nedan.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>Steg 7: Skapa ett Azure Stack-lagringskonto och Storage-kö

Skapa ett Azure Stack-lagringskonto och Storage-kö för data.

1.  Logga in på användarportalen för Azure Stack. (Varje Azure Stack har en unik URL)

2.  I Azure Stack användarportalen, expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **alla tjänster**. Rulla ned till **Storage** och välj **lagringskonton**. I den **Lagringskonton** fönstret Välj **Lägg till**.

3.  Ange ett namn för lagringskontot.

4.  Välj replikeringsalternativ för lagringskontot: **LRS**.

5.  Ange en ny resursgrupp eller välj en befintlig resursgrupp.

6.  Välj **lokala** för platsen för lagringskontot.

7.  Välj**skapa**att skapa lagringskontot.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image156.png)

1.  Välj nyligen skapade lagringskontot.

2.  Välj på**köer**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image157.png)

1.  Välj på **+ kö** och ge namnet på kön och välj **OK.**

    ![Alternativ text](media\azure-stack-solution-machine-learning\image158.png)

1.  Hämta den **Connection String** för Storage-kö och kopierar den.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image159.png)

1.  Gå till Azure-Funktionsapp och välj **programinställningar**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image160.png)

1.  Programinställningar i Funktionsappen, rulla programinställningar och välj **+ Lägg till ny inställning**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image161.png)

1.  Ange namnet på lagringskontot i den **namn** fält, lägga till i slutet; _qos

På så sätt kan appen ska veta att det här är en slutpunkt för lagring-konto.

1.  Klistra sedan in anslutningssträngen i den **värdet** fält.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image162.png)

1.  Bläddra till början av programinställningar och välj **spara**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>Uppdatera funktionen bedömning för att använda Storage-kö

1.  Välj på **integrera** på funktionen, och avmarkera den **hämta** alternativet.

2.  Välj **Spara**.

3.  Välj sedan **+ nya utdata** från utdata.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image164.png)

1.  Välj sedan **Azure Queue Storage** och välj **Välj**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image165.png)

1.  Uppdatera den **könamn** till lagringskön skapade tidigare och ange sedan den **Lagringskontoanslutning** till den lagringskontoanslutning som skapats tidigare och välj **spara.**

    ![Alternativ text](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>Steg 8: Skapa en funktion för att hantera rensa data

Skapa en ny Azure Stack-funktion för att flytta rensa data från Azure Stack till Azure.

1.  Skapa en ny funktion genom att välja **Functions**, kommer **+ ny funktion** knappen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image167.png)

1.  Välj **Timerutlösare**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image168.png)

1.  Välj **C\#**  som språk och namnet funktionen: **överföring till azure** och Ställ in schemat till **0 0 \*/1 \* \* \***  som i CRON notation är en gång i timmen.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Hämta anslutningssträngen till det värdbaserade Azure-Lagringskontot

1.  Bläddra till <https://portal.azure.com> och välj sedan den **Azure Storage-konto** skapade tidigare.

2.  Välj **åtkomstnycklar**, kopierar den **anslutningssträngen** för Lagringskontot.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>Uppdatera den överföring till azure att använda Azure som värd-lagring

1.  Gå till Azure-Funktionsapp och välj **programinställningar**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image171.png)

1.  Programinställningar i Funktionsappen, rulla programinställningar och välj **+ Lägg till ny inställning**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image172.png)

1.  Ange namnet på lagringskontot i den **namn** fält, lägga till i slutet; _qos

På så sätt kan appen ska veta att det här är en slutpunkt för lagring-konto.

1.  Klistra sedan in på Azure som värd anslutningssträng för Lagringskonto i den **värdet** fält.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image173.png)

1.  Bläddra till början av programinställningar och välj **spara**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image174.png)

1.  Gå tillbaka till den **överföring till azure** funktion.

2.  Välj på **integrera** på funktionen.

3.  Välj sedan **+ nya utdata** från utdata.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image175.png)

1.  Välj sedan **Azure Blob Storage** och välj **Välj**.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image176.png)

1.  Uppdatering i **sökväg** lagringsbehållare som skapades tidigare i följande format: **uploadeddata / {rand-guid} .txt**, och sedan ange den **Lagringskontoanslutning** till den Anslutning till lagringskonto till Azure skapade tidigare och välj **spara.**

    ![Alternativ text](media\azure-stack-solution-machine-learning\image177.png)

1.  Kopiera och klistra in innehållet i exemplet Platskod för **överföring till azure** i funktionen.

2.  Ändra vid behov så att den pekar på den anslutningssträng för Storage-konton.

3.  Spara och kör koden.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image178.png)

1.  Kontrollera Azure som värd lagringskontot för att se data har tolkats upp till molnet från Azure: framgång ser ut ungefär som den nedan.

    ![Alternativ text](media\azure-stack-solution-machine-learning\image179.png)

Data har språkoberoende av känsliga data med Azure Stack finns Kubernetes Machine Learning och överförs till Azures offentliga moln från en lokal Azure Stack via Azure Stack finns Funktionsappar, och kan mellanlagra data för överföringar i en edge/frånkopplad scenario.

## <a name="next-steps"></a>Nästa steg

 - Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).