---
title: Importera och exportera data med projekt med Azure-anteckningsböcker
description: Hur att överföra data till ett projekt med Azure-datorer från externa källor, och hur du exporterar data från ett projekt.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 586b423b-6668-4bdd-9592-4c237d7458fb
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b522b0bd641d0147518843b11be4cd3a1430ae20
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59265346"
---
# <a name="work-with-data-files-in-azure-notebook-projects"></a>Arbeta med datafiler i Azure Notebook-projekt

Data är livsnerven för många Jupyter notebooks, särskilt anteckningsböcker som används för datavetenskap. Med Azure Notebooks, kan du enkelt importera från olika källor till ett projekt och sedan använda dessa data från bärbara datorer. Du kan också ha anteckningsböcker som genererar data som lagras i projektet som du sedan kan hämta för användning på annan plats.

Den **Data** menyn inom en pågående anteckningsbok innehåller också **överför** och **hämta** kommandon som fungerar med filer i projektet, samt temporära filer för aktuellt notebook-session.

Du kan också använda kod i en anteckningsbok får direktåtkomst till en mängd olika datakällor, inklusive filer i ett projekt. Du kan också komma åt valfria data med hjälp av kommandon i en kodcell. Eftersom sådana data lagras i variablerna i notebook-session, sparas inte den i projektet om du inte använder kod att specifikt generering av projektfiler.

Arbeta med kod i data är enklast att inom en pågående anteckningsbok själva: för detta ändamål som avser den [komma till dina data i Azure-anteckningsböcker exempel notebook](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Resten av den här artikeln innehåller information om projektet på servernivå filåtgärder.

## <a name="import-data"></a>Importera data

Du kan sätta filer i ett projekt från instrumentpanelen för projektet eller inom en pågående bärbar dator med hjälp av antingen den **Data** menyn eller ett kommando som `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importera filer från instrumentpanelen för projektet

1. Navigera till mappen där du vill importera filerna i projektet.

1. Välj den **överför** kommando och sedan antingen **från URL: en** eller **från datorn** och projekt informationen som krävs för de data som du vill importera:

   - **Från URL: en**: Ange källadress i den **URL: en för filen** fältet och filnamnet för att tilldela till anteckningsboken i ditt projekt i den **filnamn** fält. Välj sedan **+ Lägg till filen** att lägga till URL: en i överföringslistan över. Upprepa processen för eventuella ytterligare URL: er och välj sedan **klar**.

     ![Ladda upp från URL: en popup-fönstret](media/quickstarts/upload-from-url-popup.png)

   - **Från datorn**: Dra och släppa filer i popup-fönstret, eller välja **Välj filer**, bläddra till och välj de filer du vill importera. Du kan släppa eller välja valfritt antal filer av valfri typ och format, eftersom det är upp till koden i anteckningsboken för att öppna filen och parsa data.

     ![Överför från datorn popup-fönstret](media/quickstarts/upload-from-computer-popup.png)

1. När du importerat filer visas på instrumentpanelen för projektet och kan nås i anteckningsboken kod med relativa sökvägar till mappen som innehåller.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importera filer från Arkiv-menyn i en anteckningsbok

1. Inom en pågående anteckningsbok, Välj den **filen** > **överför** kommando:

    ![Filen uppladdning menykommandot inom en anteckningsbok](media/file-menu-upload.png)

1. I dialogrutan som öppnas, navigera till och välj de filer som du vill ladda upp. Du kan välja valfritt antal filer av valfri typ. Välj **öppna** när du är klar.

1. I den **status för uppladdning** popup-fönstret som visas, väljer du en **målmapp** från den nedrullningsbara listan:

    - Sessionen mapp (*~/* ): Överför filer till den aktuella notebook-sessionen men skapa inte filer i projektet. Mappen session är en peer till projektmappen, men bevaras inte när sessionen har upphört. Prefixet filnamnen med den relativa sökvägen för att komma åt sessionsfiler i koden, *... /*.

        Med hjälp av mappen session är användbar för experimentering och undviker att fylla i projektet med filer som du kanske eller kanske inte behöver på lång sikt. Du kan också ladda upp filer till mappen session som har identiska namn till filer i projektet utan att orsaka konflikter och utan att behöva byta namn på filerna. Exempel: Anta att du har en version av *data.csv* i projektet redan, där du vill experimentera med en annan version av *data.csv*. Du kan köra anteckningsboken med hjälp av data i den överförda filen genom att ladda upp filen till mappen session (refererar till den i kod med *... /data.csv*) i stället för data i projektets-filen.

    - Projektmapp, till exempel (*/project*): Överför filer till projektet där de kan vara nås med hjälp av relativa sökvägar i kod. Ladda upp en fil i den här mappen är samma som att ladda upp en fil på instrumentpanelen för projektet. Filen sparas med projektet och är tillgängliga i senare sessioner.

        Överföringen misslyckas om du försöker överföra en fil med samma namn som en som redan finns i projektet. Om du vill skriva över en fil, ladda upp den nya filen från instrumentpanelen för projektet i stället, vilket ger dig möjlighet att skriva över.

1. Välj **börja ladda upp** att slutföra processen.

### <a name="create-or-import-files-using-commands"></a>Skapa eller importera filer med hjälp av kommandon

Du kan använda kommandon i en terminal eller i en Python-kodcell för att skapa filer i mappar för både projekt och sessionen. Till exempel kommandon som `curl` och `wget` hämta filer direkt från Internet.

Ladda ned filer i terminalen, markera den **Terminal** kommandot på instrumentpanelen för projektet och sedan ange lämpliga kommandon:

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

När du använder en Python-kodcell på en bärbar dator, prefix kommandon med `!`.

Projektmappen är den standardmapp, så att ange en Målfilnamnet som *oil_price.csv* skapar filen i projektet. Prefix för att skapa en sessionsfil, namnet på med *... /* som i *... /oil_price.csv*.

### <a name="create-files-in-code"></a>Skapa filer i kod

När du använder kod som skapar en fil, till exempel pandas `write_csv` funktion, sökvägar är alltid i förhållande till projektmappen. Med hjälp av *... /* skapas en sessionsfil som tas bort när anteckningsboken stannas och stängas.

## <a name="export-files"></a>Exportera filer

Du kan exportera data från instrumentpanelen för projektet eller inifrån en anteckningsbok.

## <a name="export-files-from-the-project-dashboard"></a>Exportera filer från instrumentpanelen för projektet

Högerklicka på en fil på instrumentpanelen för projektet och välj **hämta**:

![Ladda ned kommandot på snabbmenyn för projektet objekt](media/download-command.png)

Du kan också välja en fil och använda den **hämta** kommando (kortkommandot: d) på instrumentpanelen:

![Ladda ned verktygsfältet på instrumentpanelen för projektet](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exportera filer från menyn Data på en bärbar dator

1. Välj den **filen** > **hämta** menykommandot:

    ![Data Download menykommandot inom en anteckningsbok](media/file-menu-download.png)

1. Ett popup-fönster visas som visar mapparna i sessionen. den *projekt* mappen innehåller projektfilerna:

    ![Data Download kommandot popup-fönstret där du kan välja filer och mappar](media/file-menu-download-popup.png)

1. Markerar kryssrutorna till vänster för filer och mappar som du vill ladda ned och välj sedan **hämta valda**.

1. Anteckningsboken förbereder en enda *.zip* -fil som innehåller de valda filerna som du sedan sparar som du normalt göra från din webbläsare. Anteckningsboken skapar en *.zip* filen även när du hämtar en enskild fil.

## <a name="next-steps"></a>Nästa steg

- [Åtkomst till molndata på en bärbar dator](access-data-resources-jupyter-notebooks.md)
