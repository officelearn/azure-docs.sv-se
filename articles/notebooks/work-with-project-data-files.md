---
title: Importera och exportera data med projekt med förhandsversionen av Azure Notebooks
description: Lär dig hur du tar med data till ett Azure Notebooks Preview-projekt från externa källor och hur du exporterar data från ett projekt.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646984"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Arbeta med datafiler i Azure Notebooks Preview-projekt

Data är livsnerven i många Jupyter bärbara datorer, särskilt bärbara datorer som används för datavetenskap. Med Azure Notebooks kan du enkelt importera från en mängd olika källor till ett projekt och sedan använda dessa data från anteckningsböcker. Du kan också låta anteckningsböcker generera data som lagras i projektet, som du sedan kan hämta för användning någon annanstans.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

**Data-menyn** i en anteckningsbok som körs innehåller också **uppladdnings-** och nedladdningskommandon, som fungerar med filer i projektet samt temporära filer för den aktuella anteckningsbokssessionen. **Download**

Du kan också använda kod i en anteckningsbok för att komma åt en mängd olika datakällor direkt, inklusive filer i ett projekt. Du kan också komma åt godtyckliga data med hjälp av kommandon i en kodcell. Eftersom sådana data lagras i variabler i anteckningsbokssessionen sparas de inte i projektet om du inte använder kod för att specifikt generera projektfiler.

Det är bäst att arbeta med kod i data i en anteckningsbok som körs: i det syfte hänvisas till [exempel anteckningsboken Komma till dina data i Azure Notebooks](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Resten av den här artikeln innehåller information om filåtgärder på projektnivå.

## <a name="import-data"></a>Importera data

Du kan ta med filer till ett projekt från projektinstrumentpanelen eller i `curl`en anteckningsbok som körs med **datamenyn** eller ett kommando, till exempel .

### <a name="import-files-from-the-project-dashboard"></a>Importera filer från projektinstrumentpanelen

1. I projektet navigerar du till mappen där du vill importera filerna.

1. Välj kommandot **Ladda upp** och sedan antingen **från URL** eller Från **dator** och projicera nödvändig information för de data som du vill importera:

   - **Från URL:** Ange källadressen i fältet **Fil-URL** och det filnamn som ska tilldelas anteckningsboken i projektet i fältet **Filnamn.** Välj sedan **+ Lägg till fil** om du vill lägga till URL:en i uppladdningslistan. Upprepa processen för ytterligare webbadresser och välj sedan **Klar**.

     ![Ladda upp från URL-popup](media/quickstarts/upload-from-url-popup.png)

   - **Från dator**: Dra och släpp filer till popup-fönstret, eller välj **Välj filer**och bläddra sedan till och markera de datafiler som du vill importera. Du kan släppa eller välja valfritt antal filer, av valfri typ och format eftersom det är upp till koden i anteckningsboken att öppna filen och tolka dess data.

     ![Ladda upp från datorns popup](media/quickstarts/upload-from-computer-popup.png)

1. När filerna har importerats visas de på instrumentpanelen för projektet och kan nås i anteckningsbokskod med hjälp av relativa sökvägar till mappen innehållande.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importera filer från Arkiv-menyn i en anteckningsbok

1. Välj kommandot > **Filuppladdning** i en anteckningsbok som körs: **File**

    ![Kommandot Ladda upp filer i en anteckningsbok](media/file-menu-upload.png)

1. Navigera till och markera de filer som du vill ladda upp i dialogrutan som öppnas. Du kan välja valfritt antal filer av alla typer. Välj **Öppna** när du är klar.

1. I **popup-fönstret Uppladdningsstatus** som visas väljer du en **målmapp** i listrutan:

    - Sessionsmappen*~/* ( ): Laddar upp filer till den aktuella anteckningsbokssessionen men skapar inte filer i projektet. Sessionsmappen är peer to the project folder, men finns inte kvar när sessionen är avslutad. Om du vill komma åt sessionsfiler i kod prefixar du filnamnen med den relativa sökvägen *.. /*.

        Det är praktiskt att använda sessionsmappen för experiment och undviker att störa projektet med filer som du kanske eller kanske inte behöver på lång sikt. Du kan också ladda upp filer till sessionsmappen som har identiska namn till filer i projektet utan att orsaka konflikter och utan att behöva byta namn på filerna. Anta till exempel att du redan har en version av *data.csv* i projektet, men vill experimentera med en annan version av *data.csv*. Genom att överföra filen till sessionsmappen kan du köra anteckningsboken med hjälp av data i den uppladdade filen (med hänvisning till den i kod med *.. /data.csv*) i stället för data i projektets fil.

    - Projektmapp (*/project):* överför filer till projektet där de kan nås med hjälp av relativa sökvägar i kod. Att ladda upp en fil till den här mappen är samma sak som att ladda upp en fil på projektinstrumentpanelen. Filen sparas med projektet och är tillgänglig i senare sessioner.

        Överföringen misslyckas om du försöker ladda upp en fil med samma namn som en fil som redan finns i projektet. Om du vill skriva över en fil laddar du upp den nya filen från projektinstrumentpanelen i stället, vilket ger dig möjlighet att skriva över.

1. Välj **Starta uppladdning** för att slutföra processen.

### <a name="create-or-import-files-using-commands"></a>Skapa eller importera filer med kommandon

Du kan använda kommandon i en terminal eller i en Python-kodcell för att skapa filer i både projekt- och sessionsmapparna. Kommandon gillar `curl` och `wget` hämtar filer direkt från Internet.

Om du vill hämta filer i terminalen väljer du kommandot **Terminal** på projektinstrumentpanelen och anger sedan lämpliga kommandon:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

När du använder en Python-kodcell i en `!`anteckningsbok prefix kommandona med .

Projektmappen är standardmappen, så om du anger ett målfilnamn som *oil_price.csv* skapas filen i projektet. Om du vill skapa en sessionsfil förebestämde du namnet med *.. /* som i *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Skapa filer i kod

När du använder kod som skapar en `write_csv` fil, till exempel pandafunktionen, är sökvägar alltid relativa till projektmappen. Använda *.. /* skapar en sessionsfil som ignoreras när anteckningsboken stoppas och stängs.

## <a name="export-files"></a>Exportera filer

Du kan exportera data från projektinstrumentpanelen eller från en anteckningsbok.

## <a name="export-files-from-the-project-dashboard"></a>Exportera filer från instrumentpanelen för projektet

Högerklicka på en fil på projektinstrumentpanelen och välj **Hämta:**

![Hämta kommando på snabbmenyn för projektobjekt](media/download-command.png)

Du kan också välja en fil och använda kommandot **Hämta** (kortkommando: d) på instrumentpanelen:

![Kommandot Hämta verktygsfält på instrumentpanelen för projekt](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportera filer från Data-menyn i en anteckningsbok

1. Välj **menykommandot** > **Arkivhämtning:**

    ![Menykommando för datahämtning i en anteckningsbok](media/file-menu-download.png)

1. En popup-bild visas som visar mapparna i sessionen. *projektmappen* innehåller projektfilerna:

    ![Data Download kommando popup där du väljer filer och mappar](media/file-menu-download-popup.png)

1. Markera rutorna till vänster om de filer och mappar som du vill hämta och välj sedan **Hämta markerat**.

1. Anteckningsboken förbereder en enda *ZIP-fil* som innehåller de valda filerna, som du sedan sparar som vanligt från webbläsaren. Anteckningsboken skapar en *ZIP-fil* även när du hämtar en enda fil.

## <a name="next-steps"></a>Nästa steg

- [Komma åt molndata i en notebook-fil](access-data-resources-jupyter-notebooks.md)
