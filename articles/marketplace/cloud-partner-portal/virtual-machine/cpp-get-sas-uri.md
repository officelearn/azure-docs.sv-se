---
title: Hämta signatur-URI för delad åtkomst för din Microsoft Azure-baserade VM-avbildning | Azure Marketplace
description: I artikeln beskrivs hur du hjlar med SAS-signatur (Shared Access Signature) för avbildningen av den virtuella datorn.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 2fdbc2a11bd963057b465a629757f2be51ae4061
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273859"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Skaffa signatur-URI för delad åtkomst för vm-avbildningen

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [Få signatur-URI för delad åtkomst för vm-avbildningen för](https://aka.ms/GetSASURI) att hantera dina migrerade erbjudanden.

Under publiceringsprocessen måste du ange en enhetlig resursidentifierare (URI) för varje virtuell hårddisk (VHD) som är associerad med sku:erna. Microsoft behöver åtkomst till dessa virtuella hårddiskar under certifieringsprocessen. I den här artikeln beskrivs hur du skapar en SAS-uri (Shared Access Signature) för varje virtuell hårddisk. Du kommer att ange den här URI:n på fliken **SKU:er** i Cloud Partner Portal.

När du skapar SAS-urier för dina virtuella hårddiskar följer du följande krav:

- Endast ohanterat virtuella hårddiskar stöds.
- `List`och `Read` behörigheter är tillräckliga. Ange *not* eller `Write` `Delete` inte ge åtkomst.
- Åtkomsttiden *(utgångsdatum)* bör vara minst tre veckor från det att SAS URI skapas.
- Om du vill skydda mot UTC-tidsvariationer ställer du in startdatumet till en dag före det aktuella datumet. Om det aktuella datumet till exempel är den 6 oktober 2014 väljer du 2014-05-05.

## <a name="generate-the-sas-url"></a>Generera SAS-URL:en

SAS-URL:en kan genereras på två vanliga sätt med hjälp av följande verktyg:

- Microsoft Storage Explorer – Grafiskt verktyg tillgängligt för Windows, macOS och Linux
- Microsoft Azure CLI - Rekommenderas för OS-enheter som inte är windows och automatiska eller kontinuerliga integrationsmiljöer

### <a name="azure-cli"></a>Azure CLI

Följ följande steg för att generera en SAS URI med Azure CLI.

1. Ladda ned och installera [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/). Versioner finns tillgängliga för Windows, macOS och olika distributioner av Linux.
2. Skapa en PowerShell-fil ( filnamnstillägg),`.ps1` kopiera i följande kod och spara den sedan lokalt.

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
   ```

3. Redigera filen för att ange följande parametervärden.  Datum ska anges i UTC-datetime-format, till exempel `2016-10-25T00:00:00Z`.
   - `<account-name>`- Namn på ditt Azure-lagringskonto
   - `<account-key>`- Nyckeln till ditt Azure-lagringskonto
   - `<vhd-name>`- Ditt VHD-namn
   - `<start-date>`- Startdatum för start av virtuell hårddisk för vhd-åtkomst. Ange ett datum en dag före det aktuella datumet.
   - `<expiry-date>`- Utgångsdatum för tillstånd för åtkomst till virtuell hårddisk.  Ange ett datum minst tre veckor efter det aktuella datumet.

   I följande exempel visas rätt parametervärden (vid tidpunkten för detta skrivande).

   ```azurecli-interactive
   az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
   ```

4. Spara ändringarna i det här PowerShell-skriptet.
5. Kör skriptet med administratörsbehörighet för att generera en *SAS-anslutningssträng* för åtkomst på behållarnivå.  Du kan använda två grundläggande metoder:
   - Kör skriptet från konsolen.  I Windows skriver du till exempel på skriptet och väljer **Kör som administratör**.
   - Kör skriptet från en PowerShell-skriptredigerare, till exempel [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/components/ise/introducing-the-windows-powershell-ise), med administratörsbehörighet.
     Följande visar en SAS-anslutningssträng som genereras i den här redigeraren.

     ![SAS URI-generering i PowerShell ISE](./media/publishvm_032.png)

6. Kopiera den resulterande SAS-anslutningssträngen och spara den i en textfil på en säker plats.  Du redigerar den här strängen om du vill lägga till den associerade VHD-platsinformationen i den för att skapa den slutliga SAS URI.You will edit this string to add the associated VHD location information to it to create the final SAS URI.
7. I Azure-portalen navigerar du till blob-lagringen som innehåller den virtuella hårddisken som är associerad med den nyligen genererade URI.In the Azure portal, navigate to the blob storage that contains the VHD associated with the newly generated URI.
8. Kopiera URL-värdet för **slutpunkten för Blob-tjänsten**, som visas nedan.

    ![Slutpunkt för Blob-tjänst i Azure-portalen](./media/publishvm_033.png)

9. Redigera textfilen med SAS-anslutningssträngen från steg 6.  Du ska konstruera hela SAS URI med följande format:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Om namnet på VDH till `TestRGVM2.vhd`exempel är , blir den resulterande SAS URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Upprepa dessa steg för varje virtuell hårddisk i de SKU:er som du planerar att publicera.

### <a name="microsoft-storage-explorer"></a>Utforskaren för Microsoft-lagring

Följ följande steg för att generera en SAS-URI med Microsoft Azure Storage Explorer.

1. Hämta och installera [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Öppna utforskaren och klicka på ikonen **Lägg till konto** i det vänstra menyfältet.  Dialogrutan **Anslut till Azure Storage** visas.
3. Välj **Lägg till ett Azure-konto** och klicka på **Logga in...**.  Fortsätt de steg som krävs för att logga in på ditt Azure-konto.
4. I fönstret **Utforskaren** till vänster navigerar du till dina **lagringskonton** och expanderar den här noden.
5. Högerklicka på din virtuella hårddisk och välj **Hämta Share Access-signatur** på snabbmenyn.

    ![Skaffa SAS-objekt i Azure Explorer](./media/publishvm_034.png)

6. Dialogrutan **Signatur för delad** åtkomst visas. Ange värden för följande fält:
   - **Starttid** - Startdatum för virtuell hårddiskåtkomst. Ange ett datum som är en dag före det aktuella datumet.
   - **Utgångstid** - Utgångsdatum för vhd-åtkomst.  Ange ett datum minst tre veckor efter det aktuella datumet.
   - **Behörigheter** - `Read` Välj `List` behörigheter och.

     ![DIALOGRUTAN SAS i Azure Explorer](./media/publishvm_035.png)

7. Klicka på **Skapa** om du vill skapa den associerade SAS URI för den här virtuella hårddisken.  Dialogrutan visar nu information om den här åtgärden.
8. Kopiera **URL-värdet** och spara det i en textfil på en säker plats.

    ![SAS URI skapas i Azure Explorer](./media/publishvm_036.png)

    Den här genererade SAS-URL:en är för åtkomst på behållarnivå.  För att göra det specifikt måste det associerade VHD-namnet läggas till i det.

9. Redigera textfilen. Infoga ditt VHD-namn efter strängen `vhds` i SAS-URL:en (inkludera ett inledande snedstreck).  Den slutliga SAS URI bör vara av formatet:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Om namnet på VDH till `TestRGVM2.vhd`exempel är , blir den resulterande SAS URI:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Upprepa dessa steg för varje virtuell hårddisk i de SKU:er som du planerar att publicera.

## <a name="verify-the-sas-uri"></a>Verifiera SAS URI

Granska och verifiera varje genererad SAS URI med hjälp av följande checklista.  Kontrollera att:

- URI:en är av `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + formen:`<sas-connection-string>`
- URI:n innehåller filnamnet för VHD-avbildningen, inklusive filnamnstillägget ".vhd".
- Mot mitten av URI, `sp=rl` visas. Den här strängen anger att `Read` och `List` åtkomst har angetts.
- Efter den `sr=c` punkten, visas också. Den här strängen anger att åtkomst på behållarnivå har angetts.
- Kopiera och klistra in URI i en webbläsare för att börja hämta den associerade blobben.  (Du kan avbryta åtgärden innan hämtningen är klar.)

## <a name="next-steps"></a>Nästa steg

Om du har problem med att generera en SAS URI läser du [Vanliga SAS-URL-problem](./cpp-common-sas-url-issues.md).  Annars sparar du SAS URI:er på en säker plats för senare användning. Det krävs för att [publicera ditt VM-erbjudande](./cpp-publish-offer.md) i Cloud Partner Portal.
