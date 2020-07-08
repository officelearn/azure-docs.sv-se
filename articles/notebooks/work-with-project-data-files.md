---
title: Importera och exportera data med projekt med Azure Notebooks för hands version
description: Lär dig hur du hämtar data till ett Azure Notebooks för hands versions projekt från externa källor och hur du exporterar data från ett projekt.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: b3669128582d3bdd6a3c4506a040856ab7b07e9a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834122"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Arbeta med datafiler i Azure Notebooks förhandsgranska projekt

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Data är Lifeblood av många Jupyter notebook-datorer, särskilt antecknings böcker som används för data vetenskap. Med Azure Notebooks kan du enkelt importera från en rad olika källor till ett projekt och sedan använda dessa data från antecknings böcker. Du kan också använda antecknings böcker för att generera data som lagras i projektet, som du sedan kan hämta för användning någon annan stans.

**Data** -menyn i en antecknings bok som är igång innehåller också kommandon för att **Ladda upp** och **Ladda ned** , vilket fungerar tillsammans med filer i projektet samt temporära filer för den aktuella Notebook-sessionen.

Du kan också använda kod i en bärbar dator för att få åtkomst till en mängd olika data källor direkt, inklusive filer i ett projekt. Du kan också komma åt godtyckliga data med hjälp av kommandon i en kod cell. Eftersom dessa data lagras i variabler inom Notebook-sessionen, sparas de inte i projektet om du inte använder kod för att skapa projektfiler.

Att arbeta med kod i data är bäst i en antecknings bok som är igång: för detta ändamål går du till [komma åt dina data i Azure Notebooks exempel antecknings boken](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Resten av den här artikeln innehåller information om fil åtgärder på projekt nivå.

## <a name="import-data"></a>Importera data

Du kan ta med filer i ett projekt från instrument panelen i projektet eller i en aktiv antecknings bok med antingen **data** -menyn eller ett kommando som `curl` .

### <a name="import-files-from-the-project-dashboard"></a>Importera filer från instrument panelen för projektet

1. I projektet navigerar du till mappen där du vill importera filerna.

1. Välj kommandot **upload** , sedan antingen **från URL** eller **från dator** och projicera nödvändig information för de data som du vill importera:

   - **Från URL**: Ange käll adressen i fältet **fil-URL** och fil namnet som ska tilldelas antecknings boken i ditt projekt i fältet **fil namn** . Välj sedan **+ Lägg till fil** för att lägga till URL: en i överförings listan. Upprepa processen för eventuella ytterligare URL: er och välj sedan **OK**.

     ![Popup-menyn Ladda upp från URL](media/quickstarts/upload-from-url-popup.png)

   - **Från dator**: Dra och släpp filer till popup-fönstret, eller Välj **Välj filer**, bläddra till och välj de datafiler som du vill importera. Du kan släppa eller välja valfritt antal filer, oavsett typ och format, eftersom det är upp till koden i antecknings boken för att öppna filen och parsa dess data.

     ![Popup-fönstret Ladda upp från dator](media/quickstarts/upload-from-computer-popup.png)

1. När filerna har importer ATS visas de på instrument panelen för projektet och du kan komma åt dem i Notebook-koden med relativa sökvägar till mappen som innehåller.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importera filer från menyn Arkiv i en bärbar dator

1. I en aktiv antecknings bok väljer du kommandot **fil**  >  **uppladdning** :

    ![Meny kommando för fil uppladdning i en bärbar dator](media/file-menu-upload.png)

1. I dialog rutan som öppnas navigerar du till och väljer de filer som du vill ladda upp. Du kan välja valfritt antal filer av valfri typ. Välj **Öppna** när du är färdig.

1. I popup-fönstret **överförings status** som visas väljer du en **målmapp** i list rutan:

    - Session-mapp ( *~/* ): överför filer till den aktuella Notebook-sessionen, men skapar inte filer i projektet. Mappen session är en peer-till projektmappen, men den behålls inte när sessionen har slutförts. Om du vill få åtkomst till sessionsobjekt i kod, prefixerar du fil namnen med den relativa sökvägen *.. /*.

        Att använda sessionen är användbart för experimentering och du slipper göra projektet rörigt med filer som du kanske inte behöver på en långsiktig basis. Du kan också ladda upp filer till sessionen med identiska namn på filer i projektet utan att orsaka konflikter och utan att byta namn på filerna. Anta till exempel att du har en version av *data.csv* i projektet redan, men vill experimentera med en annan version av *data.csv*. Genom att ladda upp filen till sessionen kan du köra antecknings boken med data i den överförda filen (som refereras till i kod med hjälp av *. /data.csv*) i stället för data i projekt filen.

    - Projektmapp (*/Project*): överför filer till projektet där de kan nås med relativa Sök vägar i kod. Att ladda upp en fil till den här mappen är detsamma som att ladda upp en fil på instrument panelen för projektet. Filen sparas i projektet och är tillgänglig i senare sessioner.

        Överföringen Miss lyckas om du försöker ladda upp en fil med samma namn som en som redan finns i projektet. Om du vill skriva över en fil laddar du upp den nya filen från instrument panelen i stället, vilket ger dig möjlighet att skriva över.

1. Slutför processen genom att välja **Starta uppladdning** .

### <a name="create-or-import-files-using-commands"></a>Skapa eller importera filer med hjälp av kommandon

Du kan använda kommandon i en terminal eller i en python-cell för att skapa filer i både projekt-och session-mapparna. Till exempel kommandon som `curl` och `wget` Hämta filer direkt från Internet.

Om du vill ladda ned filer i terminalen väljer du kommandot **Terminal** på instrument panelen och anger lämpliga kommandon:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

När du använder en python-kodsida i en antecknings bok ska du använda kommandot som prefix `!` .

Projektmappen är standardmappen, så du kan ange ett mål namn som *oil_price.csv* skapar filen i projektet. Om du vill skapa en sessionsfil använder du namnet med *.. /* som i *.. /oil_price.csv*.

### <a name="create-files-in-code"></a>Skapa filer i kod

När du använder kod som skapar en fil, t. ex. Pandas `write_csv` -funktionen, är Sök vägar alltid relativa i mappen Project. Använder *.. /* skapar en sessionsfil som ignoreras när antecknings boken stoppas och stängs.

## <a name="export-files"></a>Exportera filer

Du kan exportera data från instrument panelen i Project eller från en bärbar dator.

## <a name="export-files-from-the-project-dashboard"></a>Exportera filer från instrument panelen för projektet

På instrument panelen för projektet högerklickar du på en fil och väljer **Hämta**:

![Hämta kommando på snabb menyn för projekt objekt](media/download-command.png)

Du kan också välja en fil och använda kommandot **Hämta** (kortkommando: d) på instrument panelen:

![Hämta Toolbar-kommandot på instrument panelen i Project](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportera filer från Data-menyn i en bärbar dator

1. Välj Meny kommandot **fil**  >  **hämtning** :

    ![Meny kommandot Hämta data i en bärbar dator](media/file-menu-download.png)

1. En popup visas som visar mapparna i sessionen. *projektmappen* innehåller projektfilerna:

    ![Popup-kommando för hämtning av data i vilken du väljer filer och mappar](media/file-menu-download-popup.png)

1. Markera rutorna till vänster om de filer och mappar som du vill ladda ned och välj sedan **Hämta markerade**.

1. Antecknings boken förbereder en enda *zip* -fil som innehåller de valda filerna, som du sedan sparar som vanligt i webbläsaren. Antecknings boken skapar en *zip* -fil även när du laddar ned en enskild fil.

## <a name="next-steps"></a>Nästa steg

- [Komma åt molndata i en notebook-fil](access-data-resources-jupyter-notebooks.md)
