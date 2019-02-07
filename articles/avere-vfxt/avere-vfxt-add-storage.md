---
title: Konfigurera Avere vFXT storage – Azure
description: Hur du lägger till en backend-lagringssystemet Avere-vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: procedural
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 13084ac21315d725df3f0913583fff3e64ee5c4a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813236"
---
# <a name="configure-storage"></a>Konfigurera lagring

Det här steget ställer in en backend-lagringssystemet för vFXT klustret.

> [!TIP]
> Om du har skapat en ny Azure Blob-behållare tillsammans med Avere vFXT klustret, att behållaren har redan ställts in för användning och du behöver inte lägga till lagring.

Följ dessa instruktioner om du inte har skapat en ny blobbehållare med ditt kluster, eller om du vill lägga till en ytterligare maskin- eller molnbaserade lagringssystem.

Det finns två huvudsakliga uppgifter:

1. [Skapa en grundläggande filer](#create-a-core-filer), som ansluter ditt vFXT-kluster till ett befintligt storage-system eller ett Azure Storage-konto.

1. [Skapa ett namnområde knutpunkt](#create-a-junction), som definierar den sökväg som klienter kan montera.

De här stegen används Avere på Kontrollpanelen. Läs [åtkomst till klustret vFXT](avere-vfxt-cluster-gui.md) att lära dig hur du använder den.

## <a name="create-a-core-filer"></a>Skapa en core-filer

”Core filer” är en vFXT term för ett backend-lagringssystem. Lagringen kan vara en maskinvara NAS-installation som NetApp eller Isilon eller det kan vara en objektlagring i molnet. Mer information om grundläggande filter kan hittas [kluster guiden inställningar i Avere](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Välj någon av de två viktigaste typerna av core filter för att lägga till en core-filer:

  * [NAS viktiga filer](#nas-core-filer) – beskriver hur du lägger till en NAS core-filer 
  * [Azure Storage molnet core filer](#azure-storage-cloud-core-filer) – beskriver hur du lägger till ett Azure Storage-konto som molnet core arkiverar

### <a name="nas-core-filer"></a>NAS core filer

En NAS core-filer kan vara en lokal NetApp eller Isilon eller en NAS-slutpunkt i molnet. Lagringssystemet måste ha en tillförlitlig höghastighetsanslutning till Avere vFXT kluster – till exempel en 1 Gbit/s ExpressRoute-anslutning (inte en VPN) - och den måste ge rotåtkomst kluster NAS-export som används.

Följande steg lägger du till en NAS core-filer:

1. I Kontrollpanelen Avere klickar du på den **inställningar** fliken högst upp.

1. Klicka på **Core Filer** > **hantera Core filter** till vänster.

1. Klicka på **Skapa**.

   ![Skärmbild av sidan Lägg till ny core filer med en markören över knappen Skapa](media/avere-vfxt-add-core-filer-start.png)

1. Fyll i informationen som krävs i guiden: 

   * Namnge din core-filer.
   * Ange ett fullständigt kvalificerat domännamn (FQDN) om tillgängligt. I annat fall ange en IP-adress eller värdnamn som motsvarar din core-filer.
   * Välj klassen filer i listan. Om du är osäker, Välj **andra**.

     ![Skärmbild av sidan Lägg till ny core filer med namnet på core-filer och dess fullständigt kvalificerade domännamn](media/avere-vfxt-add-core-filer.png)
  
   * Klicka på **nästa** och välja en cache-princip. 
   * Klicka på **lägga till Filer**.
   * Mer detaljerad information, referera till [att lägga till en ny NAS viktiga filer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) i Avere kluster inställningar guide.

Gå sedan vidare till [skapa knutpunkt](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Azure Storage molnet core filer

Om du vill använda Azure Blob storage som serverdelslagring för ditt vFXT kluster behöver du en tom behållare för att lägga till som en core-filer.

> [!TIP] 
> Om du vill skapa en blobbehållare samtidigt som du skapar klustret Avere vFXT Distributionsmall eller skript skapar en lagringsbehållare, definierar det som en core-filer och skapar namnområdet knutpunkt som en del av vFXT klustret skapas. 

Lägger till Blob-lagring i klustret kräver dessa uppgifter:

* Skapa ett lagringskonto (steg 1 nedan)
* Skapa en tom Blob-behållare (steg 2 – 3)
* Lägg till lagringsåtkomstnyckeln som en cloud-autentiseringsuppgift för vFXT klustret (steg 4 – 6)
* Lägg till Blob-behållaren som en core-filer för vFXT klustret (steg 7 – 9)
* Skapa ett namnområde knutpunkt som klienter använder för att montera core-filer ([skapa knutpunkt](#create-a-junction), samma för både maskin- och cloud storage)

Följ dessa steg för att lägga till Blob-lagring när du har skapat klustret. 

1. Skapa ett allmänt gpv2-lagringskonto med de här inställningarna:

   * **Prenumeration** – det är samma som vFXT-kluster
   * **Resursgrupp** – det är samma som vFXT klustergrupp (valfritt)
   * **Plats** – det är samma som vFXT-kluster
   * **Prestanda** - Standard (Premium storage stöds inte)
   * **Typ av konto** -General-purpose V2 (StorageV2)
   * **Replikering** – lokalt redundant lagring (LRS)
   * **Åtkomstnivå** – frekvent
   * **Säker överföring krävs** -inaktiverar det här alternativet (annat än standardvärdet)
   * **Virtuella nätverk** – krävs inte

   Du kan använda Azure-portalen eller klicka på knappen ”distribuera till Azure” nedan.

   [![för att skapa lagringskonto](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. När kontot har skapats kan du gå till sidan för lagringskontot.

   ![Nytt lagringskonto i Azure-portalen](media/avere-vfxt-new-storage-acct.png)

1. Skapa en blobbehållare genom att klicka på **Blobar** på översiktssidan och klicka sedan på **+ behållare**. Använda ett behållarnamn och kontrollera att har angetts till **privata**.

   ![Sidan för Storage-blobbar med inga befintliga behållare](media/avere-vfxt-blob-no-container.png)

1. Hämta nyckel för Azure Storage-konto genom att klicka på **åtkomstnycklar** under **inställningar**:

   ![Azure-portalen GUI för att kopiera nyckeln](media/avere-vfxt-copy-storage-key.png) 

1. Öppna Kontrollpanelen Avere för klustret. Klicka på **inställningar**, öppna sedan **kluster** > **Molnautentiseringsuppgifter** i det vänstra navigeringsfönstret. På sidan Molnautentiseringsuppgifter **Lägg till autentiseringsuppgift**.

   ![Klicka på knappen Lägg till autentiseringsuppgift på konfigurationssidan Molnautentiseringsuppgifter](media/avere-vfxt-new-credential-button.png)

1. Fyll i följande information för att skapa en autentiseringsuppgift för molnet core filer: 

   | Fält | Värde |
   | --- | --- |
   | Namn på autentiseringsuppgifter | ett beskrivande namn |
   | Typ av tjänst | (Välj åtkomstnyckel för Azure Storage) |
   | Klientorganisation | Lagringskontonamn |
   | Prenumeration | prenumerations-ID |
   | Lagringsåtkomstnyckel | Azure storage-kontonyckel (som du kopierade i föregående steg) | 

   Klicka på **Skicka**.

   ![Fyllt molnet credential formuläret Avere på Kontrollpanelen](media/avere-vfxt-new-credential-submit.png)

1. Därefter skapa core-filer. I vänster sida av Avere på Kontrollpanelen, klickar du på **Core Filer** >  **hantera Core filter**. 

1. Klicka på den **skapa** knappen på den **hantera Core filter** inställningssidan.

1. Fyll i guiden:

   * Välj typ av filer **molnet**. 
   * Namnge den nya core-filer och klicka på **nästa**.
   * Accepterar du standardprincipen för cache och fortsätta att den tredje sidan.
   * I **tjänstetypen**, Välj **Azure storage**. 
   * Välj den autentiseringsuppgift som har skapats tidigare.
   * Ange **Bucket innehållet** till **tom**
   * Ändra **certifikat verifiering** till **inaktiverad**
   * Ändra **komprimeringsläge** till **None**  
   * Klicka på **Nästa**.
   * På den fjärde sidan, anger du namnet på behållaren i **bucketnamnet** som *storage_account_name*/*container_name*.
   * Du kan också ange **krypteringstyp** till **ingen**.  Azure Storage krypteras som standard.
   * Klicka på **lägga till Filer**.

  Mer detaljerad information kan du läsa [att lägga till en ny cloud core filer](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) i konfigurationsguiden för Avere kluster. 

Sidan uppdateras och du kan uppdatera sidan om du vill visa din nya core-filer.

Sedan, måste du [skapa knutpunkt](#create-a-junction).

## <a name="create-a-junction"></a>Skapa en förgrening

Knutpunkt är en sökväg som du skapar för klienter. Klienter montera sökvägen och tas emot på det mål som du väljer.

Du kan till exempel skapa `/avere/files` att mappa till din NetApp core filer `/vol0/data` exportera och `/project/resources` underkatalog.

Mer information om vägkorsningar finns i den [namnområde delen av konfigurationsguiden för Avere kluster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Följ anvisningarna i gränssnittet Avere Kontrollpanelen inställningar:

* Klicka på **VServer** > **Namespace** i det övre vänstra hörnet.
* Ange ett namnområde sökväg som börjar med / (snedstreck), till exempel ``/avere/data``.
* Välj core-filer.
* Välj core filer exportera.
* Klicka på **Nästa**.

  ![Skärmbild av sidan ”Lägg till ny knutpunkt” med de fält som har slutförts för knutpunkt, core filer och export](media/avere-vfxt-add-junction.png)

Knutpunkten visas efter några sekunder. Skapa ytterligare vägkorsningar efter behov.

När du har skapat knutpunkten klienter kan [montera Avere vFXT klustret](avere-vfxt-mount-clients.md) att få åtkomst till filsystemet.
