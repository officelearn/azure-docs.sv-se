---
title: Distribuera Azure Blob Storage-modulen till enheter-Azure IoT Edge | Microsoft Docs
description: Distribuera en Azure Blob Storage-modulen till IoT Edge-enhet för att lagra data på gränsen.
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: kgremban
manager: mchad
ms.openlocfilehash: 089c90abb999751db77bbe1d89d1d118ae712b52
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947084"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Distribuera Azure-Blob Storage i IoT Edge-modulen till din enhet

Det finns flera sätt att distribuera moduler till en IoT Edge enhet och alla fungerar för Azure Blob Storage i IoT Edge-moduler. De två enklaste metoderna är att använda Azure-portalen eller Visual Studio Code-mallar.

## <a name="prerequisites"></a>Förutsättningar

- En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure-prenumerationen.
- En [IoT Edge-enhet](how-to-register-device-portal.md) med IoT Edge-körningen installerad.
- [Visual Studio Code](https://code.visualstudio.com/) och [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) om du distribuerar från Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure Portal

Azure Portal vägleder dig genom att skapa ett distributions manifest och distribuera distributionen till en IoT Edge enhet.

### <a name="select-your-device"></a>Välj din enhet

1. Logga in på den [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn.
1. Klicka på ID för målenhet i listan över enheter.
1. Välj **Ange moduler**.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett manifest för distribution

Ett manifest för distribution är ett JSON-dokument som beskriver vilka moduler för att distribuera, hur data flödar mellan moduler och önskade egenskaper för modultvillingar. Azure Portal har en guide som vägleder dig genom att skapa ett distributions manifest i stället för att skapa JSON-dokumentet manuellt. Det finns tre steg: **Lägg till moduler**, **Ange vägar**och **Granska distribution**.

#### <a name="add-modules"></a>Lägg till moduler

1. I den **distribution moduler** på sidan, väljer **Lägg till**.

1. Välj **IoT Edge modul**i list rutans typer.

1. Ange ett namn för modulen och ange sedan behållar avbildningen:

   - **Namn** – azureblobstorageoniotedge
   - **Avbildnings-URI** – MCR.Microsoft.com/Azure-Blob-Storage:Latest

   > [!IMPORTANT]
   > Azure IoT Edge är Skift läges känslig när du anropar moduler, och Storage SDK: n är också i gemener. Även om namnet på modulen på [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) är **AzureBlobStorageonIoTEdge**kan du med hjälp av att ändra namnet till gemener se till att dina anslutningar till Azure-Blob Storage i IoT Edge-modulen inte avbryts.

1. Värdena för standard **containern Create Options** definierar de Port bindningar som din behållare behöver, men du måste också lägga till information om lagrings kontot och en montering för lagringen på enheten. Ersätt standard-JSON i portalen med JSON nedan:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Uppdatera JSON som du kopierade med följande information:

   - Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Konto namn ska vara 3 till 24 tecken långa, med gemener och siffror. Inga blank steg.

   - Ersätt `<your storage account key>` med en 64 byte-base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.

   - Ersätt `<storage mount>` enligt behållar operativ systemet. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data. Lagrings monteringen mappar en plats på enheten som du anger till en angiven plats i modulen.

     - För Linux-behållare är  *\<formatet lagrings Sök väg eller volym >:/blobroot*. Exempel
         - Använd [volym montering](https://docs.docker.com/storage/volumes/): **min-volym:/blobroot** 
         - Använd [BIND Mount](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Se till att följa stegen för att [bevilja katalog åtkomst till behållar användaren](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - För Windows-behållare är  *\<formatet lagrings Sök väg eller volym >: C:/BlobRoot*. Exempel
         - Använd [volym montering](https://docs.docker.com/storage/volumes/): **min-volym: C:/blobroot**. 
         - Använd [BIND-montering](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData: C:/BlobRoot**.
         - I stället för att använda din lokala enhet kan du mappa din SMB-nätverks plats. mer information finns i [använda SMB-resurs som lokal lagring](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ändra inte den andra halvan av lagrings montering svärdet, som pekar på en angiven plats i modulen. Lagrings monteringen ska alltid avslutas med **:/blobroot** för Linux-behållare och **: C:/blobroot** för Windows-behållare.

1. Ange egenskaper för [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) och [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) för modulen genom att kopiera följande JSON och klistra in den i rutan **Ange önskade egenskaper för modulen** . Konfigurera varje egenskap med ett lämpligt värde, spara den och fortsätt med distributionen.

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![Ange alternativ för att skapa behållare, egenskaper för deviceAutoDeleteProperties och deviceToCloudUploadProperties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Information om hur du konfigurerar deviceToCloudUploadProperties och deviceAutoDeleteProperties efter att modulen har distribuerats finns i [Redigera modulens dubbla](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Mer information om önskade egenskaper finns i [definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).

1. Välj **Spara**.

1. Välj **nästa** att fortsätta till avsnittet vägar.

#### <a name="specify-routes"></a>Ange vägar

Behåll standard vägarna och välj **Nästa** för att fortsätta till gransknings avsnittet.

#### <a name="review-deployment"></a>Granska distribution

Granska avsnittet visas i du JSON-distributionen manifest som har skapats baserat på dina val i de föregående två avsnitt. Det finns också två moduler som har deklarerats att du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler som utgör den [IoT Edge-körningen](iot-edge-runtime.md) och är obligatoriskt standardvärden i varje distribution.

Granska information om din distribution, och välj sedan **skicka**.

### <a name="verify-your-deployment"></a>Verifiera distributionen

När du har skickat distributionen återgår du till **IoT Edge** sida i IoT Hub.

1. Välj den IoT Edge enhet som du har för distributionen för att öppna informationen.
1. I enhets informationen kontrollerar du att Blob Storage-modulen anges som båda **anges i distributionen** och **rapporteras av enheten**.

Det kan ta en stund innan modulen har startats på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan om du vill se en uppdaterad status.

## <a name="deploy-from-visual-studio-code"></a>Distribuera från Visual Studio Code

Azure IoT Edge innehåller mallar i Visual Studio Code för att hjälpa dig att utveckla edge-lösningar. Använd följande steg för att skapa en ny IoT Edge-lösning med en Blob Storage-modul och konfigurera distributions manifestet.

1. Välj **visa** > **kommandot paletten**.

1. I kommandopaletten anger och kör du kommandot **Azure IoT Edge: Ny IoT Edge-lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

   Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Skapa lösningsfiler genom att välja platsen på utvecklings datorn för Visual Studio Code. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **befintlig modul (ange fullständig bild-URL)** . |
   | Ange ett modulnamn | Ange ett namn på gemener för din modul, t. ex. **azureblobstorageoniotedge**.<br /><br />Det är viktigt att använda ett gement namn för Azure-Blob Storage i IoT Edge-modulen. IoT Edge är Skift läges känslig när du refererar till moduler och Storage SDK-standardvärdet till gemener. |
   | Tillhandahålla Docker-avbildning för modulen | Ange avbildnings-URI: n: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code tar den information du har angett, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster. Lösningsmallen skapar en manifest Distributionsmall som innehåller din avbildning i blob storage-modulen, men du måste konfigurera alternativ för att skapa modulens.

1. Öppna *deployment.template.json* i din nya lösning arbetsyta och hitta den **moduler** avsnittet. Gör följande ändringar i konfigurationen:

   1. Ta bort **SimulatedTemperatureSensor** -modulen eftersom den inte behövs för den här distributionen.

   1. Kopiera och klistra in följande kod i `createOptions` fältet:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Uppdatera modul createOptions-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Konto namn ska vara 3 till 24 tecken långa, med gemener och siffror. Inga blank steg.

1. Ersätt `<your storage account key>` med en 64 byte-base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.

1. Ersätt `<storage mount>` enligt behållar operativ systemet. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data. Lagrings monteringen mappar en plats på enheten som du anger till en angiven plats i modulen.  

      
     - För Linux-behållare är  *\<formatet lagrings Sök väg eller volym >:/blobroot*. Exempel
         - Använd [volym montering](https://docs.docker.com/storage/volumes/): **min-volym:/blobroot** 
         - Använd [BIND Mount](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Se till att följa stegen för att [bevilja katalog åtkomst till behållar användaren](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - För Windows-behållare är  *\<formatet lagrings Sök väg eller volym >: C:/BlobRoot*. Exempel
         - Använd [volym montering](https://docs.docker.com/storage/volumes/): **min-volym: C:/blobroot**. 
         - Använd [BIND-montering](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData: C:/BlobRoot**.
         - I stället för att använda din lokala enhet kan du mappa din SMB-nätverks plats. mer information finns i [använda SMB-resurs som lokal lagring](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ändra inte den andra halvan av lagrings montering svärdet, som pekar på en angiven plats i modulen. Lagrings monteringen ska alltid avslutas med **:/blobroot** för Linux-behållare och **: C:/blobroot** för Windows-behållare.

1. Konfigurera [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) och [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) för modulen genom att lägga till följande JSON i filen *Deployment. template. JSON* . Konfigurera varje egenskap med ett lämpligt värde och spara filen.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![Ange önskade egenskaper för azureblobstorageoniotedge-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Information om hur du konfigurerar deviceToCloudUploadProperties och deviceAutoDeleteProperties efter att modulen har distribuerats finns i [Redigera modulens dubbla](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Mer information om alternativen för att skapa behållare, omstart av princip och önskad status finns i [EdgeAgent önskade egenskaper](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Spara filen *deployment.template.json*.

1. Högerklicka på **deployment.template.json** och välj **generera IoT Edge-distribution manifest**.

1. Visual Studio Code tar den information som du angav i *Deployment. template. JSON* och använder den för att skapa en ny distributions manifest fil. Distribution av manifestet skapas i en ny **config** mapp i din lösning arbetsyta. När du har filen kan du följa stegen i [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) eller [distribuera Azure IoT Edge-moduler med Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Distribuera flera module-instanser

Om du vill distribuera flera instanser av Azure-Blob Storage i IoT Edge-modulen måste du ange en annan lagrings Sök väg och ändra `HostPort` värdet som modulen binder till. Blob storage-moduler exponera alltid port 11002 i behållaren, men du kan deklarera vilken port som den är bunden till på värden.

Redigera **behållar skapande alternativ** (i Azure Portal) eller fältet **createOptions** (i filen *Deployment. template. JSON* i Visual Studio `HostPort` Code) för att ändra värdet:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ändra slutpunkten så att den pekar på den uppdaterade värdport när du ansluter till ytterligare blob storage-moduler.

## <a name="next-steps"></a>Nästa steg
Läs mer om [Azure Blob Storage på IoT Edge](how-to-store-data-blob.md)

Mer information om hur distribution manifest fungerar och hur du skapar dem finns i [förstå hur IoT Edge-moduler kan användas, konfigurerats och återanvändas](module-composition.md).
