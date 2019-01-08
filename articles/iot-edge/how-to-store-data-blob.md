---
title: Store blockblob-objekt på enheter – Azure IoT Edge | Microsoft Docs
description: Distribuera en Azure Blob Storage-modulen till IoT Edge-enhet för att lagra data på gränsen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 01/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: b5dcc4295467aee82547bc2a0894320bd110998f
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065825"
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

Azure Marketplace är IoT Edge-moduler som kan distribueras direkt till din IoT Edge-enheter, inklusive Azure Blob Storage på IoT Edge. Följ dessa steg om du vill distribuera modulen från Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com), Sök efter ”Azure Blob Storage på IoT Edge”. Och **Välj** sökresultat från Marketplace.

   ![Skapa modul från Marketplace-sökning](./media/how-to-store-data-blob/marketplace-module.png)

2. Välj en IoT Edge-enhet för att ta emot den här modulen. På den **målenheter för IoT Edge-modul** anger du följande information:

   1. Välj den **prenumeration** som innehåller IoT-hubben som du använder.

   2. Välj din **IoT-hubb**.

   3. Om du vet att din **IoT Edge-enhetsnamn**, ange som i textrutan. Eller välj **hitta enhet** att välja från en lista med IoT Edge-enheter i IoT hub. 
   
   4. Välj **Skapa**.

   Nu när du har valt en IoT Edge-modul från Azure Marketplace och valt en IoT Edge-enhet för att ta emot modulen, kommer du till en tre steg-guide som hjälper dig att definiera exakt hur modulen ska distribueras.

3. I den **Lägg till moduler** steg i guiden ange moduler och Observera att den **AzureBlobStorageonIoTEdge** modulen finns redan under **distribution moduler**. 

2. Välj blob storage-modulen i listan över distributionen moduler att öppna modulinformation. 

   ![Välj Modulnamn att öppna modulinformation](./media/how-to-store-data-blob/open-module-details.png)

3. På den **anpassade IoT Edge-moduler** sidan, uppdatera Azure Blob Storage på IoT Edge-modul med följande steg:

   1. Ändra modulen **namn** vara gemener. Du kan byta namn på modulen om du vill, eller använda `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >Azure IoT Edge är skiftlägeskänsliga när du gör anrop till moduler och Storage-SDK som standard till gemener. För att säkerställa att dina anslutningar till Azure Blob Storage på IoT Edge-modul inte avbryts, namnge den gemener. 

   2. Standard **behållare skapa alternativ** port-bindningar som krävs för din behållare, men du måste också lägga till information på lagringskontot och en bindning för storage-katalog på din enhet. Skriv över JSON i portalen med JSON nedan:
    
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
   3. Uppdatera som du kopierade JSON med följande information: 

      * Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Kontonamn ska vara tre till 24 tecken långt, med gemena bokstäver och siffror.
      * Ersätt `<your storage account key>` med en 64-bytes base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.
      * Ersätt `<storage directory bind>` beroende på operativsystemet för behållaren. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data. Storage directory bind mappar en plats på din enhet som du tillhandahåller till en uppsättning plats i modulen. 

         * Linux-behållare:  **\<lagringssökväg >: / blobroot**. Till exempel/srv/containerdata: / blobroot. Eller, min volym: / blobroot. 
         * Windows-behållare:  **\<lagringssökväg >: C: / BlobRoot**. Till exempel C: / ContainerData:C: / BlobRoot. Eller, min-volymen: C: / blobroot.
   
      > [!IMPORTANT]
      > Ändra inte den andra hälften av Arkivkatalog binda värde, som pekar på en specifik plats i modulen. Storage directory bind alltid ska sluta med **: / blobroot** för Linux-behållare och **: C: / BlobRoot** för Windows-behållare.

      ![Uppdatera modulen behållare skapa alternativ - portalen](./media/how-to-store-data-blob/edit-module.png)

   4. Välj **Spara**.

4. Välj **nästa** ska fortsätta till nästa steg i guiden.
5. I den **ange vägar** steg i guiden väljer **nästa**.
6. I steget **Granska distribution** i guiden väljer du **Skicka**.
7. När du skickar in distributionen ska du gå tillbaka till den **IoT Edge** sidan för din IoT hub. Välj IoT Edge-enhet som du angett med distributionen att öppna dess egenskaper. 
8. I information om enheten kontrollerar du att blob storage-modulen visas som båda **anges vid distributionen** och **som rapporteras av enheten**. Det kan ta en liten stund för modulen igång på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan om du vill se den uppdaterade statusen. 

### <a name="visual-studio-code-templates"></a>Visual Studio Code-mallar

Azure IoT Edge innehåller mallar i Visual Studio Code för att hjälpa dig att utveckla edge-lösningar. Dessa steg kräver att du har [Visual Studio Code](https://code.visualstudio.com/) installerat på utvecklingsdatorn och konfigurerad med den [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Använd följande steg för att skapa en ny IoT Edge-lösning med ett blob storage-modulen och konfigurera distribution manifestet. 

1. Välj **visa** > **kommandot paletten**. 

2. I kommandopaletten anger och kör du kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning). Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **modul (RETUR hela avbildningen URL)**. |
   | Ange ett modulnamn | Ange ett namn på alla gemena för din modul som **azureblobstorage**.<br><br>Det är viktigt att använda ett gemener namn för Azure Blob Storage på IoT Edge-modul. IoT Edge är skiftlägeskänsligt när det gäller moduler och Storage-SDK som standard till gemener. |
   | Ange Docker-avbildning för modulen | Ange URI för avbildning: **mcr.microsoft.com/azure-blob-storage:latest** |

   VS Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster. Lösningsmallen skapar en manifest Distributionsmall som innehåller din avbildning i blob storage-modulen, men du måste konfigurera alternativ för att skapa modulens. 

3. Öppna **deployment.template.json** i din nya lösning arbetsyta och hitta den **moduler** avsnittet. Gör följande konfigurationsändringar:

   1. Ta bort den **tempSensor** modulen, eftersom den inte är nödvändigt för den här distributionen. 

   2. Kopiera och klistra in följande kod till den **createOptions** i din blob storage-modulen: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Uppdatera modulen createOptions - VS Code](./media/how-to-store-data-blob/create-options.png)

4. I Skapa-alternativen JSON uppdaterar `<storage directory bind>` beroende på operativsystemet för behållaren. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data. Storage directory bind mappar en plats på din enhet som du tillhandahåller till en uppsättning plats i modulen.  

   * Linux-behållare:  **\<lagringssökväg >: / blobroot**. Till exempel/srv/containerdata: / blobroot. Eller, min volym: / blobroot.
   * Windows-behållare:  **\<lagringssökväg >: C: / BlobRoot**. Till exempel C: / ContainerData:C: / BlobRoot. Eller, min-volymen: C: / blobroot.
   
   > [!IMPORTANT]
   > Ändra inte den andra hälften av Arkivkatalog binda värde, som pekar på en specifik plats i modulen. Storage directory bind alltid ska sluta med **: / blobroot** för Linux-behållare och **: C: / BlobRoot** för Windows-behållare.

5. Spara filen **deployment.template.json**.

6. Öppna den **.env** fil i din lösning arbetsyta. 

7. .Env-fil är konfigurerade att ta emot autentiseringsuppgifter för container-registret, men du behöver inte som för blob storage-avbildningen eftersom den är allmänt tillgängliga. I stället ersätta filen med två nya miljövariabler: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Ange ett värde för `STORAGE_ACCOUNT_NAME`, kontonamn ska vara tre till 24 tecken långt, med gemena bokstäver och siffror. Ange en 64-bytes base64-nyckeln för den `STORAGE_ACCOUNT_KEY`. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler. 

   Omfattar inte blanksteg eller citattecken runt de värden som du anger. 

9. Spara **.env**-filen. 

10. Högerklicka på **deployment.template.json** och välj **generera IoT Edge-distribution manifest**. 

11. Visual Studio Code tar den information som du erbjuds på deployment.template.json och .env och används för att skapa en ny distribution manifestfil. Distribution av manifestet skapas i en ny **config** mapp i din lösning arbetsyta. När du har filen kan du följa stegen i [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) eller [distribuera Azure IoT Edge-moduler med Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till ditt blob storage-modulen

Du kan använda kontonamnet och nyckeln för att du har konfigurerat att få åtkomst till bloblagring på IoT Edge-enhet. 

Ange din IoT Edge-enhet som blob-slutpunkt för lagring av alla begäranden som du gör. Du kan [skapa en anslutningssträng för en slutpunkt för lagring av explicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av informationen i IoT Edge och kontonamnet som du har konfigurerat. 

1. För moduler som har distribuerats på samma edge-enheten där ”Azure Blob Storage på IoT Edge” körs, blob-slutpunkten är: `http://<module name>:11002/<account name>`. 
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

