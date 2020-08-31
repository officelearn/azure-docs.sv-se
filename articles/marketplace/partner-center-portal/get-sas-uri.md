---
title: Hämta en SAS-URI för VM-avbildningen – Azure Marketplace
description: Generera en URL för signaturer för delad åtkomst (SAS) för dina virtuella hård diskar (VHD) i Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a84f287c6303e093d68dd462ccc5cecc34b463cd
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144547"
---
# <a name="get-a-sas-uri-for-your-vm-image"></a>Hämta en SAS-URI för VM-avbildningen

Under publicerings processen måste du ange en SAS-URI (Shared Access Signature) för varje virtuell hård disk som är kopplad till dina planer (tidigare kallade SKU: er). Microsoft behöver åtkomst till dessa VHD: er under certifierings processen. Du anger denna URI på fliken **planer** i Partner Center.

Att skapa SAS-URI: er för dina virtuella hård diskar uppfyller följande krav:

- De stöder bara ohanterade virtuella hård diskar.
- Endast list-och Läs behörigheter krävs. Ange inte Skriv-eller borttagnings behörighet.
- Tiden för åtkomst (utgångs datum) bör vara minst tre veckor från det att SAS-URI: n skapas.
- För att skydda mot UTC-tidsändringar anger du Start datumet till en dag före det aktuella datumet. Om det aktuella datumet är till exempel 16 juni 2020 väljer du 6/15/2020.

## <a name="generate-the-sas-address"></a>Generera SAS-adressen

Det finns två vanliga verktyg som används för att skapa en SAS-adress (URL):

1. **Microsoft Storage Explorer** – grafiskt verktyg som är tillgängligt för Windows, MacOS och Linux.
2. **Microsoft Azure CLI** – rekommenderas för operativ system som inte kommer från Windows och automatiserade eller kontinuerliga integrerings miljöer.

### <a name="using-tool-1-microsoft-storage-explorer"></a>Använda verktyg 1: Microsoft Storage Explorer

1. Hämta och installera [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Öppna Utforskaren och välj **Lägg till konto**i den vänstra menyn.
3. I dialog rutan **Anslut till Azure Storage** väljer du **Lägg till ett Azure-konto** och loggar in på ditt Azure-konto.
4. I det vänstra Explorer-fönstret expanderar du noden **lagrings konton** .
5. Högerklicka på din virtuella hård disk och välj **Hämta signatur för resurs åtkomst**.
6. I dialog rutan **signatur för delad åtkomst** fyller du i följande fält:

    1. Start tid – behörighetens start datum för VHD-åtkomst. Ange ett datum som infaller en dag före det aktuella datumet.
    2. Förfallo tid – behörighetens förfallo datum för VHD-åtkomst. Ange ett datum minst tre veckor bortom det aktuella datumet.
    3. Behörigheter – Välj behörigheterna läsa och lista.
    4. Container-Level – Markera kryss rutan skapa signatur-URI för delad åtkomst på behållare nivå.

    ![Dialog rutan signatur för delad åtkomst.](media/vm/create-sas-uri-storage-explorer.png)

7. Om du vill skapa en tillhör ande SAS-URI för den här virtuella hård disken väljer du **skapa**. Dialog rutan uppdateras och visar information om den här åtgärden.

8. Kopiera URI: n och spara den i en textfil på en säker plats.

    ![Kopierar URI.](media/vm/create-sas-uri-shared-access-signature-details.png)

    Den här genererade SAS-URI: n är för åtkomst på container nivå. Om du vill göra det speciellt kan du redigera text filen och lägga till VHD-namnet.

9. Infoga ditt VHD-namn efter VHD-strängen i SAS-URI: n (inklusive ett snedstreck). Den sista SAS-URI: n bör se ut så här:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

1. Upprepa de här stegen för varje virtuell hård disk i de planer som du ska publicera.

### <a name="using-tool-2-azure-cli"></a>Använda verktyg 2: Azure CLI

1. Hämta och installera [Microsoft Azure kl](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Versioner är tillgängliga för Windows, macOS och olika distributioner i Linux.
2. Skapa en PowerShell-fil (fil namns tillägget. ps1), kopiera i följande kod och spara den lokalt.

    ```JSON
    az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net’ --name <vhd-name> --permissions rl --start ‘<start-date>’ --expiry ‘<expiry-date>’
    ```

3. Redigera filen om du vill använda följande parameter värden. Ange datum i UTC-format, t. ex. 2020-04-01T00:00:00Z.

    - konto namn – namnet på ditt Azure Storage-konto.
    - konto nyckel – din Azure Storage-kontoinformation.
    - VHD-namn – ditt VHD-namn.
    - start datum – behörighetens start datum för VHD-åtkomst. Ange ett datum en dag före det aktuella datumet.
    - förfallo Datum-datum – behörighetens förfallo datum för VHD-åtkomst. Ange ett datum minst tre veckor efter det aktuella datumet.

    Här är ett exempel på korrekt parameter värden (när detta skrivs):

    `az storage container generate-sas --connection-string ‘DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net’ --name vhds -- permissions rl --start ‘2020-04-01T00:00:00Z’ --expiry ‘2021-04-01T00:00:00Z’`

1. Spara ändringarna.
2. Använd någon av följande metoder för att köra det här skriptet med administratörs behörighet för att skapa en SAS-anslutningssträng för åtkomst på behållare nivå:

    - Kör skriptet från-konsolen. I Windows högerklickar du på skriptet och väljer **Kör som administratör**.
    - Kör skriptet från en PowerShell-skriptfil som [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Den här skärmen visar hur en SAS-anslutningssträng skapas i den här redigeraren:

    [![skapa en SAS-anslutningssträng i PowerShell-redigeraren](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Kopiera SAS-anslutningssträngen och spara den i en textfil på en säker plats. Redigera den här strängen för att lägga till information om VHD-platsen för att skapa den sista SAS-URI: n.
7. I Azure Portal går du till blob-lagringen som innehåller den virtuella hård disk som är associerad med den nya URI: n.
8. Kopiera URL: en för thebBlob-tjänstens slut punkt:

    ![Kopierar URL: en för BLOB service-slutpunkten.](media/vm/create-sas-uri-blob-endpoint.png)

9. Redigera text filen med SAS-anslutningssträngen från steg 6. Skapa hela SAS-URI: n med följande format:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Verifiera SAS-URI: n

Kontrol lera SAS-URI: n innan du publicerar den på Partner Center för att undvika eventuella problem som rör SAS URI post sändning av begäran. Den här processen är valfri men rekommenderas.

- URI: n innehåller fil namnet för VHD-avbildningen, inklusive fil namns tillägget `.vhd` .
- `Sp=rl` visas nära mitten av din URI. Den här strängen visar Läs-och list åtkomst har angetts.
- När `sr=c` Detta visas innebär det att åtkomst till container nivå har angetts.
- Kopiera och klistra in URI: n i en webbläsare för att testa bloben (du kan avbryta åtgärden innan nedladdningen är klar).

## <a name="next-step"></a>Nästa steg

- Läs [skapa ett erbjudande för virtuell Azure-dator](azure-vm-create-offer.md).
