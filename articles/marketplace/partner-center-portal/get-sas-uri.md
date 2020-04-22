---
title: Skaffa signatur-URI för delad åtkomst för vm-avbildningen | Azure Marketplace
description: I den här artikeln beskrivs hur du hämtar SAS-uri (Shared Access Signature) för varje virtuell hårddisk (VHD).
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mingshen
ms.openlocfilehash: c226d35647e4a5a2b1d583dd6328bfb73dae2a1c
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732643"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Skaffa signatur-URI för delad åtkomst för vm-avbildningen

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Azure Virtual Machine-erbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Få signatur-URI för delad åtkomst för din VM-avbildning](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri) för Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs hur du skapar en ENHETLIG RESURSIDENTIFIERARE (SHARED Access Signature) (URI) för varje virtuell hårddisk (VHD).

Under publiceringsprocessen måste du ange en URI för varje virtuell hårddisk som är kopplad till dina planer. Dessa planer kallades tidigare SKU:er eller lagerhållningsenheter. Microsoft behöver åtkomst till dessa virtuella hårddiskar under certifieringsprocessen. Du anger den här URI:n på fliken **Planer** i Partnercenter.

När du genererar SAS-URI:er för dina virtuella hårddiskar följer du dessa krav:

* Endast ohanterat virtuella hårddiskar stöds.
* Endast `List` `Read` och behörigheter krävs. Ange inte skriv- eller ta bort åtkomst.
* Åtkomsttiden (utgångsdatum) bör vara minst tre veckor från det att SAS URI skapas.
* Om du vill skydda mot UTC-tidsändringar anger du startdatumet till en dag före det aktuella datumet. Om det aktuella datumet till exempel är den 6 oktober 2019 väljer du 2019-05-05.

## <a name="generate-the-sas-address"></a>Generera SAS-adressen

Det finns två vanliga verktyg som används för att skapa en SAS-adress (URL):

* **Microsoft Storage Explorer** – Grafiskt verktyg tillgängligt för Windows, macOS och Linux.
* **Microsoft Azure CLI** – Rekommenderas för operativsystem som inte är Windows och automatiska eller kontinuerliga integrationsmiljöer.

### <a name="use-microsoft-storage-explorer"></a>Använda Utforskaren för Microsoft Storage

1. Hämta och installera [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Öppna utforskaren och välj Lägg **till konto**på den vänstra menyn . Dialogrutan **Anslut till Azure Storage** visas.
3. Välj **Lägg till ett Azure-konto** och logga sedan **in**. Slutför de nödvändiga stegen för att logga in på ditt Azure-konto.
4. Gå till **lagringskontona i** fönstret**Till** vänster och expandera den här noden.
5. Högerklicka på den virtuella hårddisken och välj sedan **Hämta Signature för delningsåtkomst**.
6. Dialogrutan **Signatur för delad åtkomst** visas. Fyll i följande fält:

    * **Starttid** – Startdatum för vhd-åtkomst. Ange ett datum som är en dag före det aktuella datumet.
    * **Utgångstid** – Utgångsdatum för åtkomst till virtuell hårddisk. Ange ett datum som är minst tre veckor efter det aktuella datumet.
    * **Behörigheter** – Välj behörigheterna Läs och Lista.
    * **Behållare- nivå** – Kontrollera kryssrutan **Generera delad åtkomstsignatur på behållaresnivå.**

        :::image type="content" source="media/create-sas-uri-storage-explorer.png" alt-text="Illustrerar dialogrutan Signatur för delad åtkomst":::

7. Om du vill skapa den associerade SAS URI för den här virtuella hårddisken väljer du **Skapa**. Dialogrutan uppdateras och visar information om den här åtgärden.
8. Kopiera **URI:n** och spara den i en textfil på en säker plats.

    :::image type="content" source="media/create-sas-uri-shared-access-signature-details.png" alt-text="Illustrerar information om signaturen delad åtkomst":::

    Den här genererade SAS URI är för åtkomst på behållarnivå. Om du vill göra den specifik redigerar du textfilen för att lägga till VHD-namnet (nästa steg).

9. Infoga ditt VHD-namn efter vhds-strängen i SAS URI (inkludera ett snedstreck). Den slutliga SAS URI bör se ut så här:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`Om namnet på VDH till `TestRGVM2.vhd`exempel är , blir den resulterande SAS URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

10. Upprepa dessa steg för varje virtuell hårddisk i de planer som du ska publicera.

### <a name="using-azure-cli"></a>Använda Azure CLI

1. Ladda ned och installera [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Versioner finns tillgängliga för Windows, macOS och olika distributioner av Linux.
2. Skapa en PowerShell-fil (.ps1 filnamnstillägg), kopiera i följande kod och spara den sedan lokalt.

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Redigera filen om du vill använda följande parametervärden. Ange datum i UTC-datetime-format, till exempel `2020-04-01T00:00:00Z`.

    * `<account-name>`– Ditt Azure-lagringskontonamn
    * `<account-key>`– Nyckeln till ditt Azure-lagringskonto
    * `<vhd-name>`– Ditt VHD-namn
    * `<start-date>`– Startdatum för start av virtuell hårddisk för VHD-åtkomst. Ange ett datum en dag före det aktuella datumet.
    * `<expiry-date>`– Behörighetsförfallodatum för VHD-åtkomst. Ange ett datum minst tre veckor efter det aktuella datumet.

    I det här exemplet visas rätt parametervärden (vid tidpunkten för detta skrivning):

    ```PowerShell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

4. Spara ändringarna.
5. Med någon av följande metoder kör du skriptet med administratörsbehörighet för att skapa en **SAS-anslutningssträng** för åtkomst på behållarnivå:

    * Kör skriptet från konsolen. Högerklicka på skriptet i Windows och välj **Kör som administratör**.
    * Kör skriptet från en PowerShell-skriptredigerare som [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). På den här skärmen visas skapandet av en SAS-anslutningssträng i den här redigeraren:

     :::image type="content" source="media/create-sas-uri-power-shell-ise.png" alt-text="Illustrerar skapandet av en SAS-anslutningssträng med Windows PowerShell ISE":::

6. Kopiera SAS-anslutningssträngen och spara den i en textfil på en säker plats. Redigera den här strängen om du vill lägga till VHD-platsinformation för att skapa den slutliga SAS URI.Edit this string to add the VHD location information to create the final SAS URI.
7. Gå till blob-lagringen som innehåller den virtuella hårddisken som är associerad med den nya URI:n i Azure-portalen.
8. Kopiera URL:en för slutpunkten för **Blob-tjänsten,** vilket visas i följande skärmbild

    :::image type="content" source="media/create-sas-uri-blob-endpoint.png" alt-text="Illustrerar slutpunkten för Blob-tjänsten":::

9. Redigera textfilen med SAS-anslutningssträngen från steg 6. Skapa hela SAS URI med det här formatet:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

    Om namnet på den virtuella hårddisken till exempel är `TestRGVM2.vhd`kommer SAS URI att vara:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Upprepa dessa steg för varje virtuell hårddisk i de SKU:er som du planerar att publicera.

## <a name="verify-the-sas-uri"></a>Verifiera SAS URI

Granska varje skapad SAS URI med hjälp av följande checklista för att kontrollera att:

* URI ser ut så här:`<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
* URI:n innehåller filnamnet för VHD-avbildningen, inklusive filnamnstillägget ".vhd".
* `sp=rl`visas mitt i URI:n. Den här `Read` strängen visar det och `List` åtkomst har angetts.
* När `sr=c` visas innebär det att åtkomst på behållarnivå anges.
* Kopiera och klistra in URI i en webbläsare för att testa hämtningen av blobben (du kan avbryta åtgärden innan hämtningen är klar).

## <a name="next-step"></a>Nästa steg

Om du har problem med att skapa en SAS-URI läser du [Vanliga SAS-URL-problem](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues). Annars sparar du SAS URI:er på en säker plats för senare användning. Du behöver det för att publicera ditt VM-erbjudande i Partner Center.

* [Skapa ett erbjudande för virtuella datorer i Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)
