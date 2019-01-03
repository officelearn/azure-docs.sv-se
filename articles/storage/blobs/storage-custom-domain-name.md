---
title: Konfigurera ett anpassat domännamn för ditt Azure storage-konto | Microsoft Docs
description: Använda Azure-portalen för att mappa din egen kanoniskt namn (CNAME) till Blob-lagring eller web-slutpunkt i ett Azure storage-konto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 47ce2b39f1733c99ef8c15926d42aa62e1fcd44c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53634566"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurera ett anpassat domännamn för ditt Azure storage-konto

Du kan konfigurera en anpassad domän för åtkomst till blobdata på ditt Azure storage-konto. Standardslutpunkten för Azure Blob storage är  *\<lagringskontonamn->. blob.core.windows.net*. Du kan också använda webbslutpunkt som genereras som en del av den [serverstatiska webbplatser funktion (förhandsversion)](storage-blob-static-website.md). Om du mappar en anpassad domän och underdomänen, till exempel *www.contoso.com*, till blob- eller web-slutpunkt för ditt lagringskonto kan användarna använda den domänen få åtkomst till blob-data i ditt lagringskonto.

> [!IMPORTANT]
> Azure Storage har inte ännu har inbyggt stöd HTTPS med anpassade domäner. Du kan för närvarande [Använd Azure CDN för att få åtkomst till blobar med hjälp av anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md).
>

> [!NOTE]  
> Storage-konton stöder för närvarande endast ett anpassat domännamn per konto. Du kan mappa ett anpassat domännamn till både webb- och blob-Tjänsteslutpunkter.

I följande tabell visas några exempel-URL: er för blob-data som finns i ett lagringskonto med namnet *mystorageaccount*. Den anpassade domänen som har registrerats för lagringskontot är *www.contoso.com*:

| Resurstyp | Standardwebbadress | URL: en anpassad domän |
| --- | --- | --- | --- |
| Lagringskonto | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Rotbehållare | http://mystorageaccount.blob.core.windows.net/myblob eller http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob eller http://www.contoso.com/$root/myblob |
| Webb |  http://mystorageaccount. [zone].web.core.windows.net/$web/[indexdoc] eller http://mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] eller http://mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web eller http://mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http://www.contoso.com/$web eller http://www.contoso.com/ eller http://www.contoso.com/$web / [indexdoc] eller http://www.contoso.com/[indexdoc] |

> [!NOTE]  
> I följande avsnitt visas gäller även alla exempel för blob service-slutpunkt för web service-slutpunkt.

## <a name="direct-vs-intermediary-domain-mapping"></a>Direct jämfört med mellanliggande domänmappning

Du kan peka din anpassade domän till blob-slutpunkten för ditt lagringskonto på något av två sätt: 
* Använd direkt CNAME-mappning.
* Använd den *asverify* mellanliggande underdomänen.

### <a name="direct-cname-mapping"></a>Direkt CNAME-mappning

Den första och enklaste metoden är att skapa en post för kanoniskt namn (CNAME) som matchar din anpassade domäner och underdomäner direkt till blob-slutpunkten. En CNAME-post är en domain name system (DNS)-funktion som mappar en källdomän till en måldomän. I vårt exempel källdomänen använder din egen anpassade domäner och underdomäner (*www.contoso.com*, till exempel). Måldomänen är blobbtjänstens slutpunkt (*mystorageaccount.blob.core.windows.net*, till exempel).

Den direkta metoden beskrivs i avsnittet ”registrera en anpassad domän”.

### <a name="intermediary-mapping-with-asverify"></a>Mellanliggande mappning med *asverify*

Den andra metoden använder också CNAME-poster. Om du vill undvika avbrott, men den först använder en särskild underdomän *asverify* som känns igen av Azure.

Mappa din anpassade domän till en blobbslutpunkt kan orsaka en kort tids driftstopp medan du registrerar domänen i den [Azure-portalen](https://portal.azure.com). Om domänen har för närvarande stöd för ett program med ett servicenivåavtal (SLA) som kräver avbrottstid, använder du Azure *asverify* underdomän som en mellanliggande registreringssteget. Det här steget säkerställer att användare kan komma åt din domän under tiden DNS-mappningen utförs.

Den mellanliggande metoden beskrivs i [registrera en anpassad domän med hjälp av den *asverify* underdomän](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrera en anpassad domän
Registrera domänen med hjälp av proceduren i det här avsnittet om följande gäller:
* Du är unconcerned att domänen är kort inte är tillgänglig för dina användare.
* Den anpassade domänen är inte för närvarande som är värd för ett program. 

Du kan använda Azure DNS för att konfigurera ett anpassat DNS-namn för din Azure Blob-butik. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Om din anpassade domän stöder för närvarande ett program som inte kan ha några driftstopp, använder du proceduren i [registrera en anpassad domän med hjälp av den *asverify* underdomän](#register-a-custom-domain-using-the-asverify-subdomain).

Om du vill konfigurera ett anpassat domännamn, skapar du en ny CNAME-post i DNS. CNAME-post anger ett alias för ett domännamn. I vårt exempel mappar den adressen för din anpassade domän till Blob storage-slutpunkt för ditt lagringskonto.

Du kan vanligtvis hantera domänens DNS-inställningarna på din domänregistrators webbplats. Varje registrator har en liknande metod för att ange en CNAME-post, men konceptet är detsamma. Eftersom vissa paket för registrering av grundläggande domän inte erbjuder DNS-konfiguration, kan du behöva uppgradera din domän registreringspaket innan du kan skapa CNAME-post.

1. I den [Azure-portalen](https://portal.azure.com)går du till ditt lagringskonto.

1. I fönstret menyn under **Blobtjänsten**väljer **Custom domain**.  
   Den **Custom domain** öppnas fönstret.

1. Logga in till din domänregistrators webbplats och sedan gå till sidan för hantering av DNS.  
   Du kan hitta sidan i ett avsnitt som heter **domännamn**, **DNS**, eller **Namnserverhantering**.

1. Leta reda på avsnittet för att hantera CNAME.  
   Du kan behöva gå till en sida med avancerade inställningar och leta efter **CNAME**, **Alias**, eller **underdomäner**.

1. Skapa en ny CNAME-post, ange en underdomän alias som **www** eller **foton**, och sedan ange ett värdnamn.  
   Värdnamnet är blobbtjänstens slutpunkt. Dess format är  *\<mystorageaccount >. blob.core.windows.net*, där *mystorageaccount* är namnet på ditt lagringskonto. Värdnamn för att använda visas i objektet #1 i den **Custom domain** fönstret i den [Azure-portalen](https://portal.azure.com).

1. I den **Custom domain** fönstret i textrutan anger du namnet på din domän, inklusive underdomänen.  
   Exempel: om din domän är *contoso.com* och ditt underdomän alias är *www*, ange **www.contoso.com**. Om din underdomän är *foton*, ange **photos.contoso.com**.

1. Om du vill registrera din anpassade domän, Välj **spara**.  
   Om registreringen lyckas meddelar portalen dig att ditt storage-konto har uppdaterats.

När den nya CNAME-posten har spritts via DNS, om användarna har rätt behörigheter, kan de visa blob-data med hjälp av din anpassade domän.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registrera en anpassad domän med hjälp av den *asverify* underdomän
Om din anpassade domän stöder för närvarande ett program med ett SLA som måste det finnas utan avbrott, kan du registrera din anpassade domän med hjälp av proceduren i det här avsnittet. Genom att skapa en CNAME-post som pekar från *asverify.\< underdomän >. \<customdomain >* till *asverify.\< storageaccount >. blob.core.windows.net*, du kan föranmäla din domän med Azure. Du kan sedan skapa en andra CNAME-post som pekar från  *\<underdomän >.\< customdomain >* till  *\<storageaccount >. blob.core.windows.net*, och sedan trafiken till din egna domäns dirigeras till blob-slutpunkten.

Den *asverify* underdomän är en särskild underdomän som identifieras av Azure. Genom att *asverify* till din egen underdomän du tillåta att Azure ska kunna identifiera din anpassade domän utan att behöva ändra DNS-post för domänen. När du ändrar DNS-post för domänen, kommer den att mappas till blob-slutpunkt utan avbrott.

1. I den [Azure-portalen](https://portal.azure.com)går du till ditt lagringskonto.

1. I fönstret menyn under **Blobtjänsten**väljer **Custom domain**.  
   Den **Custom domain** öppnas fönstret.

1. Logga in till din DNS-leverantör webbplats och sedan gå till sidan för hantering av DNS.  
   Du kan hitta sidan i ett avsnitt som heter **domännamn**, **DNS**, eller **Namnserverhantering**.

1. Leta reda på avsnittet för att hantera CNAME.  
   Du kan behöva gå till en sida med avancerade inställningar och leta efter **CNAME**, **Alias**, eller **underdomäner**.

1. Skapa en ny CNAME-post, ange en underdomän alias som innehåller den *asverify* underdomänen, till exempel **asverify.www** eller **asverify.photos**, och sedan ange ett värdnamn.  
   Värdnamnet är blobbtjänstens slutpunkt. Dess format är *asverify.\< mystorageaccount >. blob.core.windows.net*, där *mystorageaccount* är namnet på ditt lagringskonto. Värdnamn för att använda visas i objektet #2 i den *Custom domain* fönstret i den [Azure-portalen](https://portal.azure.com).

1. I den **Custom domain** fönstret i textrutan anger du namnet på din domän, inklusive underdomänen.  
   Inkludera inte *asverify*. Exempel: om din domän är *contoso.com* och ditt underdomän alias är *www*, ange **www.contoso.com**. Om din underdomän är *foton*, ange **photos.contoso.com**.

1. Välj den **Använd indirekt CNAME-validering** markerar du kryssrutan.

1. Om du vill registrera din anpassade domän, Välj **spara**.  
   Om registreringen lyckas meddelar portalen dig att ditt storage-konto har uppdaterats. Den anpassade domänen har verifierats av Azure, men trafik till din domän är ännu inte dirigeras till ditt lagringskonto.

1. Gå tillbaka till din DNS-leverantör webbplats och skapa sedan en annan CNAME-post som mappar din underdomän till blobbtjänstens slutpunkt.  
   Till exempel ange underdomänen som *www* eller *foton* (utan den *asverify*) och anger värdnamn som  *\<mystorageaccount >. blob.core.windows.net*, där *mystorageaccount* är namnet på ditt lagringskonto. Med det här steget har registreringen av din anpassade domän slutförts.

1. Slutligen kan du ta bort den nyligen skapade CNAME-post som innehåller den *asverify* underdomän som krävdes endast som en mellanliggande steg.

När den nya CNAME-posten har spritts via DNS, om användarna har rätt behörigheter, kan de visa blob-data med hjälp av din anpassade domän.

## <a name="test-your-custom-domain"></a>Testa din anpassade domän

Kontrollera att den anpassade domänen har mappats till blobbtjänstens slutpunkt genom att skapa en blob i en offentlig behållare i ditt lagringskonto. I en webbläsare, få du åtkomst till bloben med hjälp av en URI i följande format: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Till exempel för att komma åt ett webbformulär i den *myforms* behållaren i den *photos.contoso.com* anpassade underdomänen, du kan använda följande URI: N: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Avregistrera en anpassad domän

Använd någon av följande procedurer för att avregistrera en anpassad domän för din Blob storage-slutpunkt.

### <a name="azure-portal"></a>Azure Portal

Om du vill ta bort inställningen för anpassad domän, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com)går du till ditt lagringskonto.

1. I fönstret menyn under **Blobtjänsten**väljer **Custom domain**.  
   Den **Custom domain** öppnas fönstret.

1. Radera innehållet i textrutan som innehåller ditt domännamn.

1. Välj knappen **Spara**.

När den anpassade domänen har tagits bort, visas ett Portalmeddelande om ditt storage-konto har uppdaterats.

### <a name="azure-cli"></a>Azure CLI

Ta bort en anpassad domän-registrering med den [az storage-konto update](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI kommandot och ange en tom sträng (`""`) för den `--custom-domain` argumentvärde.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ta bort en anpassad domän-registrering med den [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell-cmdleten och ange en tom sträng (`""`) för den `-CustomDomainName` argumentvärde.

* Kommandoformatet:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplet:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Nästa steg
* [Mappa en anpassad domän till en slutpunkt för Azure Content Delivery Network (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Använda Azure CDN för att få åtkomst till blobar med hjälp av anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Värd för statisk webbplats i Azure Blob storage (förhandsversion)](storage-blob-static-website.md)
