---
title: Hämta signatur för delad åtkomst URI för din Microsoft Azure-baserad avbildning | Microsoft Docs
description: Beskriver hur du hämtar signaturen för delad åtkomst (SAS) URI för din avbildning.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: dcfe744cc8ca6f3b3cd201898a79fcce3f24f8d5
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639924"
---
# <a name="get-shared-access-signature-uri-for-your-vm-image"></a>Hämta signatur för delad åtkomst URI för VM-avbildning

Under publiceringsprocessen ska ange du en uniform resource identifier (URI) för varje virtuell hårddisk (VHD) som är associerade med dina SKU: er. Microsoft behöver åtkomst till dessa VHD:er under certifieringsprocessen. Den här artikeln beskriver hur du skapar en signatur för delad åtkomst (SAS) URI för varje VHD. Anger du den här URI: N i den **SKU: er** flik i partnerportalen i molnet. 

När du genererar SAS URI: er för dina VHD: er, uppfylla följande krav:

- Endast ohanterade virtuella hårddiskar stöds.
- `List` och `Read` har rätt behörighet. Gör *inte* ger `Write` eller `Delete` åtkomst.
- Varaktigheten för åtkomsten (*förfallodatum*) bör vara minst tre veckor från när SAS-URI har skapats.
- Ange startdatum för att skydda mot variationer för UTC-tid, till en dag före aktuellt datum. Om det aktuella datumet infaller 6 oktober 2014, väljer du till exempel 10/5/2014.

## <a name="generate-the-sas-url"></a>Generera SAS-URL

SAS-Webbadressen kan skapas på två vanliga sätt med hjälp av följande verktyg:

-   Microsoft Storage Explorer – grafiskt verktyg som är tillgängliga för Windows, macOS och Linux
-   Microsoft Azure-CLI - rekommenderas för icke - Windows-operativsystem och miljöer för automatisk eller kontinuerlig integrering


### <a name="azure-cli"></a>Azure CLI

Använd följande steg för att generera en SAS-URI med Azure CLI.

1.  Ladda ned och installera den [Microsoft Azure CLI](https://azure.microsoft.com/documentation/articles/xplat-cli-install/).  Versioner är tillgängliga för Windows, macOS och olika Linux-distributioner. 
2.  Skapa en PowerShell-fil (`.ps1` filnamnstillägget), kopiera in följande kod och spara den lokalt.

    ``` powershell
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <vhd-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```
    
3.  Redigera filen för att ange följande parametervärden.  Datum måste anges i UTC-datum/tid-format, till exempel `10-25-2016T00:00:00Z`.
    - `<account-name>` -Ditt Azure storage-kontonamn
    - `<account-key>` -Ditt Azure storage-kontonyckel
    - `<vhd-name>` -Ditt namn på virtuell Hårddisk
    - `<start-date>` -Permission startdatum för VHD-åtkomst. Ange ett datum en dag före aktuellt datum. 
    - `<expiry-date>` -Permission utgångsdatumet för VHD-åtkomst.  Ange ett datum minst tre veckor efter det aktuella datumet. 
 
    I följande exempel visar rätt parametervärden (när detta skrivs).

    ``` powershell
        az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ONc+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name vhds --permissions rl --start '2017-11-06T00:00:00Z' --expiry '2018-08-20T00:00:00Z'
    ```
 
4. Spara ändringarna i det här PowerShell-skriptet.
5. Kör det här skriptet använder administrativa privilegier för att generera en *SAS anslutningssträngen* för åtkomst till nivån-behållare.  Du kan använda två sätt:
    - Kör skriptet från konsolen.  Till exempel i Windows, Skriv genom att klicka på skript och välj **kör som administratör**.
    - Kör skriptet från en PowerShell-Skriptredigerare, som den [Windows PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise), med administratörsprivilegier. 
  Följande visar en SAS-anslutningssträng som genereras inom den här redigeraren. 

    ![SAS-URI-generation i PowerShell ISE](./media/publishvm_032.png)

6. Kopiera strängen för SAS-anslutning och spara den till en textfil på en säker plats.  Du kommer att redigera den här strängen för att lägga till den associerade VHD-platsinformationen till det för att skapa den slutgiltiga SAS-URI. 
7. Gå till blob-lagringen som innehåller den virtuella Hårddisken som är associerade med den nyligen skapade URI i Azure-portalen.
8. Kopiera URL-värdet för den **Blobbtjänstens slutpunkt**, enligt nedan.

    ![BLOB service-slutpunkt i Azure-portalen](./media/publishvm_033.png)

9. Redigera filen med SAS-anslutningssträngen från steg 6.  Du ska skapa fullständiga SAS-URI i följande format:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Om namnet på VDH är till exempel `TestRGVM2.vhd`, och sedan den resulterande SAS-URI är:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Upprepa dessa steg för varje VHD i SKU: er som du planerar att publicera.


### <a name="microsoft-storage-explorer"></a>Microsoft Storage Explorer

Använd följande steg för att generera en SAS-URI med Microsoft Azure Storage Explorer.

1. Hämta och installera [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
2. Öppnar du Utforskaren och i den vänstra menyraden klickar du på den **Lägg till konto** ikon.  Den **Anslut till Azure Storage** dialogrutan visas.
3. Välj **Lägg till ett Azure-konto** och klicka på **Logga in...**.  Fortsätt de steg som krävs för att logga in på ditt Azure-konto.
4. I den vänstra **Explorer** fönstret navigerar du till din **Lagringskonton** och visa den här noden.
5. Högerklicka på din VHD och välj **hämta signatur för resursen åtkomst** på snabbmenyn. 

    ![Hämta SAS-objekt i Azure Explorer](./media/publishvm_034.png)

6. Den **signatur för delad åtkomst** dialogrutan visas. Ange värden för följande fält:
    - **Starttid** -behörighet startdatum för VHD-åtkomst. Ange ett datum som är en dag före aktuellt datum.
    - **Förfallotiden** -behörigheten upphör att gälla för VHD-åtkomst.  Ange ett datum minst tre veckor efter det aktuella datumet.
    - **Behörigheter** – Välj den `Read` och `List` behörigheter. 

    ![SAS-dialog i Azure Explorer](./media/publishvm_035.png)

7. Klicka på **skapa** att skapa den associerade SAS-URI för den här virtuella Hårddisken.  Dialogrutan visar nu information om den här åtgärden. 
8. Kopiera den **URL** värde och spara den till en textfil på en säker plats. 

    ![SAS-URI skapa i Azure Explorer](./media/publishvm_036.png)

    Den här genererade SAS-URL är för behållaren behörighet.  Om du vill göra det specifika, måste associerade VHD-namn läggas till den.

9. Redigera textfilen. Infoga namnet på din virtuella Hårddisken efter den `vhds` strängen i SAS-Webbadressen (inkludera ett inledande snedstreck).  Den slutliga SAS-URI bör vara i formatet:

    `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`

    Om namnet på VDH är till exempel `TestRGVM2.vhd`, och sedan den resulterande SAS-URI är:

    `https://catech123.blob.core.windows.net/vhds/TestRGVM2.vhd?st=2018-05-06T07%3A00%3A00Z&se=2019-08-02T07%3A00%3A00Z&sp=rl&sv=2017-04-17&sr=c&sig=wnEw9RfVKeSmVgqDfsDvC9IHhis4x0fc9Hu%2FW4yvBxk%3D`

Upprepa dessa steg för varje VHD i SKU: er som du planerar att publicera.


## <a name="verify-the-sas-uri"></a>Kontrollera SAS-URI

Granska och kontrollera att varje genererad SAS-URI med hjälp av följande checklista.  Kontrollera att:
- URI: N är i formatet:       `<blob-service-endpoint-url>` + `/vhds/` + `<vhd-name>?` + `<sas-connection-string>`
- URI: N innehåller din VHD-avbildning filnamnet och filnamnstillägget ”VHD”.
- Mot mitten av URI: N, `sp=rl` visas. Den här strängen indikerar att `Read` och `List` åtkomst har angetts.
- Därefter `sr=c` visas också. Den här strängen anger att behållaren behörighet har angetts.
- Kopiera och klistra in URI: N i en webbläsare för att börja att ladda ned den associerade blobben.  (Du kan avbryta åtgärden innan nedladdningen är klar.)


## <a name="next-steps"></a>Nästa steg

Om du har problem att generera en SAS-URI, läser [vanliga SAS-Webbadressen utfärdar](./cpp-common-sas-url-issues.md).  I annat fall spara SAS URI(s) till en säker plats för senare användning. Det kommer att behöva [publicera ditt erbjudande för virtuell dator](./cpp-publish-offer.md) i partnerportalen i molnet.
