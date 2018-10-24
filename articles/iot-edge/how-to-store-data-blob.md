---
title: Azure Blob-lagring på Azure IoT Edge-enheter | Microsoft Docs
description: Distribuera en Azure Blob Storage-modulen till IoT Edge-enhet för att lagra data på gränsen.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 10/03/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b86f73302d9f5d07cd1e6e8c7801de56a988cc7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955294"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store data på gränsen med Azure Blob Storage på IoT Edge (förhandsversion)

Azure Blob Storage på IoT Edge innehåller en [blockblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) lagringslösning kant. Ett blob storage-modulen på din IoT Edge-enhet som fungerar som en tjänst för Azure block blob, men blockblob-objekt lagras lokalt på din IoT Edge-enhet. Du kan få åtkomst till dina blobar på samma sätt i Azure storage SDK eller blockera blob API-anrop som du redan är van att. 

Scenarier där data som videor, bilder, ekonomi, sjukhus data eller data som behöver lagras lokalt, senare som kan bearbetas lokalt eller överförs till molnet är bra exempel att använda den här modulen.

Den här artikeln innehåller instruktioner för att distribuera en Azure Blob Storage på IoT Edge-behållaren som kör en blob-tjänst på din IoT Edge-enhet. 

>[!NOTE]
>Azure Blob Storage på IoT Edge är i [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Förutsättningar

En Azure IoT Edge-enhet:

* Du kan använda utvecklingsdatorn eller en virtuell dator som en gränsenhet genom att följa stegen i snabbstarten för [Linux-](quickstart-linux.md) eller [Windows-enheter](quickstart.md).
* Azure Blob Storage på IoT Edge-modul har stöd för följande enhetskonfigurationer:

   | Operativsystem | Arkitektur |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (oktober uppdatering) | AMD64 |
   | Windows 10 IoT Enterprise (oktober uppdatering) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Molnresurser:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på standardnivå i Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Distribuera blob-lagring till din enhet

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar för Azure Blob Storage på IoT Edge-moduler. De två enklaste metoderna är att använda Azure-portalen eller Visual Studio Code-mallar. 

### <a name="azure-portal"></a>Azure Portal

#### <a name="find-the-module"></a>Hitta modulen

Välj något av två sätt att hitta blob storage-modulen:

1. I Azure Portal-Sök efter ”Azure Blob Storage på IoT Edge”. Och **Välj** resultatet sökobjekt
2. Gå till Marketplace från Azure Portal och klicka sedan på ”Internet of Things”. Välj ”Azure Blob Storage på IoT Edge” under ”IoT Edge-moduler”-avsnittet. Klicka **skapa**

#### <a name="steps-to-deploy"></a>Steg för att distribuera

**Målenheter för IoT Edge-modul**

1. Välj den ”prenumeration” där IoT-hubben har distribuerats.
2. Välj ”IoT Hub”.
3. Ange ”IoT Edge-enhetsnamnet” där du vill distribuera den här modulen. Du kan välja att använda ”hitta enhet” för att leta upp din enhet.
4. Klicka på **Skapa**.

**Ange moduler**

1. I avsnittet ”Lägg till moduler” under ”distribution moduler” hittar du denna modul är redan visas i listan med namn som börjar med ”AzureBlobStorageonIoTEdge”. 
2. **Välj** blob storage-modulen i listan över ”distribution moduler”. ”IoT Edge anpassade moduler” sidpanel öppnas.
3. **Namn på**: du kan ändra namnet på den här modulen
4. **Bild-URI: N**: Ersätt URI som **mcr.microsoft.com/azure-blob-storage:latest**
5. **Alternativ för behållaren skapa**: redigera JSON nedanför med dina värden och Ersätt den med JSON i Portal-sida:
   
   ```json
   {
       "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
       ],
       "HostConfig":{
           "Binds":[
               "<storage directory bind>"
           ],
           "PortBindings":{
               "11002/tcp":[{"HostPort":"11002"}]
           }
       }
   }
   ```   
   
    * Uppdatera `<your storage account name>`. Kontonamn ska vara tre till 24 tecken långt, med gemena bokstäver och siffror.
    * Uppdatera `<your storage account key>` med en 64-bytes base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.
    * Uppdatera `<storage directory bind>`. Beroende på operativsystemet för behållaren. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data.  

       * Linux-behållare:  **\<lagringssökväg >: / blobroot**. Till exempel/srv/containerdata: / blobroot. Eller, min volym: / blobroot. 
       * Windows-behållare:  **\<lagringssökväg >: C: / BlobRoot**. Till exempel C: / ContainerData:C: / BlobRoot. Eller, min-volymen: C: / blobroot.
   
   > [!CAUTION]
   > Ändra inte den ”/ blobroot” för Linux och ”C:/BlobRoot” för Windows, för  **\<Storage directory bind >** värden.

    ![Uppdatera modulen värden](./media/how-to-store-data-blob/edit-module.png)

6. **Spara** värdena i ”IoT Edge anpassade moduler”
7. Klicka på **nästa** i avsnittet ”Ställa in moduler”
8. Klicka på **nästa** i avsnittet ”Ange vägar”
9. När du har granskat, klickar du på **skicka** i avsnittet ”granska distribution”.
10. Kontrollera i IoT-hubben att enheten kör blob storage-modulen 

### <a name="visual-studio-code-templates"></a>Visual Studio Code-mallar

Azure IoT Edge innehåller mallar i Visual Studio Code för att hjälpa dig att utveckla edge-lösningar. Dessa steg kräver att du har [Visual Studio Code](https://code.visualstudio.com/) installerat på utvecklingsdatorn och konfigurerad med den [Azure IoT Edge-tillägget](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Använd följande steg för att skapa en ny IoT Edge-lösning med ett blob storage-modulen och konfigurera distribution manifestet. 

1. Välj **visa** > **kommandot paletten**. 

2. Ange i kommandopaletten, och kör kommandot **Azure IoT Edge: nya IoT Edge-lösning**. 

3. Följ anvisningarna för att skapa en ny lösning: 

   1. **Välj mapp** – Bläddra till mappen där du vill skapa den nya lösningen.  
   
   2. **Ange ett lösningsnamn** – ange ett namn för din lösning eller acceptera standardinställningarna.
   
   3. **Vald modul mallen** -Välj **modul (RETUR hela avbildningen URL)**.
   
   4. **Ange ett Modulnamn** -ange ett beskrivande namn för din modul som **azureBlobStorage**.
   
   5. **Ange Docker-avbildning för modulen** -ange URI för avbildning: **mcr.microsoft.com/azure-blob-storage:latest**

VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster. 

Lösningsmallen skapar en manifest Distributionsmall som innehåller din avbildning i blob storage-modulen, men du måste konfigurera alternativ för att skapa modulens. 

1. Öppna **deployment.template.json** i din nya lösning arbetsyta och hitta den **moduler** avsnittet. 

2. Ta bort den **tempSensor** modulen, eftersom den inte är nödvändigt för den här distributionen. 

3. Kopiera och klistra in följande kod till den **createOptions** i din blob storage-modulen: 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![Uppdatera modulen alternativ för att skapa](./media/how-to-store-data-blob/create-options.png)

4. I Skapa-alternativen JSON uppdaterar `<storage directory bind>` beroende på operativsystemet för behållaren. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data.  

   * Linux-behållare:  **\<lagringssökväg >: / blobroot**. Till exempel/srv/containerdata: / blobroot. Eller, min volym: / blobroot.
   * Windows-behållare:  **\<lagringssökväg >: C: / BlobRoot**. Till exempel C: / ContainerData:C: / BlobRoot. Eller, min-volymen: C: / blobroot.
   
   > [!CAUTION]
   > Ändra inte den ”/ blobroot” för Linux och ”C:/BlobRoot” för Windows, för  **\<Storage directory bind >** värden.

5. Spara **deployment.template.json**.

6. Öppna **.env** i lösningens arbetsyta. 

7. Du behöver inte ange alla registervärden för behållare för blob storage-avbildningen eftersom den är allmänt tillgängliga. Lägg till två nya miljövariabler i stället: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Ange ett värde för `STORAGE_ACCOUNT_NAME`, kontonamn ska vara tre till 24 tecken långt, med gemena bokstäver och siffror. Och ange en 64-bytes base64-nyckel för den `STORAGE_ACCOUNT_KEY`. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler. 

9. Spara **.env**. 

10. Högerklicka på **deployment.template.json** och välj **generera IoT Edge-distribution manifest**. 

Visual Studio Code tar den information som du erbjuds på deployment.template.json och .env och används för att skapa en ny distribution manifestfil. Distribution av manifestet skapas i en ny **config** mapp i din lösning arbetsyta. När du har filen kan du följa stegen i [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) eller [distribuera Azure IoT Edge-moduler med Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till ditt blob storage-modulen

Du kan använda kontonamnet och nyckeln för att du har konfigurerat att få åtkomst till bloblagring på IoT Edge-enhet. 

Ange din IoT Edge-enhet som blob-slutpunkt för lagring av alla begäranden som du gör. Du kan [skapa en anslutningssträng för en slutpunkt för lagring av explicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av informationen i IoT Edge och kontonamnet som du har konfigurerat. 

1. För moduler som har distribuerats på samma edge-enheten där ”Azure Blob Storage på IoT Edge” körs, blob-slutpunkten är: `http://<Module Name>:11002/<account name>`. 
2. För moduler som har distribuerats på olika edge-enhet än edge-enhet där ”Azure Blob Storage på IoT Edge” körs, så beroende på din konfiguration av blob-slutpunkten är: `http://<device IP >:11002/<account name>` eller `http://<IoT Edge device hostname>:11002/<account name>` eller `http://<FQDN>:11002/<account name>`

## <a name="logs"></a>Logs

Du hittar loggarna i behållaren under: 
* För Linux: /blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>Distribuera flera instanser

Om du vill distribuera flera instanser av Azure Blob Storage på IoT Edge, behöver du bara ändra HostPort som modulen Binder till. Blob storage-moduler exponera alltid port 11002 i behållaren, men du kan deklarera vilken port som den är bunden till på värden. 

Redigera modulen skapa alternativ för att ändra värdet för HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Ändra slutpunkten så att den pekar på den uppdaterade värdport när du ansluter till ytterligare blob storage-moduler. 

### <a name="try-it-out"></a>Prova

Azure Blob Storage-dokumentationen innehåller snabbstarter som innehåller exempelkoden på flera språk. Du kan köra de här exemplen för att testa Azure Blob Storage på IoT Edge genom att ändra blob-slutpunkten så att den pekar till ditt blob storage-modulen.

Följande snabbstarter använder språk som stöds också av IoT Edge, så att du kan distribuera dem som IoT Edge-moduler tillsammans med blob storage-modulen:

* [NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Stöds lagringsåtgärder

BLOB storage-moduler på IoT Edge använder samma Azure Storage SDK och stämmer överens med 2018-03-28-versionen av Azure Storage-API för block blob-slutpunkter. Senare versioner är beroende av kundernas behov. 

Inte alla åtgärder i Azure Blob Storage stöds av Azure Blob Storage på IoT Edge. Följande avsnitt visar vilka åtgärder som inte är en är stöds. 

### <a name="account"></a>Konto

Stöds: 
* Visa en lista med containrar

Stöds inte: 
* Hämta och ange egenskaper för blob
* Preliminära blob-begäran
* Hämta statistik för blob-tjänsten
* Hämta kontoinformation

### <a name="containers"></a>Containrar

Stöds: 
* Skapa och ta bort behållare
* Hämta egenskaper för behållare och metadata
* Lista blobar

Stöds inte: 
* Hämta och ange behållar-ACL
* Lånet behållare
* Ställ in metadata för behållaren

### <a name="blobs"></a>Blobar

Stöds: 
* Placera, hämta och ta bort blob
* Hämta och ange egenskaper för blob
* Hämta och ange blob-metadata

Stöds inte: 
* Lånet blob
* Ta ögonblicksbild av blob
* Kopiera och avbryta kopiering av blob
* Ångra borttagningen av bloben
* Ställ in blobbnivå

### <a name="block-blobs"></a>Blockblob-objekt

Stöds: 
* Placera block:-blocket måste vara mindre än eller lika med 4 MB i storlek
* Använda och få Blockeringslista

Stöds inte:
* Placera block från URL

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

