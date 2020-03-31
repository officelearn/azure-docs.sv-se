---
title: Mappa en anpassad domän till en slutpunkt för Azure Blob Storage
titleSuffix: Azure Storage
description: Mappa en anpassad domän till en Blob Storage- eller webbslutpunkt i ett Azure-lagringskonto.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370482"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Mappa en anpassad domän till en slutpunkt för Azure Blob Storage

Du kan mappa en anpassad domän till en blob-tjänstslutpunkt eller en [statisk webbplatsslutpunkt.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Den här mappningen fungerar endast för `www.contoso.com`underdomäner (till exempel: ). Om du vill att webbslutpunkten ska vara tillgänglig `contoso.com`på rotdomänen (till exempel: ) måste du använda Azure CDN. Vägledning finns i [mappningen av en anpassad domän med avsnittet HTTPS-aktiverad](#enable-https) i den här artikeln. Eftersom du går till den delen av den här artikeln för att aktivera rotdomänen för din anpassade domän är steget i det avsnittet för att aktivera HTTPS valfritt. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Mappa en anpassad domän med endast HTTP aktiverat

Den här metoden är enklare, men möjliggör endast HTTP-åtkomst. Om lagringskontot är konfigurerat för att [kräva säker överföring](../common/storage-require-secure-transfer.md) via HTTPS måste du aktivera HTTPS-åtkomst för din anpassade domän. 

Information om hur du aktiverar HTTPS-åtkomst finns i [mappningen av en anpassad domän med avsnittet HTTPS-aktiverad](#enable-https) i den här artikeln. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Mappa en anpassad domän

> [!IMPORTANT]
> Din anpassade domän kommer att vara kort otillgänglig för användare när du slutför konfigurationen. Om din domän för närvarande stöder ett program med ett servicenivåavtal (SLA) som kräver noll driftstopp följer du stegen i [mappningen av en anpassad domän med noll driftstopp](#zero-down-time) i den här artikeln för att säkerställa att användarna kan komma åt din domän medan DNS-mappningen sker.

Om du inte är oroad över att domänen inte är tillgänglig för användarna gör du så här.

:heavy_check_mark: Steg 1: Hämta värdnamnet för lagringsslutpunkten.

:heavy_check_mark: Steg 2: Skapa en kanonisk namn (CNAME) post med din domänleverantör.

:heavy_check_mark: Steg 3: Registrera den anpassade domänen med Azure. 

: heavy_check_mark: Steg 4: Testa din anpassade domän.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Steg 1: Hämta värdnamnet för lagringsslutpunkten 

Värdnamnet är url:en för lagringsslutpunkt utan protokollidentifieraren och det efterföljande snedstrecket. 

1. Gå till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)

2. Välj **Egenskaper**under **Inställningar**i menyfönstret .  

3. Kopiera värdet för **slutpunkten för primär blob-tjänst** eller den **primära statiska webbplatsslutpunkten** till en textfil. 

4. Ta bort protokollidentifieraren (*t.ex.* https) och det efterföljande snedstrecket från strängen. Följande tabell innehåller exempel.

   | Typ av slutpunkt |  slutpunkt | värdnamn |
   |------------|-----------------|-------------------|
   |blob-tjänst  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statisk webbplats  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Avsätt det här värdet åt sidan för senare.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Steg 2: Skapa en CNAME-post (Canonical Name) med domänleverantören

Skapa en CNAME-post för att peka på värdnamnet. En CNAME-post är en typ av DNS-post som mappar ett källdomännamn till ett måldomännamn.

1. Logga in på domänregistratorerns webbplats och gå sedan till sidan för att hantera DNS-inställningen.

   Sidan kan hittas i ett avsnitt med namnet **Domännamn,** **DNS**eller **Name Server Management**.

2. Leta reda på avsnittet för hantering av CNAME-poster. 

   Du kanske måste gå till en avancerad inställningssida och leta efter **CNAME,** **Alias**eller **Underdomäner**.

3. Skapa en CNAME-post. Som en del av den posten anger du följande objekt: 

   - Underdomänaliaset, `www` till `photos`exempel eller . Underdomänen krävs, rotdomäner stöds inte. 
      
   - Värdnamnet som du fick i avsnittet [Hämta värdnamnet för lagringsslutpunkten](#endpoint) tidigare i den här artikeln. 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Steg 3: Registrera din anpassade domän med Azure

1. Gå till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)

2. Välj **Anpassad domän**under **Blob-tjänst**i menyfönstret.  

   ![anpassat domänalternativ](./media/storage-custom-domain-name/custom-domain-button.png "anpassad domän")

   Fönstret **Anpassad domän** öppnas.

3. I textrutan **Domännamn** anger du namnet på din anpassade domän, inklusive underdomänen  
   
   Om domänen till exempel *är contoso.com* och underdomänaliaset `www.contoso.com`är *www*anger du . Om underdomänen *photos*är foton `photos.contoso.com`anger du .

4. Om du vill registrera den anpassade domänen väljer du knappen **Spara.**

   När CNAME-posten har spridits via DNS (Domain Name Servers), och om användarna har rätt behörighet kan de visa blob-data med hjälp av den anpassade domänen.

#### <a name="step-4-test-your-custom-domain"></a>Steg 4: Testa din anpassade domän

Skapa en blob i en offentlig behållare i ditt lagringskonto för att bekräfta att din anpassade domän är mappad till din blob-tjänstslutpunkt. Öppna sedan bloben i en webbläsare med hjälp av en URI i följande format:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Om du till exempel vill komma åt ett webbformulär i *my forms-behållaren* i *photos.contoso.com* anpassade underdomänen kan du använda följande URI:`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Mappa en anpassad domän med noll driftstopp

> [!NOTE]
> Om du inte är oroad över att domänen är kort otillgänglig för användarna bör du överväga att följa stegen i avsnittet [Mappa en anpassad domän](#map-a-domain) i den här artikeln. Det är ett enklare tillvägagångssätt med färre steg.  

Om din domän för närvarande stöder ett program med ett servicenivåavtal (SLA) som kräver noll driftstopp följer du dessa steg för att säkerställa att användarna kan komma åt din domän medan DNS-mappningen sker. 

:heavy_check_mark: Steg 1: Hämta värdnamnet för lagringsslutpunkten.

:heavy_check_mark: Steg 2: Skapa en cname-post (Intermediary Canonical Name) hos domänleverantören.

:heavy_check_mark: Steg 3: Förregistrera den anpassade domänen med Azure.

:heavy_check_mark: Steg 4: Skapa en CNAME-post med domänleverantören.

: heavy_check_mark: Steg 5: Testa din anpassade domän.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Steg 1: Hämta värdnamnet för lagringsslutpunkten 

Värdnamnet är url:en för lagringsslutpunkt utan protokollidentifieraren och det efterföljande snedstrecket. 

1. Gå till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)

2. Välj **Egenskaper**under **Inställningar**i menyfönstret .  

3. Kopiera värdet för **slutpunkten för primär blob-tjänst** eller den **primära statiska webbplatsslutpunkten** till en textfil. 

4. Ta bort protokollidentifieraren (*t.ex.* https) och det efterföljande snedstrecket från strängen. Följande tabell innehåller exempel.

   | Typ av slutpunkt |  slutpunkt | värdnamn |
   |------------|-----------------|-------------------|
   |blob-tjänst  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statisk webbplats  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Avsätt det här värdet åt sidan för senare.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Steg 2: Skapa en CNAME-post (Intermediary Canonical Name) hos domänleverantören

Skapa en tillfällig CNAME-post för att peka på värdnamnet. En CNAME-post är en typ av DNS-post som mappar ett källdomännamn till ett måldomännamn.

1. Logga in på domänregistratorerns webbplats och gå sedan till sidan för att hantera DNS-inställningen.

   Sidan kan hittas i ett avsnitt med namnet **Domännamn,** **DNS**eller **Name Server Management**.

2. Leta reda på avsnittet för hantering av CNAME-poster. 

   Du kanske måste gå till en avancerad inställningssida och leta efter **CNAME,** **Alias**eller **Underdomäner**.

3. Skapa en CNAME-post. Som en del av den posten anger du följande objekt: 

   - Underdomänaliaset, `www` till `photos`exempel eller . Underdomänen krävs, rotdomäner stöds inte.

     Lägg `asverify` till underdomänen i aliaset. Till `asverify.www` exempel: `asverify.photos`eller .
       
   - Värdnamnet som du fick i avsnittet [Hämta värdnamnet för lagringsslutpunkten](#endpoint) tidigare i den här artikeln. 

     Lägg till underdomänen `asverify` i värdnamnet. Till exempel: `asverify.mystorageaccount.blob.core.windows.net`.

4. Om du vill registrera den anpassade domänen väljer du knappen **Spara.**

   Om registreringen lyckas meddelar portalen dig att ditt lagringskonto har uppdaterats. Din anpassade domän har verifierats av Azure, men trafiken till din domän dirigeras ännu inte till ditt lagringskonto.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Steg 3: Förregistrera din anpassade domän med Azure

När du förregistrerar din anpassade domän med Azure tillåter du att Azure känner igen din anpassade domän utan att behöva ändra DNS-posten för domänen. På så sätt, när du ändrar DNS-posten för domänen, kommer den att mappas till blobslutpunkten utan driftstopp.

1. Gå till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)

2. Välj **Anpassad domän**under **Blob-tjänst**i menyfönstret.  

   ![anpassat domänalternativ](./media/storage-custom-domain-name/custom-domain-button.png "anpassad domän")

   Fönstret **Anpassad domän** öppnas.

3. I textrutan **Domännamn** anger du namnet på din anpassade domän, inklusive underdomänen  
   
   Om domänen till exempel *är contoso.com* och underdomänaliaset `www.contoso.com`är *www*anger du . Om underdomänen *photos*är foton `photos.contoso.com`anger du .

4. Markera kryssrutan **Använd indirekt CNAME-validering.**

5. Om du vill registrera den anpassade domänen väljer du knappen **Spara.**
  
   När CNAME-posten har spridits via DNS (Domain Name Servers), och om användarna har rätt behörighet kan de visa blob-data med hjälp av den anpassade domänen.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Steg 4: Skapa en CNAME-post med domänleverantören

Skapa en tillfällig CNAME-post för att peka på värdnamnet.

1. Logga in på domänregistratorerns webbplats och gå sedan till sidan för att hantera DNS-inställningen.

   Sidan kan hittas i ett avsnitt med namnet **Domännamn,** **DNS**eller **Name Server Management**.

2. Leta reda på avsnittet för hantering av CNAME-poster. 

   Du kanske måste gå till en avancerad inställningssida och leta efter **CNAME,** **Alias**eller **Underdomäner**.

3. Skapa en CNAME-post. Som en del av den posten anger du följande objekt: 

   - Underdomänaliaset, `www` till `photos`exempel eller . Underdomänen krävs, rotdomäner stöds inte.
      
   - Värdnamnet som du fick i avsnittet [Hämta värdnamnet för lagringsslutpunkten](#endpoint-2) tidigare i den här artikeln. 

#### <a name="step-5-test-your-custom-domain"></a>Steg 5: Testa din anpassade domän

Skapa en blob i en offentlig behållare i ditt lagringskonto för att bekräfta att din anpassade domän är mappad till din blob-tjänstslutpunkt. Öppna sedan bloben i en webbläsare med hjälp av en URI i följande format:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Om du till exempel vill komma åt ett webbformulär i *my forms-behållaren* i *photos.contoso.com* anpassade underdomänen kan du använda följande URI:`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Ta bort en anpassad domänmappning

Om du vill ta bort en anpassad domänmappning avregistrerar du den anpassade domänen. Använd någon av följande procedurer.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Så här tar du bort den anpassade domäninställningen:

1. Gå till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)

2. Välj **Anpassad domän**under **Blob-tjänst**i menyfönstret.  
   Fönstret **Anpassad domän** öppnas.

3. Ta bort innehållet i textrutan som innehåller ditt eget domännamn.

4. Välj knappen **Spara**.

När den anpassade domänen har tagits bort visas ett portalmeddelande om att ditt lagringskonto har uppdaterats

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort en anpassad domänregistrering använder du KOMMANDOT`""`AZ Storage `--custom-domain` [Account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI och anger sedan en tom sträng ( ) för argumentvärdet.

* Kommandoformat:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Kommandoexempel:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ta bort en anpassad domänregistrering använder du PowerShell-cmdleten`""` [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger sedan en tom sträng ( ) för `-CustomDomainName` argumentvärdet.

* Kommandoformat:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Kommandoexempel:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Mappa en anpassad domän med HTTPS aktiverat

Den här metoden innebär fler steg, men det möjliggör HTTPS-åtkomst. 

Om du inte behöver användare för att komma åt blob eller webbinnehåll med https läser du [avsnittet Mappa en anpassad domän med endast HTTP-aktiverat](#enable-http) avsnitt i den här artikeln. 

Så här mappar du en anpassad domän och aktiverar HTTPS-åtkomst:

1. Aktivera [Azure CDN](../../cdn/cdn-overview.md) på din blob eller webbslutpunkt. 

   En slutpunkt för Blob Storage finns i [Integrera ett Azure-lagringskonto med Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md). 

   En statisk webbplatsslutpunkt finns i [Integrera en statisk webbplats med Azure CDN](static-website-content-delivery-network.md).

2. [Mappa Azure CDN-innehåll till en anpassad domän](../../cdn/cdn-map-content-to-custom-domain.md).

3. [Aktivera HTTPS på en anpassad Azure CDN-domän](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > När du uppdaterar den statiska webbplatsen måste du rensa cachelagrat innehåll på CDN-edge-servrarna genom att rensa CDN-slutpunkten. Mer information finns i [Purge an Azure CDN endpoint](../../cdn/cdn-purge-endpoint.md) (Rensa en Azure CDN-slutpunkt).

4. (Valfritt) Läs igenom följande vägledning:

   * [SAS-token (Shared Access Signature) med Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [HTTP-till-HTTPS-omdirigering med Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Prissättning och fakturering när du använder Blob Storage med Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om statisk webbplatshosting i Azure Blob-lagring](storage-blob-static-website.md)
