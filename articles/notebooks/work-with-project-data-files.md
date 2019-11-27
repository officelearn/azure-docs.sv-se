---
title: Importera och exportera data med projekt med Azure-anteckningsböcker
description: Hur att överföra data till ett projekt med Azure-datorer från externa källor, och hur du exporterar data från ett projekt.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: bd7ba27859e9d05c0d57c2f78b6449c2bc48ca33
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277390"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Arbeta med datafiler i Azure Notebook-projekt

Data är livsnerven för många Jupyter notebooks, särskilt anteckningsböcker som används för datavetenskap. Med Azure Notebooks, kan du enkelt importera från olika källor till ett projekt och sedan använda dessa data från bärbara datorer. Du kan också ha anteckningsböcker som genererar data som lagras i projektet som du sedan kan hämta för användning på annan plats.

**Data** -menyn i en antecknings bok som är igång innehåller också kommandon för att **Ladda upp** och **Ladda ned** , vilket fungerar tillsammans med filer i projektet samt temporära filer för den aktuella Notebook-sessionen.

Du kan också använda kod i en anteckningsbok får direktåtkomst till en mängd olika datakällor, inklusive filer i ett projekt. Du kan också komma åt valfria data med hjälp av kommandon i en kodcell. Eftersom sådana data lagras i variablerna i notebook-session, sparas inte den i projektet om du inte använder kod att specifikt generering av projektfiler.

Att arbeta med kod i data är bäst i en antecknings bok som är igång: för detta ändamål går du till [komma åt dina data i Azure Notebooks exempel antecknings boken](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Resten av den här artikeln innehåller information om projektet på servernivå filåtgärder.

## <a name="import-data"></a>Importera data

Du kan hämta filer till ett projekt från instrument panelen i projektet eller i en aktiv antecknings bok med antingen **data** -menyn eller ett kommando som `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importera filer från instrumentpanelen för projektet

1. Navigera till mappen där du vill importera filerna i projektet.

1. Välj kommandot **upload** , sedan antingen **från URL** eller **från dator** och projicera nödvändig information för de data som du vill importera:

   - **Från URL**: Ange käll adressen i fältet **fil-URL** och fil namnet som ska tilldelas antecknings boken i ditt projekt i fältet **fil namn** . Välj sedan **+ Lägg till fil** för att lägga till URL: en i överförings listan. Upprepa processen för eventuella ytterligare URL: er och välj sedan **OK**.

     ![Ladda upp från URL: en popup-fönstret](media/quickstarts/upload-from-url-popup.png)

   - **Från dator**: Dra och släpp filer till popup-fönstret, eller Välj **Välj filer**, bläddra till och välj de datafiler som du vill importera. Du kan släppa eller välja valfritt antal filer av valfri typ och format, eftersom det är upp till koden i anteckningsboken för att öppna filen och parsa data.

     ![Överför från datorn popup-fönstret](media/quickstarts/upload-from-computer-popup.png)

1. När du importerat filer visas på instrumentpanelen för projektet och kan nås i anteckningsboken kod med relativa sökvägar till mappen som innehåller.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importera filer från menyn Arkiv i en bärbar dator

1. I en aktiv antecknings bok väljer du kommandot **fil** > **uppladdning** :

    ![Filen uppladdning menykommandot inom en anteckningsbok](media/file-menu-upload.png)

1. I dialogrutan som öppnas, navigera till och välj de filer som du vill ladda upp. Du kan välja valfritt antal filer av valfri typ. Välj **Öppna** när du är färdig.

1. I popup-fönstret **överförings status** som visas väljer du en **målmapp** i list rutan:

    - Session-mapp ( *~/* ): överför filer till den aktuella Notebook-sessionen, men skapar inte filer i projektet. Mappen session är en peer till projektmappen, men bevaras inte när sessionen har upphört. Om du vill få åtkomst till sessionsobjekt i kod, prefixerar du fil namnen med den relativa sökvägen *.. /* .

        Med hjälp av mappen session är användbar för experimentering och undviker att fylla i projektet med filer som du kanske eller kanske inte behöver på lång sikt. Du kan också ladda upp filer till mappen session som har identiska namn till filer i projektet utan att orsaka konflikter och utan att behöva byta namn på filerna. Anta till exempel att du har en version av *data. csv* i projektet redan, men vill experimentera med en annan version av *data. csv*. Genom att ladda upp filen till sessionen kan du köra antecknings boken med data i den överförda filen (som refereras till i kod med hjälp av *. /data.csv*) i stället för data i projekt filen.

    - Projektmapp ( */Project*): överför filer till projektet där de kan nås med relativa Sök vägar i kod. Ladda upp en fil i den här mappen är samma som att ladda upp en fil på instrumentpanelen för projektet. Filen sparas med projektet och är tillgängliga i senare sessioner.

        Överföringen misslyckas om du försöker överföra en fil med samma namn som en som redan finns i projektet. Om du vill skriva över en fil, ladda upp den nya filen från instrumentpanelen för projektet i stället, vilket ger dig möjlighet att skriva över.

1. Slutför processen genom att välja **Starta uppladdning** .

### <a name="create-or-import-files-using-commands"></a>Skapa eller importera filer med hjälp av kommandon

Du kan använda kommandon i en terminal eller i en Python-kodcell för att skapa filer i mappar för både projekt och sessionen. Till exempel kommandon som `curl` och `wget` hämta filer direkt från Internet.

Om du vill ladda ned filer i terminalen väljer du kommandot **Terminal** på instrument panelen och anger lämpliga kommandon:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

När du använder en python-kodsida i en bärbar dator, ska du använda kommandot med `!`.

Projektmappen är standardmappen, så du kan ange ett mål namn som *oil_price. csv* skapar filen i projektet. Om du vill skapa en sessionsfil använder du namnet med *.. /* som i *.. /oil_price. csv*.

### <a name="create-files-in-code"></a>Skapa filer i kod

När du använder kod som skapar en fil, t. ex. Pandas `write_csv`-funktionen, är Sök vägar alltid relativa i mappen Project. Använder *.. /* skapar en sessionsfil som ignoreras när antecknings boken stoppas och stängs.

## <a name="export-files"></a>Exportera filer

Du kan exportera data från instrumentpanelen för projektet eller inifrån en anteckningsbok.

## <a name="export-files-from-the-project-dashboard"></a>Exportera filer från instrumentpanelen för projektet

På instrument panelen för projektet högerklickar du på en fil och väljer **Hämta**:

![Ladda ned kommandot på snabbmenyn för projektet objekt](media/download-command.png)

Du kan också välja en fil och använda kommandot **Hämta** (kortkommando: d) på instrument panelen:

![Ladda ned verktygsfältet på instrumentpanelen för projektet](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportera filer från menyn Data på en bärbar dator

1. Välj kommandot **fil** > **Hämta** meny:

    ![Data Download menykommandot inom en anteckningsbok](media/file-menu-download.png)

1. En popup visas som visar mapparna i sessionen. *projektmappen* innehåller projektfilerna:

    ![Data Download kommandot popup-fönstret där du kan välja filer och mappar](media/file-menu-download-popup.png)

1. Markera rutorna till vänster om de filer och mappar som du vill ladda ned och välj sedan **Hämta markerade**.

1. Antecknings boken förbereder en enda *zip* -fil som innehåller de valda filerna, som du sedan sparar som vanligt i webbläsaren. Antecknings boken skapar en *zip* -fil även när du laddar ned en enskild fil.

## <a name="next-steps"></a>Nästa steg

- [Komma åt moln data i en bärbar dator](access-data-resources-jupyter-notebooks.md)
