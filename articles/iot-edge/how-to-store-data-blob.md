---
title: Store blockblob-objekt på enheter – Azure IoT Edge | Microsoft Docs
description: Distribuera en Azure Blob Storage-modulen till IoT Edge-enhet för att lagra data på gränsen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0fc34c913453abd174009213233a54e30b9346d3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881392"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store data på gränsen med Azure Blob Storage på IoT Edge (förhandsversion)

Azure Blob Storage på IoT Edge innehåller en [blockblob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) lagringslösning kant. Ett blob storage-modulen på din IoT Edge-enhet som fungerar som en tjänst för Azure block blob, men blockblob-objekt lagras lokalt på din IoT Edge-enhet. Du kan få åtkomst till dina blobar på samma sätt i Azure storage SDK eller blockera blob API-anrop som du redan är van att. 

Den här modulen som levereras med **automatisk lagringsnivåer** och **automatisk förfallodatum** funktioner.

> [!NOTE]
> För närvarande automatiskt lagringsnivåer och automatisk förfallodatum funktioner är endast tillgängliga i Linux AMD64- och Linux ARM32.

**Automatisk lagringsnivåer** är en konfigurerbar funktioner, där du kan automatiskt ladda upp data från din lokala blob storage till Azure med stöd för tillfälliga internet-anslutning. På så sätt kan du:
- Aktivera på/av funktionen lagringsnivåer
- Välj den ordning i vilken data ska kopieras till Azure som NewestFirst eller OldestFirst
- Ange Azure Storage-konto som du vill att dina data som överförs.
- Ange de behållare som du vill överföra till Azure. Den här modulen kan du ange behållarnamn för både källa och mål.
- Hela nivåindelning (med hjälp av `Put Blob` åtgärden) och blockera nivå lagringsnivåer (med hjälp av `Put Block` och `Put Block List` operations).

Den här modulen använder block på lagringsnivåer, när din blob består av block. Här följer några vanliga scenarier:
- Programmet uppdateras vissa block med en tidigare överförda blob, den här modulen Överför bara uppdaterade block och inte hela blob.
- Modulen överför blob och internet-anslutning är borta, när anslutningen är tillbaka igen det överför endast de återstående block och inte hela blob.

Om en oväntat avslut (till exempel strömavbrott) inträffar under en blob-överföringen kan kommer alla block som var dags att överföringen att överföras igen, när modulen är online igen.

**Automatisk förfallodatum** är en konfigurerbar funktionalitet där den här modulen tar automatiskt bort dina blobar från den lokala lagringen när Time to Live (TTL) upphör att gälla. Det anges i minuter. På så sätt kan du:
- Aktivera på/av funktionen för automatisk förfallodatum
- Ange TTL-värdet i minuter

Scenarier där data som videor, bilder, ekonomi, sjukhus data eller data som behöver lagras lokalt, senare som kan bearbetas lokalt eller överförs till molnet är bra exempel att använda den här modulen.

Den här artikeln innehåller instruktioner för att distribuera en Azure Blob Storage på IoT Edge-behållaren som kör en blob-tjänst på din IoT Edge-enhet. 

>[!NOTE]
>Azure Blob Storage på IoT Edge är i [förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Titta på videon och få snabb introduktion
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

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

2. Välj vilken IoT Edge-enhet som ska ta emot modulen. På sidan **Målenheter för IoT Edge-enheter** anger du följande information:

   1. Välj den **prenumeration** som innehåller IoT-hubben som du använder.

   2. Välj din **IoT-hubb**.

   3. Om du vet att din **IoT Edge-enhetsnamn**, ange som i textrutan. Eller välj **Hitta enhet** för att välja från en lista över IoT Edge-enheter i IoT-hubben. 
   
   4. Välj **Skapa**.

   När du har valt en IoT Edge-modul från Azure Marketplace, samt valt den IoT Edge-enhet som ska ta emot modulen, kommer du till en trestegsguide som hjälper dig att definiera exakt hur modulen ska distribueras.

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

   4. Ange [automatisk lagringsnivåer och automatisk förfallodatum](#configure-auto-tiering-and-auto-expiration-via-azure-portal) i önskade egenskaper. Lista över [automatisk lagringsnivåer](#auto-tiering-properties) och [automatisk förfallodatum](#auto-expiration-properties) egenskaper och deras möjliga värden. 

   5. Välj **Spara**. 

4. Välj **Nästa** för att fortsätta till nästa steg i guiden.
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

5. Konfigurera [automatisk lagringsnivåer och automatisk förfallodatum](#configure-auto-tiering-and-auto-expiration-via-vscode). Lista över [automatisk lagringsnivåer](#auto-tiering-properties) och [automatisk förfallodatum](#auto-expiration-properties) egenskaper

6. Spara filen **deployment.template.json**.

7. Öppna den **.env** fil i din lösning arbetsyta. 

8. .Env-fil är konfigurerade att ta emot autentiseringsuppgifter för container-registret, men du behöver inte som för blob storage-avbildningen eftersom den är allmänt tillgängliga. I stället ersätta filen med två nya miljövariabler: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Ange ett värde för `STORAGE_ACCOUNT_NAME`, kontonamn ska vara tre till 24 tecken långt, med gemena bokstäver och siffror. Ange en 64-bytes base64-nyckeln för den `STORAGE_ACCOUNT_KEY`. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler. 

   Omfattar inte blanksteg eller citattecken runt de värden som du anger. 

10. Spara **.env**-filen. 

11. Högerklicka på **deployment.template.json** och välj **generera IoT Edge-distribution manifest**. 

12. Visual Studio Code tar den information som du erbjuds på deployment.template.json och .env och används för att skapa en ny distribution manifestfil. Distribution av manifestet skapas i en ny **config** mapp i din lösning arbetsyta. När du har filen kan du följa stegen i [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) eller [distribuera Azure IoT Edge-moduler med Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>Automatisk lagringsnivåer och egenskaper för automatisk upphörande och konfiguration

Använd önskade egenskaper för att ställa in automatisk lagringsnivåer och egenskaper för automatisk upphörande. De kan ange under distributionen eller ändras senare genom att redigera modultvilling utan att behöva distribuera om. Vi rekommenderar att du kontrollerar ”Modultvilling” för `reported configuration` och `configurationValidation` att kontrollera att värden korrekt har spridits.

### <a name="auto-tiering-properties"></a>Egenskaper för automatisk lagringsnivåer 
Namnet på den här inställningen är `tieringSettings`

| Fält | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| tieringOn | SANT, FALSKT | Som standard anges till `false`, om du vill aktivera det på ställas `true`|
| backlogPolicy | NewestFirst OldestFirst | Kan du välja den ordning i vilken data ska kopieras till Azure. Som standard anges till `OldestFirst`. Ordningen som bestäms av tid för senaste ändring av Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` en anslutningssträng som låter dig ange Azure Storage-konto som du vill att dina data laddas upp. Ange `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Lägg till lämpliga EndpointSuffix Azure där data ska överföras, det varierar för Global Azure, Azure Government och Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Låter dig ange behållarnamn som du vill överföra till Azure. Den här modulen kan du ange behållarnamn för både källa och mål. Om du inte anger namnet på Målbehållaren, tilldelas den automatiskt behållarnamn som `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Du kan skapa mallen strängar för behållaren målnamn, Kolla in kolumnen möjliga värden. <br>* %h -> IoT Hub-namn (3 – 50 tecken). <br>* %d -> IoT-enhetens Id (1 till 129 tecken). <br>* %m -> Modulnamn (1 till 64 tecken). <br>* %c -> Källbehållarnamn (3 till 63 tecken). <br><br>Maxstorleken för behållarens namn är 63 tecken, samtidigt som automatiskt tilldelas namnet på Målbehållaren om storleken på behållare överskrider 63 tecken det beskära varje avsnitt (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) 15 tecken. |

### <a name="auto-expiration-properties"></a>Egenskaper för automatisk upphörande
Namnet på den här inställningen är `ttlSettings`

| Fält | Möjliga värden | Förklaring |
| ----- | ----- | ---- |
| ttlOn | SANT, FALSKT | Som standard anges till `false`, om du vill aktivera det på ställas `true`|
| timeToLiveInMinutes | `<minutes>` | Ange TTL-värdet i minuter. Modulen tas bort automatiskt dina blobar från lokal lagring när TTL upphör att gälla |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Konfigurera automatisk lagringsnivåer och automatisk förfallodatum via Azure portal

Ange önskade egenskaper för att aktivera automatisk lagringsnivåer och automatisk förfallodatum, kan du ange dessa värden:

- **Under den första distributionen**: Kopiera JSON i **önskade egenskaper för modultvilling Set** box. Konfigurera varje egenskap med lämpligt värde, spara den och fortsätta med distributionen.

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

  ![Ange egenskaper för automatisk lagringsnivåer och automatisk förfallodatum](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **När modulen har distribuerats via funktionen ”Modultvilling för identitet”**: Gå till ”identitet Modultvilling” för den här modulen, kopiera JSON under egenskaper som önskas, konfigurera varje egenskap med lämpligt värde och spara. I ”Modultvilling för identitet” Json se till att varje gång du lägger till eller uppdaterar någon önskad egenskap, den `reported configuration` avsnittet återspeglar ändringarna, och `configurationValidation` rapporteras slutförd för varje egenskap.

   ```json 
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

   ```

![tiering+ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Konfigurera automatisk lagringsnivåer och automatisk förfallodatum via VSCode

- **Under den första distributionen**: Lägg till den nedan JSON i din deployment.template.json att definiera de önskade egenskaperna för den här modulen. Konfigurera varje egenskap med lämpligt värde och spara den.

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

Här är ett exempel på önskade egenskaper för den här modulen: ![ange önskade egenskaper för azureblobstorageoniotedge - VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **När modulen har distribuerats via ”Modultvilling”**: [Redigera Modultvillingen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) av den här modulen kopiera JSON under egenskaper som önskas, konfigurera varje egenskap med lämpligt värde och spara. I ”Modultvilling” Json se till att varje gång du lägger till eller uppdaterar någon önskad egenskap, den `reported configuration` avsnittet återspeglar ändringarna, och `configurationValidation` rapporteras slutförd för varje egenskap.

   ```json 
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

   ```
  ## <a name="logs"></a>Logs

Följ anvisningarna för att [konfigurera ditt dockerloggar för IoT Edge-moduler](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till ditt blob storage-modulen

Du kan använda kontonamnet och nyckeln för att du har konfigurerat att få åtkomst till bloblagring på IoT Edge-enhet. 

Ange din IoT Edge-enhet som blob-slutpunkt för lagring av alla begäranden som du gör. Du kan [skapa en anslutningssträng för en slutpunkt för lagring av explicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av informationen i IoT Edge och kontonamnet som du har konfigurerat. 

1. För moduler som har distribuerats på samma edge-enheten där ”Azure Blob Storage på IoT Edge” körs, blob-slutpunkten är: `http://<module name>:11002/<account name>`. 
2. För moduler som har distribuerats på olika edge-enhet än edge-enhet där ”Azure Blob Storage på IoT Edge” körs, så beroende på din konfiguration av blob-slutpunkten är: `http://<device IP >:11002/<account name>` eller `http://<IoT Edge device hostname>:11002/<account name>` eller `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Distribuera flera instanser

Om du vill distribuera flera instanser av Azure Blob Storage på IoT Edge, måste du ange olika lagringssökväg och ändra HostPort som modulen Binder till. Blob storage-moduler exponera alltid port 11002 i behållaren, men du kan deklarera vilken port som den är bunden till på värden. 

Redigera modulen skapa alternativ för att ändra värdet för HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Ändra slutpunkten så att den pekar på den uppdaterade värdport när du ansluter till ytterligare blob storage-moduler. 

## <a name="try-it-out"></a>Prova det

### <a name="azure-blob-storage-quickstart-samples"></a>Azure snabbstarten om Blob Storage-exempel
Azure Blob Storage-dokumentationen innehåller snabbstarter som innehåller exempelkoden på flera språk. Du kan köra de här exemplen för att testa Azure Blob Storage på IoT Edge genom att ändra blob-slutpunkten så att den pekar till ditt blob storage-modulen. Följ stegen för att [ansluta till ditt blob storage-modulen](#connect-to-your-blob-storage-module)

Följande snabbstarter använder språk som stöds också av IoT Edge, så att du kan distribuera dem som IoT Edge-moduler tillsammans med blob storage-modulen:

* [NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Azure Lagringsutforskaren
Du kan även försöka [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) att ansluta till ditt konto för lokal lagring. Vi har försökt med [tidigare version 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0) i Azure Explorer.
> [!NOTE]
> Du kan stöta på fel när du utför stegen nedan, Ignorera och uppdatera. 

1. Ladda ned och installera Azure Storage Explorer
2. Ansluta till Azure Storage med hjälp av en anslutningssträng
3. Ange anslutningssträng: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Gå igenom stegen för att ansluta.
5. Skapa behållare i ditt konto för lokal lagring
6. Starta överföring av filer som blockblobar.
   > [!NOTE]
   > Avmarkera kryssrutan för att ladda upp dem som sidblobar. Den här modulen stöder inte sidblobar. Du får detta meddelande vid överföring av filer som .iso, .vhd, .vhdx eller stora filer.

7. Du kan välja att ansluta dina Azure storage-konton där du laddar upp data. Det ger dig en enda vy för dina lokala storage-konto och ett Azure storage-konto

## <a name="supported-storage-operations"></a>Stöds lagringsåtgärder

BLOB storage-moduler på IoT Edge använder samma Azure Storage SDK och stämmer överens med den 2017-04-17-versionen av Azure Storage-API för block blob-slutpunkter. Senare versioner är beroende av kundernas behov.

Inte alla åtgärder i Azure Blob Storage stöds av Azure Blob Storage på IoT Edge. I följande avsnitt visas åtgärderna som stöds respektive inte stöds.

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
* Hämta och ange behållar-ACL
* Ställ in metadata för behållaren

Stöds inte:
* Lånet behållare

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
* Placera block
* Använda och få Blockeringslista

Stöds inte:
* Placera block från URL

## <a name="feedback"></a>Feedback:
Din feedback är viktig för oss att göra den här modulen och dess funktioner praktiskt och enkelt att använda. Dela gärna din feedback och berätta för oss hur vi kan förbättra.

Du kan nå ut till oss på absiotfeedback@microsoft.com 

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

