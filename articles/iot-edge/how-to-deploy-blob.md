---
title: Distribuera blob-lagring på modul till din enhet - Azure IoT Edge
description: Distribuera en Azure Blob Storage-modul till din IoT Edge-enhet för att lagra data på gränsen.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 04b145622a1a4237b576a1bb512b5f749f9c3823
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133301"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Distribuera Azure Blob Storage i IoT Edge-modulen till din enhet

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar för Azure Blob Storage på IoT Edge-moduler. De två enklaste metoderna är att använda Azure-portalen eller Visual Studio-kodmallarna.

## <a name="prerequisites"></a>Krav

- En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i din Azure-prenumeration.
- En [IoT Edge-enhet](how-to-register-device.md) med IoT Edge-körningen installerad.
- [Visual Studio-kod](https://code.visualstudio.com/) och [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) om distribution från Visual Studio-kod.

## <a name="deploy-from-the-azure-portal"></a>Distribuera från Azure-portalen

Azure-portalen guidar dig genom att skapa ett distributionsmanifest och skicka distributionen till en IoT Edge-enhet.

### <a name="select-your-device"></a>Välj enhet

1. Logga in på [Azure-portalen](https://portal.azure.com) och navigera till din IoT-hubb.
1. Välj **IoT Edge** på menyn.
1. Klicka på målenhetens ID från listan över enheter.
1. Välj **Ange moduler**.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Azure-portalen har en guide som hjälper dig att skapa ett distributionsmanifest. Den har tre steg ordnade i flikar: **Moduler,** **Rutter**och **Granska + Skapa**.

#### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **IoT-kantmoduler** på sidan klickar du på listrutan **Lägg till** och väljer **IoT-edgemodul** för att visa sidan **Lägg till IoT-kantmodul.**

2. Ange ett namn på modulen på fliken **Modulinställningar** och ange sedan behållaravbildningen URI:

   Exempel:
  
   - **IoT Edge modul namn:**`azureblobstorageoniotedge`
   - **Bild URI:**`mcr.microsoft.com/azure-blob-storage:latest`

   ![Inställningar för modultvilling](./media/how-to-deploy-blob/addmodule-tab1.png)

   Välj inte **Lägg till** förrän du har angett värden på **flikarna Modulinställningar,** **Alternativ för Modulskapande**och **Inställningar för Modul Dubbla inställningar** enligt beskrivningen i den här proceduren.

   > [!IMPORTANT]
   > Azure IoT Edge är skiftlägeskänslig när du ringer samtal till moduler och Lagring SDK är också standard för gemener. Även om namnet på modulen på [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) är **AzureBlobStorageonIoTEdge,** hjälper ändring av namnet till gemener till att säkerställa att dina anslutningar till Azure Blob Storage på IoT Edge-modulen inte avbryts.

3. Öppna fliken **Alternativ för att skapa behållare.**

   ![Inställningar för modultvilling](./media/how-to-deploy-blob/addmodule-tab3.png)

   Kopiera och klistra in följande JSON i rutan för att tillhandahålla lagringskontoinformation och ett fäste för lagring på enheten.
  
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

4. Uppdatera JSON som du kopierade till **Alternativ för att skapa behållare** med följande information:

   - Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Kontonamnen ska vara 3 till 24 tecken långa, med gemener och siffror. Inga mellanslag.

   - Ersätt `<your storage account key>` med en 64-byte base64 nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64). Du använder dessa autentiseringsuppgifter för att komma åt blob-lagringen från andra moduler.

   - Byt ut `<storage mount>` enligt ditt behållaroperativsystem. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en befintlig katalog på din IoT Edge-enhet där blob-modulen ska lagra sina data. Lagringsfästet mappar en plats på enheten som du anger till en anställd plats i modulen.

     - För Linux-behållare är formatet * \<lagringssökväg eller volym>:/blobroot*. Exempel
         - använd [volymfäste:](https://docs.docker.com/storage/volumes/) **min volym:/blobroot**
         - använd [bindfäste:](https://docs.docker.com/storage/bind-mounts/) **/srv/containerdata:/blobroot**. Se till att följa stegen för att [bevilja katalogåtkomst till behållaranvändaren](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - För Windows-behållare är formatet * \<lagringssökväg eller volym>:C:/BlobRoot*. Exempel
         - använd [volymfäste:](https://docs.docker.com/storage/volumes/) **min volym:C:/blobroot**.
         - använd [bindfäste:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - I stället för att använda din lokala enhet kan du mappa din SMB-nätverksplats, för mer information, se [använda SMB-resurs som lokal lagring](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ändra inte den andra halvan av lagringsmonteringsvärdet, vilket pekar på en viss plats i modulen. Lagringsfästet ska alltid sluta med **:/blobroot** för Linux-behållare och **:C:/BlobRoot** för Windows-behållare.

5. Kopiera följande JSON på fliken **Modultvillinginställningar** och klistra in det i rutan.

   ![Inställningar för modultvilling](./media/how-to-deploy-blob/addmodule-tab4.png)

   Konfigurera varje egenskap med ett lämpligt värde, enligt platshållarnas anvisningar. Om du använder IoT Edge-simulatorn anger du värdena på relaterade miljövariabler för dessa egenskaper enligt [enhetenToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) och [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Information om hur du konfigurerar enhetenToCloudUploadProperties och deviceAutoDeleteProperties efter att modulen har distribuerats finns i [Redigera modultvillingen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Mer information om önskade egenskaper finns i [Definiera eller uppdatera önskade egenskaper](module-composition.md#define-or-update-desired-properties).

6. Välj **Lägg till**.

7. Välj **Nästa: Rutter** för att fortsätta till flödet.

#### <a name="specify-routes"></a>Ange rutter

Behåll standardvägarna och välj **Nästa: Granska + skapa** för att fortsätta till granskningsavsnittet.

#### <a name="review-deployment"></a>Granska distribution

Granskningsavsnittet visar det JSON-distributionsmanifest som skapades baserat på dina val i de två föregående avsnitten. Det finns också två moduler som deklarerats som du inte har lagt till: **$edgeAgent** och **$edgeHub**. Dessa två moduler utgör [IoT Edge-körningen](iot-edge-runtime.md) och krävs standardvärden i varje distribution.

Granska distributionsinformationen och välj sedan **Skapa**.

### <a name="verify-your-deployment"></a>Verifiera distributionen

När du har skapat distributionen går du tillbaka till **IoT Edge-sidan** i din IoT-hubb.

1. Välj den IoT Edge-enhet som du riktade in dig på med distributionen för att öppna dess information.
1. Kontrollera att blob-lagringsmodulen visas både **anges i distributionen** och Rapporteras **av enheten**i enhetsinformationen .

Det kan ta en stund innan modulen startas på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan för att se en uppdaterad status.

## <a name="deploy-from-visual-studio-code"></a>Distribuera från Visual Studio-kod

Azure IoT Edge tillhandahåller mallar i Visual Studio-kod som hjälper dig att utveckla kantlösningar. Följ följande steg för att skapa en ny IoT Edge-lösning med en blob-lagringsmodul och konfigurera distributionsmanifestet.

1. Välj **Visa** > **kommandopalett**.

1. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) på kommandopaletten.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

   Skapa lösningen genom att följ anvisningarna på kommandopaletten.

   | Field | Värde |
   | ----- | ----- |
   | Välj mapp | Välj plats på utvecklingsmaskinen för Visual Studio Code för att skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **befintlig modul (Ange url till full bild).** |
   | Ange ett modulnamn | Ange ett helt gementårsnamn för din modul, till exempel **azureblobstorageoniotedge**.<br/><br/>Det är viktigt att använda ett gemener namn för Azure Blob Storage på IoT Edge-modulen. IoT Edge är skiftlägeskänslig när man refererar till moduler och Storage SDK-standardvärdena. |
   | Ge Docker-avbildning för modulen | Ange bild-URI: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code tar den information du angav, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster. Lösningsmallen skapar en distributionsmanifestmall som innehåller avbildningen för blob-lagringsmodulen, men du måste konfigurera modulens skapa alternativ.

1. Öppna *deployment.template.json* på den nya **lösningsarbetsytan** och leta reda på avsnittet moduler. Gör följande konfigurationsändringar:

   1. Ta bort modulen **SimuleradTemperatureSensor** eftersom den inte är nödvändig för den här distributionen.

   1. Kopiera och klistra in `createOptions` följande kod i fältet:

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

      ![Uppdatera modul createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Kontonamnen ska vara 3 till 24 tecken långa, med gemener och siffror. Inga mellanslag.

1. Ersätt `<your storage account key>` med en 64-byte base64 nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64). Du använder dessa autentiseringsuppgifter för att komma åt blob-lagringen från andra moduler.

1. Byt ut `<storage mount>` enligt ditt behållaroperativsystem. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på IoT Edge-enheten där du vill att blob-modulen ska lagra dess data. Lagringsfästet mappar en plats på enheten som du anger till en anställd plats i modulen.  

     - För Linux-behållare är formatet * \<lagringssökväg eller volym>:/blobroot*. Exempel
         - använd [volymfäste:](https://docs.docker.com/storage/volumes/) **min volym:/blobroot**
         - använd [bindfäste:](https://docs.docker.com/storage/bind-mounts/) **/srv/containerdata:/blobroot**. Se till att följa stegen för att [bevilja katalogåtkomst till behållaranvändaren](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - För Windows-behållare är formatet * \<lagringssökväg eller volym>:C:/BlobRoot*. Exempel
         - använd [volymfäste:](https://docs.docker.com/storage/volumes/) **min volym:C:/blobroot**.
         - använd [bindfäste:](https://docs.docker.com/storage/bind-mounts/) **C:/ContainerData:C:/BlobRoot**.
         - I stället för att använda din lokala enhet kan du mappa din SMB-nätverksplats, för mer information se [använda SMB-resurs som lokal lagring](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Ändra inte den andra halvan av lagringsmonteringsvärdet, vilket pekar på en viss plats i modulen. Lagringsfästet ska alltid sluta med **:/blobroot** för Linux-behållare och **:C:/BlobRoot** för Windows-behållare.

1. Konfigurera [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) och [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) för din modul genom att lägga till följande JSON i *filen deployment.template.json.* Konfigurera varje egenskap med ett lämpligt värde och spara filen. Om du använder IoT Edge-simulatorn ställer du in värdena på relaterade miljövariabler för dessa egenskaper, som du hittar i förklaringsavsnittet av [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) och [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![ange önskade egenskaper för azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Information om hur du konfigurerar enhetenToCloudUploadProperties och deviceAutoDeleteProperties efter att modulen har distribuerats finns i [Redigera modultvillingen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Mer information om alternativ för att skapa behållare, starta om principen och önskad status finns i [Önskade egenskaper för EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Spara filen *deployment.template.json*.

1. Högerklicka på **deployment.template.json** och välj **Generera IoT Edge-distributionsmanifest**.

1. Visual Studio Code tar den information som du angav i *deployment.template.json* och använder den för att skapa en ny distributionsmanifestfil. Distributionsmanifestet skapas i en ny **konfigurationsmapp** på lösningsarbetsytan. När du har den filen kan du följa stegen i [Distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) eller Distribuera Azure [IoT Edge-moduler med Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Distribuera flera modulinstanser

Om du vill distribuera flera instanser av Azure Blob Storage på IoT Edge-modulen `HostPort` måste du ange en annan lagringssökväg och ändra värdet som modulen binder till. Blob-lagringsmodulerna exponerar alltid port 11002 i behållaren, men du kan deklarera vilken port den är bunden till på värden.

Redigera **alternativ för att skapa behållare** (i Azure-portalen) eller fältet **createOptions** (i filen `HostPort` *deployment.template.json* i Visual Studio Code) för att ändra värdet:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

När du ansluter till ytterligare blob-lagringsmoduler ändrar du slutpunkten så att den pekar på den uppdaterade värdporten.

## <a name="configure-proxy-support"></a>Konfigurera proxyinställningar

Om din organisation använder en proxyserver måste du konfigurera proxystöd för edgeAgent- och edgeHub-runtime-moduler. Den här processen omfattar två uppgifter:

- Konfigurera runtime-demonerna och IoT Edge-agenten på enheten.
- Ange HTTPS_PROXY miljövariabel för moduler i distributionsmanifestet JSON-filen.

Den här processen beskrivs i [Konfigurera en IoT Edge-enhet för att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

Dessutom kräver en blob-lagringsmodul också HTTPS_PROXY-inställningen i manifestdistributionsfilen. Du kan redigera distributionsmanifestfilen direkt eller använda Azure-portalen.

1. Navigera till din Iot-hubb i Azure-portalen och välj **Iot Edge** på menyn till den vänstra rutan.

1. Välj den enhet som modulen ska konfigureras med modulen.

1. Välj **Ange moduler**.

1. Välj blob-lagringsmodulen i avsnittet **IoT Edge Modules** på sidan.

1. På sidan **Uppdatera IoT Edge Module** väljer du fliken **Miljövariabler.**

1. Lägg `HTTPS_PROXY` till för **namnet** och proxy-URL:en för **värdet**.

      ![Ange HTTPS_PROXY miljövariabel](./media/how-to-deploy-blob/https-proxy-config.png)

1. Klicka på **Uppdatera**och sedan **granska + skapa**.

1. Observera att proxyn läggs till i modulen i distributionsmanifestet och väljer **Skapa**.

1. Verifiera inställningen genom att välja modulen på sidan enhetsinformation, och på den nedre delen av **informationssidan för IoT-kantmoduler** väljer du fliken **Miljövariabler.**

      ![Ange HTTPS_PROXY miljövariabel](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Blob Storage på IoT Edge](how-to-store-data-blob.md).

Mer information om hur distributionsmanifest fungerar och hur du skapar dem finns i [Förstå hur IoT Edge-moduler kan användas, konfigureras och återanvändas](module-composition.md).
