---
title: Konfigurera Avere vFXT-lagring - Azure
description: Så här lägger du till ett backend-lagringssystem i din Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252615"
---
# <a name="configure-storage"></a>Konfigurera lagring

Det här steget konfigurerar ett backend-lagringssystem för ditt vFXT-kluster.

> [!TIP]
> Om du har skapat en ny Azure Blob-behållare tillsammans med Avere vFXT-klustret är den behållaren redan konfigurerad och klar att användas.

Följ dessa instruktioner om du inte har skapat en ny Blob-behållare med klustret, eller om du vill lägga till ytterligare ett maskinvaru- eller molnbaserat lagringssystem.

Det finns två huvuduppgifter:

1. [Skapa en kärnfiler](#create-a-core-filer), som ansluter ditt vFXT-kluster till ett befintligt lagringssystem eller en Azure Storage-kontobehållare.

1. [Skapa en namnområdeskorsning](#create-a-junction)som definierar sökvägen som klienterna ska montera.

I de här stegen används Kontrollpanelen av Avere. Läs [åtkomst till vFXT-klustret](avere-vfxt-cluster-gui.md) för att lära dig hur du använder det.

## <a name="create-a-core-filer"></a>Skapa en kärna filer

"Core filer" är en vFXT term för ett backend-lagringssystem. Lagringen kan vara en nas-maskinvaruapparat som NetApp eller Isilon, eller så kan det vara ett molnobjektbutik. Mer information om kärnfilers finns i [Avere-klusterinställningsguiden](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Om du vill lägga till en kärnfiler väljer du en av de två huvudtyperna av kärnfilers:

* [NAS core filer](#nas-core-filer) - beskriver hur du lägger till en NAS core filer
* [Azure Storage cloud core filer](#azure-blob-storage-cloud-core-filer) - beskriver hur du lägger till en Azure Blob-lagringsbehållare som en cloud core filer

### <a name="nas-core-filer"></a>NAS kärna filer

En NAS-kärnfiler kan vara en lokal NetApp- eller Isilon-apparat, eller en NAS-slutpunkt i molnet. Lagringssystemet måste ha en tillförlitlig höghastighetsanslutning till Avere vFXT-klustret - till exempel en 1GBps ExpressRoute-anslutning (inte en VPN) - och det måste ge klusterrotåtkomsten till NAS-exporten som används.

Följ dessa steg för att lägga till en NAS-kärnfiler:

1. Klicka på fliken **Inställningar** högst upp på Kontrollpanelen i Avere.

1. Klicka på **Core Filer** > **Hantera Core Filers** till vänster.

1. Klicka på **Skapa**.

   ![Skärmbild av sidan Lägg till nya filer med en markör över knappen Skapa](media/avere-vfxt-add-core-filer-start.png)

1. Fyll i den information som krävs i guiden:

   * Nämn din kärna filer.
   * Ange ett fullständigt kvalificerat domännamn (FQDN) om tillgängligt. Annars anger du en IP-adress eller ett värdnamn som löser till dina kärnfiler.
   * Välj din filer klass i listan. Om du är osäker väljer du **Annat**.

     ![Skärmbild av sidan Lägg till nya kärnfiler med filnamn för kärnfiler och dess fullständiga domännamn](media/avere-vfxt-add-core-filer.png)
  
   * Klicka på **Nästa** och välj en cacheprincip.
   * Klicka på **Lägg till filer**.
   * Mer detaljerad information finns i Lägga till [en ny NAS-kärnfiler](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) i Avere-klusterinställningsguiden.

Fortsätt sedan till [Skapa en korsning](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage moln kärna filer

Om du vill använda Azure Blob-lagring som din vFXT-klusters backend-lagring behöver du en tom behållare för att lägga till som en kärnfiler.

Genom att lägga till Blob-lagring i klustret krävs följande uppgifter:

* Skapa ett lagringskonto (steg 1 nedan)
* Skapa en tom Blob-behållare (steg 2-3)
* Lägg till lagringsåtkomstnyckeln som en molnautentiseringsuppgifter för vFXT-klustret (steg 4-6)
* Lägg till Blob-behållaren som en kärnfiler för vFXT-klustret (steg 7-9)
* Skapa en namnområdeskorsning som klienter använder för att montera kärnfilerna ([Skapa en korsning](#create-a-junction), samma för både maskinvara och molnlagring)

> [!TIP]
> Om du skapar en ny Blob-behållare när du skapar ett Avere vFXT för Azure-kluster konfigurerar distributionsmallen automatiskt behållaren som en kärnfiler. (Detta gäller även om du använder skriptet för att skapa, som är tillgängligt på begäran.) Du behöver inte konfigurera kärnfilerna efteråt.
>
> Verktyget för att skapa kluster utför dessa konfigurationsuppgifter åt dig:
>
> * Skapar en ny Blob-behållare i det angivna lagringskontot
> * Definierar behållaren som en kärnfiler
> * Skapar en namnområdeskorsning till behållaren
> * Skapar en slutpunkt för lagringstjänster i klustrets virtuella nätverk

Så här lägger du till Blob-lagring när du har skapat klustret.

1. Skapa ett V2-lagringskonto för allmänt ändamål med följande inställningar:

   * **Prenumeration** - samma som vFXT-klustret
   * **Resursgrupp** - samma som vFXT-klustergruppen (valfritt)
   * **Plats** - samma som vFXT-klustret
   * **Prestanda** - Standard (Premium-lagring stöds inte)
   * **Kontosort** - Allmänt V2 (StorageV2)
   * **Replikering** - Lokalt redundant lagring (LRS)
   * **Åtkomstnivå** – het
   * **Säker överföring krävs** - inaktivera det här alternativet (icke-standardvärde)
   * **Virtuella nätverk** - krävs inte

   Du kan använda Azure-portalen eller klicka på knappen "Distribuera till Azure" nedan.

   [![för att skapa lagringskonto](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. När kontot har skapats bläddrar du till lagringskontosidan.

   ![Nytt lagringskonto i Azure-portalen](media/avere-vfxt-new-storage-acct.png)

1. Skapa en ny Blob-behållare: Klicka på **Behållare** på översiktssidan och klicka sedan på **+Behållare**. Använd valfritt behållarnamn och se till att åtkomsten är inställd på **Privat**.

   ![Sidan Lagringsblobbar med knappen +behållar inringad och en ny behållare som skapas på en popup-sida](media/avere-vfxt-new-blob.png)

1. Hämta azure storage-kontonyckeln genom att klicka på **Åtkomstnycklar** under **Inställningar**. Kopiera en av nycklarna.

   ![Azure Portal GUI för kopiering av nyckeln](media/avere-vfxt-copy-storage-key.png)

1. Öppna Avere Kontrollpanelen för ditt kluster. Klicka på **Inställningar**och öppna**Autentiseringsuppgifter** för **Klustermoln** > i det vänstra navigeringsfönstret. Klicka på **Lägg till autentiseringsuppgifter**på sidan Molnreferenser .

   ![Klicka på knappen Lägg till autentiseringsuppgifter på konfigurationssidan för molnautentiseringsuppgifter](media/avere-vfxt-new-credential-button.png)

1. Fyll i följande information för att skapa en autentiseringsuppgifter för molnkärnfilerna:

   | Field | Värde |
   | --- | --- |
   | Namn på autentiseringsuppgifter | alla beskrivande namn |
   | Typ av tjänst | (välj Azure Storage-åtkomstnyckel) |
   | Klientorganisation | lagringskontots namn |
   | Prenumeration | subscription ID |
   | Åtkomstnyckel för lagring | Azure-lagringskontonyckel (kopierad i föregående steg) |

   Klicka på **Skicka**.

   ![Ifylld formulär för molnautentiseringsuppgifter på Kontrollpanelen i Avere](media/avere-vfxt-new-credential-submit.png)

1. Skapa sedan kärnfilerna. Klicka på **Core Filer** >  **Manage Core Filers**till vänster på Kontrollpanelen i Avere.

1. Klicka på knappen **Skapa** på sidan **Hantera filers för kärnfilers.**

1. Fyll i guiden:

   * Välj filer typ **Cloud**.
   * Namnge den nya kärnfilerna och klicka på **Nästa**.
   * Acceptera standardcacheprincipen och fortsätt till den tredje sidan.
   * I **Tjänsttyp**väljer du **Azure storage**.
   * Välj den autentiseringsdag som skapats tidigare.
   * Ange **att bucket-innehåll** ska **vara tomt**
   * Ändra **certifikatverifiering** till **Inaktiverad**
   * Ändra **komprimeringsläge** till **Ingen**
   * Klicka på **Nästa**.
   * På den fjärde sidan anger du namnet på behållaren i **Bucket-namnet** *som storage_account_name*/*container_name*.
   * Du kan också ange **krypteringstyp** till **Ingen**.  Azure Storage krypteras som standard.
   * Klicka på **Lägg till filer**.

   Mer detaljerad information finns i [Lägga till en ny cloud core filer](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) i Avere-klusterkonfigurationsguiden.

Sidan uppdateras eller så kan du uppdatera sidan för att visa dina nya kärnfiler.

Därefter måste du [skapa en korsning](#create-a-junction).

## <a name="create-a-junction"></a>Skapa en korsning

En korsning är en bana som du skapar för klienter. Kunderna monterar banan och anländer till den destination du väljer.

Du kan till `/vfxt/files` exempel skapa för att `/vol0/data` mappa `/project/resources` till din NetApp core filer export och underkatalogen.

Mer information om korsningar finns i [namnområdesavsnittet i Avere-klusterkonfigurationsguiden](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Följ dessa steg i Avere Kontrollpanelens gränssnitt:

* Klicka på > **VServer-namnområdet** längst upp till vänster. **VServer**
* Ange en namnområdessökväg som börjar ``/vfxt/data``med / (snedstreck), till exempel .
* Välj din kärna filer.
* Välj kärnfiler export.
* Klicka på **Nästa**.

  ![Skärmbild av sidan "Lägg till ny korsning" med fälten slutförda för korsning, kärnfiler och export](media/avere-vfxt-add-junction.png)

Korsningen visas efter några sekunder. Skapa ytterligare korsningar efter behov.

När korsningen har skapats använder klienter namnområdessökvägen för att komma åt filerna från lagringssystemet.

## <a name="next-steps"></a>Nästa steg

* [Montera Avere vFXT-klustret](avere-vfxt-mount-clients.md)
* Lär dig effektiva sätt att [flytta data till en ny Blob-behållare](avere-vfxt-data-ingest.md)
