---
title: URI för signatur för delad åtkomst för VM-avbildningar – Azure Marketplace
description: Generera en URL för signaturer för delad åtkomst (SAS) för dina virtuella hård diskar (VHD) i Azure Marketplace.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: 24ba593a56d4d12211d10f143261caaf6d901bfc
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85964676"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Hämta signatur-URI för delad åtkomst för din VM-avbildning

Den här artikeln beskriver hur du skapar en signatur för delad åtkomst (SAS) URI (Uniform Resource Identifier) för varje virtuell hård disk (VHD).

Under publicerings processen måste du ange en URI för varje virtuell hård disk som är kopplad till dina planer. Dessa planer kallades tidigare för SKU: er eller lager enheter. Microsoft behöver åtkomst till dessa VHD: er under certifierings processen. Du anger denna URI på fliken **planer** i Partner Center.

När du genererar SAS-URI: er för dina virtuella hård diskar följer du dessa krav:

* Endast ohanterade virtuella hård diskar stöds.
* Endast `List` och `Read` behörigheter krävs. Ange inte Skriv-eller borttagnings behörighet.
* Tiden för åtkomst (utgångs datum) bör vara minst tre veckor från det att SAS-URI: n skapas.
* För att skydda mot UTC-tidsändringar anger du Start datumet till en dag före det aktuella datumet. Om det aktuella datumet är till exempel 6 oktober 2019 väljer du 10/5/2019.

## <a name="generate-the-sas-address"></a>Generera SAS-adressen

Det finns två vanliga verktyg som används för att skapa en SAS-adress (URL):

* **Microsoft Storage Explorer** – grafiskt verktyg som är tillgängligt för Windows, MacOS och Linux.
* **Microsoft Azure CLI** – rekommenderas för operativ system som inte kommer från Windows och automatiserade eller kontinuerliga integrerings miljöer.

### <a name="use-microsoft-storage-explorer"></a>Använd Microsoft Storage Explorer

1. Hämta och installera [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Öppna Utforskaren och välj **Lägg till konto**i den vänstra menyn. Dialog rutan **Anslut till Azure Storage** visas.
3. Välj **Lägg till ett Azure-konto** och **Logga**in. Slutför de nödvändiga stegen för att logga in på ditt Azure-konto.
4. I det**vänstra fönstret går** du till dina **lagrings konton** och expanderar den här noden.
5. Högerklicka på din virtuella hård disk och välj **Hämta signatur för resurs åtkomst**.
6. Dialog rutan **signatur för delad åtkomst** visas. Fyll i följande fält:

    * **Start tid** – behörighetens start datum för VHD-åtkomst. Ange ett datum som infaller en dag före det aktuella datumet.
    * **Förfallo tid** – behörighetens förfallo datum för VHD-åtkomst. Ange ett datum som infaller minst tre veckor bortom det aktuella datumet.
    * **Behörigheter** – Välj behörigheterna läsa och lista.
    * **Container-Level** – Markera kryss rutan **Skapa signatur-URI för delad åtkomst på behållare nivå** .

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Visar dialog rutan signatur för delad åtkomst":::

7. Om du vill skapa en tillhör ande SAS-URI för den här virtuella hård disken väljer du **skapa**. Dialog rutan uppdateras och visar information om den här åtgärden.
8. Kopiera **URI: n** och spara den i en textfil på en säker plats.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Visar informations rutan signatur för delad åtkomst":::

    Den här genererade SAS-URI: n är för åtkomst på container nivå. Om du vill göra det speciellt kan du redigera text filen för att lägga till VHD-namnet (nästa steg).

9. Infoga ditt VHD-namn efter VHD-strängen i SAS-URI: n (inklusive ett snedstreck). Den sista SAS-URI: n bör se ut så här:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Om namnet på VDH till exempel är `TestRGVM2.vhd` , blir den resulterande SAS-URI: n:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Upprepa de här stegen för varje virtuell hård disk i de planer som du ska publicera.

### <a name="using-azure-cli"></a>Använda Azure CLI

1. Hämta och installera [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Versioner är tillgängliga för Windows, macOS och olika distributioner i Linux.
2. Skapa en PowerShell-fil (fil namns tillägget. ps1), kopiera i följande kod och spara den lokalt.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Redigera filen om du vill använda följande parameter värden. Ange datum i UTC-format för UTC, till exempel `2020-04-01T00:00:00Z` .

    * `<account-name>`– Namnet på ditt Azure Storage-konto
    * `<account-key>`– Din Azure Storage-konto nyckel
    * `<vhd-name>`– Ditt VHD-namn
    * `<start-date>`– Behörighetens start datum för VHD-åtkomst. Ange ett datum en dag före det aktuella datumet.
    * `<expiry-date>`– Behörighetens förfallo datum för VHD-åtkomst. Ange ett datum minst tre veckor efter det aktuella datumet.

    I det här exemplet visas lämpliga parameter värden (när detta skrivs):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Spara ändringarna.
5. Använd någon av följande metoder för att köra det här skriptet med administratörs behörighet för att skapa en **SAS-anslutningssträng** för åtkomst på behållare nivå:

    * Kör skriptet från-konsolen. I Windows högerklickar du på skriptet och väljer **Kör som administratör**.
    * Kör skriptet från en PowerShell-skriptfil som [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Den här skärmen visar hur en SAS-anslutningssträng skapas i den här redigeraren:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Visar hur du skapar en SAS-anslutningssträng med Windows PowerShell ISE":::

6. Kopiera SAS-anslutningssträngen och spara den i en textfil på en säker plats. Redigera den här strängen för att lägga till information om VHD-platsen för att skapa den sista SAS-URI: n.
7. I Azure Portal går du till blob-lagringen som innehåller den virtuella hård disk som är associerad med den nya URI: n.
8. Kopiera URL: en för **BLOB service slut punkten**, som visas i följande skärm bild

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Visar Blob Service slut punkten":::

9. Redigera text filen med SAS-anslutningssträngen från steg 6. Skapa hela SAS-URI: n med följande format:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Om till exempel namnet på den virtuella hård disken är `TestRGVM2.vhd` , blir SAS-URI: n:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Upprepa dessa steg för varje virtuell hård disk i de SKU: er som du planerar att publicera.

## <a name="verify-the-sas-uri"></a>Verifiera SAS-URI: n

Granska varje skapad SAS-URI genom att använda följande check lista för att kontrol lera att:

* URI: n ser ut så här:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI: n innehåller fil namnet för VHD-avbildningen, inklusive fil namns tillägget. VHD.
* `sp=rl`visas nära mitten av din URI. Den här strängen visar att `Read` och `List` åtkomsten har angetts.
* När `sr=c` Detta visas innebär det att åtkomst till container nivå har angetts.
* Kopiera och klistra in URI: n i en webbläsare för att testa bloben (du kan avbryta åtgärden innan nedladdningen är klar).

## <a name="next-step"></a>Nästa steg

Om du har problem med att skapa en SAS-URI, se [vanliga SAS URL-problem](common-sas-uri-issues.md). Annars sparar du SAS-URI: erna på en säker plats för senare användning. Du behöver den för att publicera ditt VM-erbjudande i Partner Center.

* [Skapa ett erbjudande för virtuell Azure-dator](azure-vm-create-offer.md)
