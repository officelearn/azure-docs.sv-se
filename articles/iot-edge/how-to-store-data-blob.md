---
title: Azure Blob-lagring på Azure IoT Edge-enheter | Microsoft Docs
description: Distribuera en Azure Blob Storage-modulen till IoT Edge-enhet för att lagra data på gränsen.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9e48eba4b46f024b056fe53b3b3df24feec802e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995677"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store data på gränsen med Azure Blob Storage på IoT Edge (förhandsversion)

Azure BLOB-lagring på IoT Edge är en lösning block blob storage vid gränsen. Ett blob storage-modulen på din IoT Edge-enhet som fungerar som en Azure blob-tjänst, men blockblob-objekt lagras lokalt på din IoT Edge-enhet. Du kan få åtkomst till dina blobar på samma sätt i Azure storage SDK eller blockera blob API-anrop som du redan är van att. 

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

Azure Blob Storage på IoT Edge innehåller tre standard behållaravbildningar, två för Linux-behållare (AMD64 och ARM32 arkitekturer) och en för Windows-behållare (AMD64). När du använder en av avbildningarna modulen för att distribuera blob-lagring till din IoT Edge-enhet kan ange du tre uppgifter för att konfigurera modulinstans för din enhet:

* En **kontonamn** och **kontonyckel**. För att hålla sig konsekvent med Azure Storage, använder blob storage modulerna kontonamn och nycklar för att hantera åtkomst. Kontonamn ska vara tre till 24 tecken långt, med gemena bokstäver och siffror. Nycklar ska vara base64-kodad och 64 byte långt. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64).
* En **lokala lagringsalternativ**. Blob storage-modulen lagrar blobar lokalt på IoT Edge-enhet så att blobarna spara om modulen stoppar eller startar om. Deklarera en befintlig [volym](https://docs.docker.com/storage/volumes
) eller lokal mappsökväg där blobar ska lagras på din enhet. 

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar för Azure Blob Storage på IoT Edge-moduler. De två enklaste metoderna är att använda Azure-portalen eller Visual Studio Code-mallar. 

### <a name="azure-portal"></a>Azure Portal

Om du vill distribuera blob storage via Azure portal följer du stegen i [distribuera Azure IoT Edge-moduler från Azure portal](how-to-deploy-modules-portal.md). Innan du skapar gå att distribuera din modul, kopiera URI för avbildning och förbereda behållaren alternativ baserat på operativsystemet behållare. Använda värdena i den **konfigurera ett distribution-manifest** i artikeln för distribution. 

Ange URI för avbildning för blob storage-modulen: **mcr.microsoft.com/azure-blob-storage**. 
   
Använd följande JSON-mallen för den **behållare skapa alternativ** fält. Konfigurera JSON med ditt lagringskontonamn och lagringskontonyckel storage directory bindning.  
   
   ```json
   {
       "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
       ],
       "HostConfig":[
           "Binds":[
               "<storage directory bind>"
           ],
           "PortBindings":{
               "11002/tcp":[{"HostPort":"11002"}]
           }
       ]
   }
   ```   
   
I Skapa-alternativen JSON uppdaterar `\<your storage account name\>` med ett namn. Uppdatera `\<your storage account key\>` med en 64-bytes base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64) vilket gör att du kan välja din längd i byte. Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.

I Skapa-alternativen JSON uppdaterar `<storage directory bind>` beroende på operativsystemet för behållaren. Ange namnet på en [volym](https://docs.docker.com/storage/volumes/) eller den absoluta sökvägen till en katalog på din IoT Edge-enhet där du vill att blob-modulen för att lagra data.  

   * Linux-behållare:  **\<lagringssökväg >: / blobroot**. Till exempel/srv/containerdata: / blobroot. Eller, min volym: / blobroot. 
   * Windows-behållare:  **\<lagringssökväg >: C: / BlobRoot**. Till exempel C: / ContainerData:C: / BlobRoot. Eller, min-volymen: C: / blobroot. 


Du behöver inte ange autentiseringsuppgifter för registret för att komma åt Azure Blob Storage på IoT Edge och du behöver inte deklarera alla vägar för din distribution. 

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
   
   5. **Ange Docker-avbildning för modulen** -ange URI för avbildning: **mcr.microsoft.com/azure-blob-storage**

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

5. Spara **deployment.template.json**.

6. Öppna **.env** i lösningens arbetsyta. 

7. Du behöver inte ange alla registervärden för behållare för blob storage-avbildningen eftersom den är allmänt tillgängliga. Lägg till två nya miljövariabler i stället: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Ange ett namn för lagringskontonamn och tillhandahålla en 64-bytes base64-nyckel för lagringskontonyckeln. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler. 

9. Spara **.env**. 

10. Högerklicka på **deployment.template.json** och välj **generera IoT Edge-distribution manifest**. 

Visual Studio Code tar den information som du erbjuds på deployment.template.json och .env och används för att skapa en ny distribution manifestfil. Distribution av manifestet skapas i en ny **config** mapp i din lösning arbetsyta. När du har filen kan du följa stegen i [distribuera Azure IoT Edge-moduler från Visual Studio Code](how-to-deploy-modules-vscode.md) eller [distribuera Azure IoT Edge-moduler med Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Ansluta till ditt blob storage-modulen

Du kan använda kontonamnet och nyckeln för att du har konfigurerat att få åtkomst till bloblagring på IoT Edge-enhet. 

Ange din IoT Edge-enhet som blob-slutpunkt för lagring av alla begäranden som du gör. Du kan [skapa en anslutningssträng för en slutpunkt för lagring av explicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) med hjälp av informationen i IoT Edge och kontonamnet som du har konfigurerat. 

Blob-slutpunkten för Azure Blob Storage på IoT Edge är `http://<IoT Edge device hostname>:11002/<account name>`. 

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

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Stöds lagringsåtgärder

BLOB storage-moduler på IoT Edge använder samma Azure Storage SDK och stämmer överens med 2018-03-28-versionen av Azure Storage-API för block blob-slutpunkter. Senare versioner är beroende av kundernas behov. 

Inte alla åtgärder i Azure Blob Storage stöds av Azure Blob Storage på IoT Edge. Följande avsnitt visar vilka åtgärder som inte är en är stöds. 

### <a name="account"></a>Konto

Stöds: 
* Lista behållare

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
* Placera block
* Använda och få Blockeringslista

Stöds inte:
* Placera block från URL

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

