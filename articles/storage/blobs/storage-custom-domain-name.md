---
title: Konfigurera ett anpassat domännamn för din Azure Blob storage-slutpunkt | Microsoft Docs
description: Använd Azure-portalen för att mappa dina egna kanoniskt namn (CNAME) till slutpunkt för Blob-lagring i Azure Storage-konto.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: 2b776e8f40f6972a60f933b0104312b119439f38
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
ms.locfileid: "29559935"
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Konfigurera ett eget domännamn för din Blob Storage-slutpunkt

Du kan konfigurera en anpassad domän för åtkomst till blob-data i Azure storage-konto. Standardslutpunkt för Blob storage är `<storage-account-name>.blob.core.windows.net`. Om du mappar en anpassade domäner och underdomäner som **www.contoso.com** till blob-slutpunkten för ditt lagringsutrymme konto användare kan sedan åtkomst till blob-data i ditt lagringskonto med domänen.

> [!IMPORTANT]
> Azure Storage har inte har inbyggt stöd HTTPS med anpassade domäner. Du kan för närvarande [använder Azure CDN för åtkomst till blobbar med anpassade domäner via HTTPS](storage-https-custom-domain-cdn.md).
>

I följande tabell visas några exempel-URL: er för blob-data som finns i ett lagringskonto med namnet **mittlagringskonto**. Den anpassade domänen som registrerats för lagringskontot är **www.contoso.com**:

| Resurstyp | Standard-URL | URL: en anpassad domän |
| --- | --- | --- |
| Lagringskonto | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Rotbehållare | http://mystorageaccount.BLOB.Core.Windows.NET/myblob eller http://mystorageaccount.blob.core.windows.net/$ root/minblobb| http://www.contoso.com/myblob eller http://www.contoso.com/$ root/minblobb |

## <a name="direct-vs-intermediary-domain-mapping"></a>Direkt kontra mellanliggande domänmappning

Det finns två sätt att peka domänen till blob-slutpunkten för ditt lagringskonto: Dirigera CNAME mappning och använder den *asverify* mellanliggande underdomänen.

### <a name="direct-cname-mapping"></a>Direkt CNAME-mappning

Den första och enklaste metoden är att skapa en kanoniskt namn (CNAME)-post som mappar dina anpassade domäner och underdomäner direkt till-slutpunkt för blob. En CNAME-post är en domain name system (DNS) funktion som mappar ett källdomänen till en mål-domän. I det här fallet källdomänen är din egen anpassade domäner och underdomäner, till exempel *www.contoso.com*. Måldomänen är Blobbtjänstens slutpunkt, till exempel *mystorageaccount.blob.core.windows.net*.

Den direkta metoden beskrivs i [registrera en anpassad domän](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Mellanliggande mappning med *asverify*

Den andra metoden använder också CNAME-poster, men först använder en särskild underdomän som identifieras av Azure för att undvika driftsavbrott: **asverify**.

Processen för att matcha den anpassade domänen till en blob-slutpunkten kan resultera i en kort period driftstopp för domänen när du registrerar den i den [Azure-portalen](https://portal.azure.com). Om den anpassade domänen för närvarande stöder ett program med ett servicenivåavtal (SLA) som kräver driftstopp, så du kan använda Azure *asverify* underdomän som en mellanliggande registreringssteget. Detta mellanliggande steg gör att användarna ska kunna komma åt din domän när DNS-mappning äger rum.

Metoden mellanliggande beskrivs i [registrera en anpassad domän med hjälp av den *asverify* underdomän](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrera en anpassad domän
Använd den här proceduren för att registrera den anpassade domänen om du har några frågor om domänen är en kort tillgängligt för att användarna, eller om den anpassade domänen inte är för närvarande är värd för ett program. Du kan använda Azure DNS för att konfigurera en anpassad DNS-namn för din Azure Blob store. Mer information finns i [Använd Azure DNS för att ange inställningar för anpassad domän för en Azure-tjänst](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Om den anpassade domänen för närvarande stöder ett program som inte kan ha någon avbrottstid, följer du proceduren som beskrivs i [registrera en anpassad domän med hjälp av den *asverify* underdomän](#register-a-custom-domain-using-the-asverify-subdomain).

Om du vill konfigurera ett anpassat domännamn måste du skapa en ny CNAME-post i DNS. CNAME-post anger ett alias för ett domännamn. I det här fallet mappar adressen till den anpassade domänen till slutpunkt för Blob-lagring för ditt lagringskonto.

Normalt kan du hantera din domän DNS-inställningar på din domänregistrator webbplats. Varje register har en liknande metod för att ange en CNAME-post, men konceptet är detsamma. Vissa paket för registrering av grundläggande domän erbjuder inte DNS-konfiguration, så du kan behöva uppgradera din domän registreringspaket innan du kan skapa CNAME-post.

1. Navigera till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Under **BLOB-tjänsten** på bladet menyn väljer **anpassad domän** att öppna den *anpassad domän* bladet.
1. Logga in på webbplatsen för din domänregistrator och gå till sidan för hantering av DNS. Du kan hitta det här i ett avsnitt som till exempel **Domännamn**, **DNS**, eller **hantering av namnhantering**.
1. Leta reda på avsnittet för att hantera CNAME. Du kan behöva gå till en sida med avancerade inställningar och leta efter orden **CNAME**, **Alias**, eller **underdomäner**.
1. Skapa en ny CNAME-post och ange en underdomän alias som **www** eller **foton**. Ange ett värdnamn som är Blobbtjänstens slutpunkt, i formatet **mystorageaccount.blob.core.windows.net** (där *mittlagringskonto* är namnet på ditt lagringskonto). Värdnamnet som ska användas som visas i objektet #1 för den *anpassad domän* bladet i den [Azure-portalen](https://portal.azure.com).
1. I textrutan på den *anpassad domän* bladet i den [Azure-portalen](https://portal.azure.com), ange namnet på din domän, inklusive underdomänen. Om din domän är till exempel **contoso.com** och underdomän-alias är **www**, ange **www.contoso.com**. Om din underdomän **foton**, ange **photos.contoso.com**. Underdomänen är *krävs*.
1. Välj **spara** på den *anpassad domän* bladet för att registrera den anpassade domänen. Om registreringen lyckas visas en portalmeddelandet storage-konto har uppdaterats.

När din nya CNAME-posten har spridits via DNS kan användarna visa blob-data med hjälp av den anpassade domänen så länge som de har rätt behörigheter.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Registrera en anpassad domän med hjälp av den *asverify* underdomän
Använd den här proceduren för att registrera din anpassade domän om den anpassade domänen för närvarande stöder ett program med ett SLA som kräver att det finnas utan avbrott. Genom att skapa en CNAME-post som pekar från `asverify.<subdomain>.<customdomain>` till `asverify.<storageaccount>.blob.core.windows.net`, kan du registrera din domän med Azure. Du kan sedan skapa en andra CNAME-post som pekar från `<subdomain>.<customdomain>` till `<storageaccount>.blob.core.windows.net`, då trafik till den anpassade domänen dirigeras till blob-slutpunkten.

Den **asverify** underdomän är en särskild underdomän som identifieras av Azure. Av tillagt `asverify` till dina egna underdomän du bevilja Azure att identifiera din anpassade domän utan att ändra DNS-post för domänen. När du ändrar DNS-post för domänen, kommer att mappas till blob-slutpunkten utan avbrott.

1. Navigera till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Under **BLOB-tjänsten** på bladet menyn väljer **anpassad domän** att öppna den *anpassad domän* bladet.
1. Logga in på din DNS-leverantör webbplatsen och gå till sidan för hantering av DNS. Du kan hitta det här i ett avsnitt som till exempel **Domännamn**, **DNS**, eller **hantering av namnhantering**.
1. Leta reda på avsnittet för att hantera CNAME. Du kan behöva gå till en sida med avancerade inställningar och leta efter orden **CNAME**, **Alias**, eller **underdomäner**.
1. Skapa en ny CNAME-post och ange en underdomän alias som innehåller den *asverify* underdomänen. Till exempel **asverify.www** eller **asverify.photos**. Ange ett värdnamn som är Blobbtjänstens slutpunkt, i formatet **asverify.mystorageaccount.blob.core.windows.net** (där **mittlagringskonto** är namnet på ditt lagringskonto). Värdnamnet som ska användas som visas i objektet #2 i den *anpassad domän* bladet i den [Azure-portalen](https://portal.azure.com).
1. I textrutan på den *anpassad domän* bladet i den [Azure-portalen](https://portal.azure.com), ange namnet på din domän, inklusive underdomänen. Inkludera inte *asverify*. Om din domän är till exempel **contoso.com** och underdomän-alias är **www**, ange **www.contoso.com**. Om din underdomän **foton**, ange **photos.contoso.com**. Underdomänen krävs.
1. Välj den **Använd indirekt CNAME-validering** kryssrutan.
1. Välj **spara** på den *anpassad domän* bladet för att registrera den anpassade domänen. Om registreringen lyckas visas en portal meddelande om ditt lagringskonto har uppdaterats. Nu är din anpassade domän har verifierats av Azure, men trafik till din domän är ännu inte dirigeras till ditt lagringskonto.
1. Tillbaka till din DNS-leverantör webbplats och skapa en annan CNAME-post som mappar en underdomän till Blob-tjänstens slutpunkt. Till exempel ange underdomänen som **www** eller **foton** (utan den *asverify*), och värdnamn som **mystorageaccount.blob.core.windows.net** (där **mittlagringskonto** är namnet på ditt lagringskonto). Registreringen av den anpassade domänen är klar med det här steget.
1. Slutligen kan du ta bort CNAME-post som du skapade som innehåller den **asverify** underdomän som det var nödvändigt endast som en mellanliggande steg.

När din nya CNAME-posten har spridits via DNS kan användarna visa blob-data med hjälp av den anpassade domänen så länge som de har rätt behörigheter.

## <a name="test-your-custom-domain"></a>Testa den anpassade domänen

Skapa en blobb i en offentlig behållare inom ditt lagringskonto för att bekräfta din anpassade domän verkligen är mappad till Blob-tjänstens slutpunkt. Sedan, i en webbläsare, Använd en URI i följande format till blob:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Du kan till exempel använda följande URI: N att komma åt ett webbformulär i den **myforms** behållare i den **photos.contoso.com** anpassade underdomän:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Avregistrera en anpassad domän

Använd någon av följande procedurer för att avregistrera en anpassad domän för din slutpunkt för Blob-lagring.

### <a name="azure-portal"></a>Azure Portal

Utför följande i Azure portal för att ta bort inställningen för domänen:

1. Navigera till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Under **BLOB-tjänsten** på bladet menyn väljer **anpassad domän** att öppna den *anpassad domän* bladet.
1. Radera innehållet i textrutan som innehåller ditt domännamn.
1. Välj knappen **Spara**.

När den anpassade domänen har tagits bort, visas ett portal meddelande om ditt lagringskonto har uppdaterats.

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd den [az lagring uppdatering](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI kommando och ange en tom sträng (`""`) för den `--custom-domain` argumentvärdet att ta bort en anpassad domän för registrering.

* Kommandoformatet:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemplet:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Använd den [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) PowerShell-cmdlet och ange en tom sträng (`""`) för den `-CustomDomainName` argumentvärdet att ta bort en anpassad domän för registrering.

* Kommandoformatet:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplet:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Nästa steg
* [Mappa en anpassad domän till en slutpunkt i Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Använder Azure CDN åtkomst till blobbar med anpassade domäner via HTTPS](storage-https-custom-domain-cdn.md)
