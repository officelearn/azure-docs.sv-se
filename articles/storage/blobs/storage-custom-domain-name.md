---
title: Konfigurera ett anpassat domännamn för ditt Azure Storage-konto | Microsoft Docs
description: Använda Azure-portalen för att mappa din egen kanoniskt namn (CNAME) till Blob- eller web-slutpunkt i ett Azure Storage-konto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: beacb39752b2af51aba4ddeb41c9bff68803df1b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954780"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurera ett anpassat domännamn för ditt Azure Storage-konto

Du kan konfigurera en anpassad domän för åtkomst till blobdata på ditt Azure storage-konto. Standardslutpunkt för Blob storage är `<storage-account-name>.blob.core.windows.net`. Du kan också använda web-slutpunkt som genereras som en del av den [serverstatiska webbplatser funktion (förhandsversion)](storage-blob-static-website.md). Om du mappar en anpassad domän och underdomän som **www.contoso.com** till blob eller web-slutpunkt för ditt lagringsutrymme konto användare kan sedan komma åt blobdata i ditt storage-konto med hjälp av domänen.

> [!IMPORTANT]
> Azure Storage har inte ännu har inbyggt stöd HTTPS med anpassade domäner. Du kan för närvarande [använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Storage-konton stöder för närvarande endast ett anpassat domännamn per konto. Det innebär att du inte kan mappa ett anpassat domännamn till både webb- och blob-Tjänsteslutpunkter.

I följande tabell visas några exempel-URL: er för blob-data som finns i ett lagringskonto med namnet **mystorageaccount**. Den anpassade domänen registrerad för lagringskontot är **www.contoso.com**:

| Resurstyp | Standardwebbadress | URL: en anpassad domän |
| --- | --- | --- | --- |
| Lagringskonto | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Rotbehållare | http://mystorageaccount.blob.core.windows.net/myblob eller http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob eller http://www.contoso.com/$root/myblob |
| Webb |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] eller http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] eller http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web eller http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web eller http://www.contoso.com/ eller http://www.contoso.com/$web / [indexdoc] eller http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> Alla exempel för Blob-tjänsteslutpunkt nedan gäller även för web service-slutpunkt.

## <a name="direct-vs-intermediary-domain-mapping"></a>Direct jämfört med mellanliggande domänmappning

Det finns två sätt att peka din anpassade domän på blob-slutpunkten för ditt lagringskonto: Dirigera CNAME-mappning, och använder den *asverify* mellanliggande underdomänen.

### <a name="direct-cname-mapping"></a>Direkt CNAME-mappning

Den första och enklaste metoden är att skapa en post för kanoniskt namn (CNAME) som matchar din anpassade domäner och underdomäner direkt till blob-slutpunkten. En CNAME-post är en domain name system (DNS)-funktion som mappar en källdomän till en måldomän. I det här fallet källdomänen är din egen anpassade domäner och underdomäner, till exempel *www.contoso.com*. Måldomänen är Blobbtjänstens slutpunkt, till exempel *mystorageaccount.blob.core.windows.net*.

Den direkta metoden beskrivs i [registrera en anpassad domän](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Mellanliggande mappning med *asverify*

Den andra metoden som också använder CNAME-poster, men först använder en särskild underdomän som identifieras av Azure för att undvika driftstopp: **asverify**.

Processen för att mappa din anpassade domän till en blobbslutpunkt kan resultera i en kort tids driftstopp medan du registrerar den i den [Azure-portalen](https://portal.azure.com). Om din anpassade domän stöder för närvarande ett program med ett servicenivåavtal (SLA) som kräver utan stilleståndstid så du kan använda Azure *asverify* underdomän som en mellanliggande registreringssteget. Detta mellanliggande steg gör att användarna ska kunna komma åt din domän under tiden DNS-mappningen utförs.

Den mellanliggande metoden beskrivs i [registrera en anpassad domän med hjälp av den *asverify* underdomän](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrera en anpassad domän
Använd den här proceduren för att registrera din anpassade domän om du har inga frågor om domänen som kort inte är tillgänglig för dina användare, eller om din anpassade domän inte är för närvarande som är värd för ett program. Du kan använda Azure DNS för att konfigurera ett anpassat DNS-namn för din Azure Blob-butik. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Om din anpassade domän för närvarande stöder ett program som inte kan ha några driftstopp, följer du proceduren som beskrivs i [registrera en anpassad domän med hjälp av den *asverify* underdomän](#register-a-custom-domain-using-the-asverify-subdomain).

Om du vill konfigurera ett anpassat domännamn, måste du skapa en ny CNAME-post i DNS. CNAME-post anger ett alias för ett domännamn. I det här fallet mappar den adressen för din anpassade domän till Blob storage-slutpunkt för ditt lagringskonto.

Du kan normalt hantera domänens DNS-inställningarna på din domänregistrators webbplats. Varje registrator har en liknande metod för att ange en CNAME-post, men konceptet är detsamma. Vissa paket för registrering av grundläggande domän erbjuder inte DNS-konfiguration, så du kan behöva uppgradera din domän registreringspaket innan du kan skapa CNAME-post.

1. Gå till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Under **BLOBTJÄNSTEN** på menyn-bladet och välj **Custom domain** att öppna den *Custom domain* bladet.
1. Logga in till din domänregistrators webbplats och gå till sidan för hantering av DNS. Du kan hitta det här i ett avsnitt som till exempel **Domännamn**, **DNS**, eller **hantering av namnhantering**.
1. Leta reda på avsnittet för att hantera CNAME. Du kan behöva gå till en sida med avancerade inställningar och leta efter orden **CNAME**, **Alias**, eller **underdomäner**.
1. Skapa en ny CNAME-post och ange en underdomän alias som **www** eller **foton**. Ange ett värdnamn, vilket är Blobbtjänstens slutpunkt, i formatet **mystorageaccount.blob.core.windows.net** (där *mystorageaccount* är namnet på ditt lagringskonto). Värdnamn för att använda visas i objektet #1 i den *Custom domain* -bladet i den [Azure-portalen](https://portal.azure.com).
1. I textrutan på den *Custom domain* -bladet i den [Azure-portalen](https://portal.azure.com), anger du namnet på din domän, inklusive underdomänen. Exempel: om din domän är **contoso.com** och ditt underdomän alias är **www**, ange **www.contoso.com**. Om din underdomän är **foton**, ange **photos.contoso.com**. Underdomänen är *krävs*.
1. Välj **spara** på den *Custom domain* bladet för att registrera din anpassade domän. Om registreringen lyckas visas ett Portalmeddelande om ditt storage-konto har uppdaterats.

När den nya CNAME-posten har spritts via DNS, kan användarna visa blob-data med hjälp av din anpassade domän, så länge som de har rätt behörighet.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Registrera en anpassad domän med hjälp av den *asverify* underdomän
Använd den här proceduren för att registrera din anpassade domän om din anpassade domän är för närvarande stöd för ett program med ett SLA som kräver att det finnas utan avbrott. Genom att skapa en CNAME-post som pekar från `asverify.<subdomain>.<customdomain>` till `asverify.<storageaccount>.blob.core.windows.net`, du kan föranmäla din domän med Azure. Du kan sedan skapa en andra CNAME-post som pekar från `<subdomain>.<customdomain>` till `<storageaccount>.blob.core.windows.net`, då trafik till din anpassade domän dirigeras till din blobbslutpunkt.

Den **asverify** underdomän är en särskild underdomän som identifieras av Azure. Genom att `asverify` till din egen underdomän du tillåta att Azure ska kunna identifiera din anpassade domän utan att ändra DNS-post för domänen. När du ändrar DNS-post för domänen, kommer den att mappas till blob-slutpunkt utan avbrott.

1. Gå till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Under **BLOBTJÄNSTEN** på menyn-bladet och välj **Custom domain** att öppna den *Custom domain* bladet.
1. Logga in till din DNS-leverantör webbplats och gå till sidan för hantering av DNS. Du kan hitta det här i ett avsnitt som till exempel **Domännamn**, **DNS**, eller **hantering av namnhantering**.
1. Leta reda på avsnittet för att hantera CNAME. Du kan behöva gå till en sida med avancerade inställningar och leta efter orden **CNAME**, **Alias**, eller **underdomäner**.
1. Skapa en ny CNAME-post och ange en underdomän alias som innehåller den *asverify* underdomänen. Till exempel **asverify.www** eller **asverify.photos**. Ange ett värdnamn, vilket är Blobbtjänstens slutpunkt, i formatet **asverify.mystorageaccount.blob.core.windows.net** (där **mystorageaccount** är namnet på ditt lagringskonto). Värdnamn för att använda visas i objektet #2 i den *Custom domain* -bladet i den [Azure-portalen](https://portal.azure.com).
1. I textrutan på den *Custom domain* -bladet i den [Azure-portalen](https://portal.azure.com), anger du namnet på din domän, inklusive underdomänen. Inkludera inte *asverify*. Exempel: om din domän är **contoso.com** och ditt underdomän alias är **www**, ange **www.contoso.com**. Om din underdomän är **foton**, ange **photos.contoso.com**. Underdomänen krävs.
1. Välj den **Använd indirekt CNAME-validering** kryssrutan.
1. Välj **spara** på den *Custom domain* bladet för att registrera din anpassade domän. Om registreringen lyckas visas en portal-meddelande om att ditt storage-konto har uppdaterats. Nu kan den anpassade domänen har verifierats av Azure, men trafik till din domän är ännu inte dirigeras till ditt lagringskonto.
1. Gå tillbaka till din DNS-leverantör webbplats och skapa en annan CNAME-post som mappar din underdomän till Blobbtjänstens slutpunkt. Till exempel ange underdomänen som **www** eller **foton** (utan den *asverify*), och värdnamn som **mystorageaccount.blob.core.windows.net**  (där **mystorageaccount** är namnet på ditt lagringskonto). Med det här steget har registreringen av din anpassade domän slutförts.
1. Slutligen kan du ta bort CNAME-post som du skapade som innehåller den **asverify** underdomän som det var nödvändigt endast som en mellanliggande steg.

När den nya CNAME-posten har spritts via DNS, kan användarna visa blob-data med hjälp av din anpassade domän, så länge som de har rätt behörighet.

## <a name="test-your-custom-domain"></a>Testa din anpassade domän

Skapa en blob i en offentlig behållare i ditt storage-konto för att bekräfta din anpassade domän mappas faktiskt till Blobbtjänstens slutpunkt. Sedan i en webbläsare, Använd en URI i formatet att få åtkomst till bloben:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Exempelvis kan du kan använda följande URI: N för att få åtkomst till ett webbformulär i den **myforms** behållaren i den **photos.contoso.com** anpassade underdomän:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Avregistrera en anpassad domän

Använd någon av följande procedurer för att avregistrera en anpassad domän för din Blob storage-slutpunkt.

### <a name="azure-portal"></a>Azure Portal

Utför följande i Azure portal för att ta bort inställningen för anpassad domän:

1. Gå till ditt lagringskonto i den [Azure-portalen](https://portal.azure.com).
1. Under **BLOBTJÄNSTEN** på menyn-bladet och välj **Custom domain** att öppna den *Custom domain* bladet.
1. Radera innehållet i textrutan som innehåller ditt domännamn.
1. Välj knappen **Spara**.

När den anpassade domänen har tagits bort, visas en portal-meddelande om att ditt storage-konto har uppdaterats.

### <a name="azure-cli"></a>Azure CLI

Använd den [az storage-konto update](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI kommandot och ange en tom sträng (`""`) för den `--custom-domain` argumentvärdet att ta bort en anpassad domän-registrering.

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

Använd den [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) PowerShell-cmdleten och ange en tom sträng (`""`) för den `-CustomDomainName` argumentvärdet att ta bort en anpassad domän-registrering.

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
* [Mappa en anpassad domän till en slutpunkt för Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Använda Azure CDN för att få åtkomst till blobar med anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Värd för statisk webbplats i Azure Blob Storage (förhandsversion)](storage-blob-static-website.md)
