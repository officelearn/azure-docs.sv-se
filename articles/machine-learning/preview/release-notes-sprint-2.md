---
title: "Azure ML-arbetsstationen viktig information för sprint 2 December 2017"
description: "Det här dokumentet beskriver uppdateringar för sprint 2-versionen av Azure ML"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: f3579942624de282b01d74c4b8c449c56a66e7b7
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="sprint-2---december-2017"></a>Skriv ut 2 - December 2017 

#### <a name="version-number-01171115263"></a>Versionsnummer: 0.1.1711.15263

>Här är hur du kan [hittar du versionsnumret](https://docs.microsoft.com/en-us/azure/machine-learning/preview/known-issues-and-troubleshooting-guide).

Välkommen till den tredje uppdateringen Azure Machine Learning-arbetsstationen. Den här uppdateringen innehåller förbättringar i appen arbetsstationen, kommandoradsgränssnittet (CLI) och backend-tjänster. Tack för att skicka leenden och frowns. Många av följande uppdateringar görs direkt resultatet av din feedback. 

## <a name="notable-new-features"></a>Viktiga nya funktioner
- [Stöd för SQL Server och SQL Azure DB som en datakälla](https://docs.microsoft.com/en-us/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 
- [Djupgående inlärning på Spark med GPU stöd med MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Alla AML behållare är kompatibla med Azure IoT-gränsenheterna när distribuerade (inga ytterligare åtgärder krävs)](http://aka.ms/aml-iot-edge-blog)
- Registrerad modell lista och detaljer vyer tillgängliga Azure-portalen
- Åtkomst till beräkning mål med hjälp av SSH-nyckel för autentisering förutom användarnamn/lösenord-baserad åtkomst. 
- Nya mönstret frekvens Inspector i data Förbered dig upplevelse. 

## <a name="detailed-updates"></a>Detaljerad uppdateringar
Nedan följer en lista över detaljerade uppdateringar under varje komponent i Azure Machine Learning i den här sprint.

### <a name="installer"></a>Installer
- Installationsprogrammet kan self uppdatering så som buggar korrigeringar och nya funktioner som kan användas utan att användaren behöver installera om det.

### <a name="workbench-authentication"></a>Autentisering av arbetsstation
- Flera korrigeringar av system för autentisering. Berätta om du fortfarande har problem med inloggningen.
- UI-ändringar som gör det lättare att hitta inställningarna för proxyservern Manager.

### <a name="workbench"></a>Workbench
- Skrivskyddad vy har nu lätta blå bakgrund
- Flyttade redigera-knappen till höger för att göra det lättare att hitta.
- ”dsource”, ”dprep” och ”ipynb” filformat kan nu återges i raw-format
- Arbetsstationen har nu en ny redigera miljö som hjälper användaren mot med externa IDE Redigera skript och använda arbetsstationen endast för att redigera filtyper som har en innehållsrik redigering (till exempel bärbara datorer, datakällor, paket för förberedelse av Data)
- Inläsning av listan med arbetsytorna och projekt som användaren har åtkomst till är betydligt snabbare nu

### <a name="data-preparation"></a>Förberedelse av data 
- Ett mönster frekvens Inspector att visa mönster i en kolumn. Du kan också filtrera dina data med hjälp av dessa mönster. Detta visar vyn liknar värdet räknar-inspector. Skillnaden är att mönster frekvens visar antalet unika mönster av data i stället för antalet unika data. Du kan också filtrera in eller ut alla rader som uppfyller ett visst mönster.

![Bild av mönstret frekvens inspector på produkt-nummer](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Prestandaförbättringar vid rekommenderar edge fall att granska i omvandling-härledd kolumn efter exempel'

- [Stöd för SQL Server och SQL Azure DB som en datakälla](https://docs.microsoft.com/en-us/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 

![Bild av hur du skapar en ny SQL server-datakälla](media/release-notes-sprint-2/sql-server-data-source.png)

- Aktiverad ”i korthet” vy över rad- och antal

![Bild av radantal för kolumnen vid en grace](media/release-notes-sprint-2/row-col-count.png)

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
[!NOTE] -k (eller--Använd azureml-ssh-nyckel) anger i kommandot för att generera och använder SSH-nyckel.

- Azure ML-arbetsstationen ska generera en offentlig nyckel och utdata som i konsolen. Logga in på målet beräkning med samma användarnamn och Lägg till ~/.ssh/authorized_keys filen med den här offentliga nyckeln.

- Du kan förbereda det här målet för beräkning och använda den för körning och Azure ML-arbetsstationen använder denna nyckel för autentisering.  

Mer information om hur du skapar beräkning mål finns [konfigurerar Azure Machine Learning experiment Service](https://docs.microsoft.com/en-us/azure/machine-learning/preview/experimentation-service-configuration)

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

![modellinformation i portalen](media/release-notes-sprint-2/model-list.jpg)

![Översikt över säkerhetsmodell i portalen](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Djup Learning på Spark med [GPU-support](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Stöd för Resource Manager-mallar för distribution av enkelt resurs
- Stöd för SparklyR ekosystem
- [AZTK integrering](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Exempelprojekt
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) och [MMLSpark](https://github.com/Azure/mmlspark) exempel uppdateras med den nya versionen av Azure ML-SDK

## <a name="breaking-changes"></a>GÖR ÄNDRINGAR
- Upphöja den `--type` växla i `az ml computetarget attach` till en underordnad. 

    - `az ml computetarget attach --type remotedocker`är nu`az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster`är nu`az ml computetarget attach cluster`
