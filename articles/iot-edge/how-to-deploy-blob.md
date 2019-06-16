---
title: Distribuera Azure Blob Storage-modulen till enheter – Azure IoT Edge | Microsoft Docs
description: Distribuera en Azure Blob Storage-modulen till IoT Edge-enhet för att lagra data på gränsen.
author: kgremban
ms.author: kgremban
ms.date: 05/21/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: philmea
ms.openlocfilehash: d844e81de9cfb556e91ab5c0d5a8074c822cce0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65990470"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Distribuera Azure Blob Storage på IoT Edge-modul till din enhet

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar för Azure Blob Storage på IoT Edge-moduler. De två enklaste metoderna är att använda Azure-portalen eller Visual Studio Code-mallar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen.
- En [IoT Edge-enhet](how-to-register-device-portal.md) med IoT Edge-körningen installerad.
- [Visual Studio Code](https://code.visualstudio.com/) och [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) om distribuera från Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure portal

Azure-portalen visar hur du skapar ett manifest för distribution och skicka distributionen till en IoT Edge-enhet.

### <a name="select-your-device"></a>Välj din enhet

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn.
1. Klicka på ID för målenhet i listan över enheter.
1. Välj **Ange moduler**.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Azure-portalen har en guide som vägleder dig genom att skapa ett manifest för distribution, i stället för att skapa JSON-dokumentet manuellt. Den har tre steg: **Lägg till moduler**, **ange vägar**, och **granska distribution**.

#### <a name="add-modules"></a>Lägg till moduler

1. I den **distribution moduler** på sidan, väljer **Lägg till**.

1. Välj vilka typer av moduler i den nedrullningsbara listan **IoT Edge-modul**.

1. Ange ett namn för modulen och sedan ange behållaravbildningen:

   - **Namn på** -azureblobstorageoniotedge
   - **Bild-URI: N** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge är skiftlägeskänsliga när anrop till moduler och Storage-SDK är också till gemener. Även om namnet på modulen i den [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) är **AzureBlobStorageonIoTEdge**, ändra namnet till gemener hjälper att säkerställa att dina anslutningar till Azure Blob Storage på IoT Edge-modul inte avbryts.

1. Standard **behållare skapa alternativ** värden definiera port-bindningar som krävs för din behållare, men du måste också lägga till information på lagringskontot och en bindning för storage-katalog på din enhet. Ersätt standard JSON i portalen med JSON nedan:

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

1. Uppdatera som du kopierade JSON med följande information:

   - Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Kontonamn ska vara 3 och 24 tecken långt, med gemena bokstäver och siffror. Inga blanksteg.

   - Ersätt `<your storage account key>` med en 64-bytes base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.

   - Ersätt `<storage directory bind>` beroende på operativsystem för behållaren. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data. Storage directory bind mappar en plats på din enhet som du tillhandahåller till en uppsättning plats i modulen.

     - Formatet för Linux-behållare är  *\<lagringssökväg >: / blobroot*. Till exempel **/srv/containerdata: / blobroot** eller **Mina volymer: / blobroot**.
     - Formatet för Windows-behållare är  *\<lagringssökväg >: C: / BlobRoot*. Till exempel **C: / ContainerData:C: / BlobRoot** eller **min-volymen: C: / blobroot**.

     > [!IMPORTANT]
     > Ändra inte den andra hälften av Arkivkatalog binda värde, som pekar på en specifik plats i modulen. Storage directory bind alltid ska sluta med **: / blobroot** för Linux-behållare och **: C: / BlobRoot** för Windows-behållare.

    ![Uppdatera modulen behållare skapa alternativ - portalen](./media/how-to-store-data-blob/edit-module.png)

1. Ange [lagringsnivåer](how-to-store-data-blob.md#tiering-properties) och [time-to-live](how-to-store-data-blob.md#time-to-live-properties) egenskaper för genom att kopiera följande JSON och klistra in den i den **önskade egenskaper för modultvilling Set** box. Konfigurera varje egenskap med ett lämpligt värde, spara den och fortsätta med distributionen.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

      ```

   ![ställer in egenskaperna lagringsnivåer och time to live](./media/how-to-store-data-blob/iotedge_custom_module.png)

   Information om hur du konfigurerar lagringsnivåer och TTL när din modul har distribuerats finns i [redigera Modultvillingen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Mer information om önskade egenskaper finns i [definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).

1. Välj **Spara**.

1. Välj **nästa** att fortsätta till avsnittet vägar.

#### <a name="specify-routes"></a>Ange vägar

Behåll standardvägar och välj **nästa** att fortsätta till avsnittet granskning.

#### <a name="review-deployment"></a>Granska distribution

Granska avsnittet visas i du JSON-distributionen manifest som har skapats baserat på dina val i de föregående två avsnitt. Det finns även två moduler deklarerade att du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler som utgör den [IoT Edge-körningen](iot-edge-runtime.md) och är obligatoriskt standardvärden i varje distribution.

Granska information om din distribution, och välj sedan **skicka**.

### <a name="verify-your-deployment"></a>Verifiera din distribution

När du skickar in distributionen ska du gå tillbaka till den **IoT Edge** sidan för din IoT hub.

1. Välj IoT Edge-enhet som du angett med distributionen att öppna dess egenskaper.
1. I information om enheten kontrollerar du att blob storage-modulen visas som båda **anges vid distributionen** och **som rapporteras av enheten**.

Det kan ta en liten stund för modulen igång på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan om du vill se den uppdaterade statusen.

## <a name="deploy-from-visual-studio-code"></a>Distribuera från Visual Studio Code

Azure IoT Edge innehåller mallar i Visual Studio Code för att hjälpa dig att utveckla edge-lösningar. Använd följande steg att skapa en ny IoT Edge-lösning med ett blob storage-modulen och för att konfigurera distribution manifestet.

1. Välj **visa** > **kommandot paletten**.

1. I kommandopaletten anger och kör du kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning).

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

   Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj platsen på utvecklingsdatorn för Visual Studio Code för att skapa lösningsfiler. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **modul (RETUR hela avbildningen URL)** . |
   | Ange ett modulnamn | Ange ett namn på alla gemena för din modul som **azureblobstorage**.<br /><br />Det är viktigt att använda ett gemener namn för Azure Blob Storage på IoT Edge-modul. IoT Edge är skiftlägeskänsligt när det gäller moduler och Storage-SDK som standard till gemener. |
   | Ange Docker-avbildning för modulen | Ange URI för avbildning: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code tar den information du tillhandahålls, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster. Lösningsmallen skapar en manifest Distributionsmall som innehåller din avbildning i blob storage-modulen, men du måste konfigurera alternativ för att skapa modulens.

1. Öppna *deployment.template.json* i din nya lösning arbetsyta och hitta den **moduler** avsnittet. Gör följande konfigurationsändringar:

   1. Ta bort den **tempSensor** modulen, eftersom den inte är nödvändigt för den här distributionen.

   1. Kopiera och klistra in följande kod till den `createOptions` fält:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Uppdatera modulen createOptions – Visual Studio Code](./media/how-to-store-data-blob/create-options.png)

1. Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Kontonamn ska vara 3 och 24 tecken långt, med gemena bokstäver och siffror. Inga blanksteg.

1. Ersätt `<your storage account key>` med en 64-bytes base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.

1. Ersätt `<storage directory bind>` beroende på operativsystem för behållaren. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data. Storage directory bind mappar en plats på din enhet som du tillhandahåller till en uppsättning plats i modulen.  

      - Formatet för Linux-behållare är  *\<lagringssökväg >: / blobroot*. Till exempel **/srv/containerdata: / blobroot** eller **Mina volymer: / blobroot**.
      - Formatet för Windows-behållare är  *\<lagringssökväg >: C: / BlobRoot*. Till exempel **C: / ContainerData:C: / BlobRoot** eller **min-volymen: C: / blobroot**.

      > [!IMPORTANT]
      > Ändra inte den andra hälften av Arkivkatalog binda värde, som pekar på en specifik plats i modulen. Storage directory bind alltid ska sluta med **: / blobroot** för Linux-behållare och **: C: / BlobRoot** för Windows-behållare.

1. Konfigurera [lagringsnivåer](how-to-store-data-blob.md#tiering-properties) och [time-to-live](how-to-store-data-blob.md#time-to-live-properties) egenskaper för genom att lägga till följande JSON till det *deployment.template.json* fil. Konfigurera varje egenskap med ett lämpligt värde och spara filen.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }
   ```

   ![Ange önskade egenskaper för azureblobstorageoniotedge – Visual Studio Code](./media/how-to-store-data-blob/tiering_ttl.png)

   Information om hur du konfigurerar lagringsnivåer och TTL när din modul har distribuerats finns i [redigera Modultvillingen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Mer information om alternativ för att skapa, starta om princip och önskad status [EdgeAgent önskade egenskaper](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Spara filen *deployment.template.json*.

1. Högerklicka på **deployment.template.json** och välj **generera IoT Edge-distribution manifest**.

1. Visual Studio Code tar den information som du angav i *deployment.template.json* och används för att skapa en ny distribution manifestfil. Distribution av manifestet skapas i en ny **config** mapp i din lösning arbetsyta. När du har filen kan du följa stegen i [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) eller [distribuera Azure IoT Edge-moduler med Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Distribuera flera instanser av modul

Om du vill distribuera flera instanser av Azure Blob Storage på IoT Edge-modul, måste du ange en sökväg till olika lagringsenheter och ändra den `HostPort` värde som modulen Binder till. Blob storage-moduler exponera alltid port 11002 i behållaren, men du kan deklarera vilken port som den är bunden till på värden.

Redigera **behållare skapa alternativ** (i Azure portal) eller **createOptions** fältet (i den *deployment.template.json* filen i Visual Studio Code) att ändra `HostPort` värde:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ändra slutpunkten så att den pekar på den uppdaterade värdport när du ansluter till ytterligare blob storage-moduler.

## <a name="next-steps"></a>Nästa steg

Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).
