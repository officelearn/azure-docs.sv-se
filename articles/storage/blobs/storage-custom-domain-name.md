---
title: Konfigurera ett anpassat domän namn för ditt Azure Storage-konto | Microsoft Docs
description: Använd Azure Portal för att mappa ditt eget kanoniska namn (CNAME) till blob-lagringen eller webb slut punkten i ett Azure Storage-konto.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: ae809dd4841d6df8caabebfaded263b52f386834
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845042"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Konfigurera ett anpassat domän namn för ditt Azure Storage-konto

Du kan konfigurera en anpassad domän för åtkomst till BLOB-data på ditt Azure Storage-konto. Standard slut punkten för Azure Blob Storage är  *\<Storage-Account-name >. blob. Core. Windows. net*. Du kan också använda webb slut punkten som skapas som en del av funktionen för [statiska webbplatser](storage-blob-static-website.md). Om du mappar en anpassad domän och under domän, t. ex. *www\.-contoso.com*, till BLOB-eller webb slut punkten för ditt lagrings konto kan användarna använda domänen för att få åtkomst till BLOB-data i ditt lagrings konto.

> [!IMPORTANT]
> Azure Storage har inte inbyggt stöd för HTTPS med anpassade domäner. Du kan för närvarande [använda Azure CDN för att få åtkomst till blobbar genom att använda anpassade domäner över https](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Lagrings konton har för närvarande endast stöd för ett anpassat domän namn per konto. Du kan inte mappa ett anpassat domän namn till både webb-och blob-tjänstens slut punkter.
> 
> [!NOTE]
> Mappningen fungerar bara för under domäner (t. ex. www\.-contoso.com). Om du vill att din webb slut punkt ska vara tillgänglig på rot domänen (t. ex. contoso.com) måste du [använda Azure CDN med anpassade domäner](storage-https-custom-domain-cdn.md)

I följande tabell visas några exempel-URL: er för BLOB-data som finns i ett lagrings konto med namnet *mystorageaccount*. Den anpassade under domänen som är registrerad för lagrings kontot är *www\.-contoso.com*:

| Resurstyp | Standardwebbadress | Anpassad domän-URL |
| --- | --- | --- |
| Lagringskonto | http:\//mystorageaccount.blob.core.windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.Core.Windows.net/mycontainer/myblob | http:\//www.contoso.com/mycontainer/myblob |
| Rot behållare | http:\//mystorageaccount.blob.Core.Windows.net/myblob eller http:\//mystorageaccount.blob.Core.Windows.net/$root/myblob | http:\//www.contoso.com/myblob eller http:\//www.contoso.com/$root/myblob |
| Webb |  http:\//mystorageaccount. [ zon]. Web. Core. Windows. net/$Web/[indexdoc] eller http:\//mystorageaccount. [ zon]. Web. Core. Windows. net/[indexdoc] eller http:\//mystorageaccount. [ zon]. Web. Core. Windows. net/$Web eller http:\//mystorageaccount. [ zon]. Web. Core. Windows. net/ | http:\//www.contoso.com/$Web eller http:\//www.contoso.com/eller http:\//www.contoso.com/$Web/[indexdoc] eller http:\//www.contoso.com/[indexdoc] |

> [!NOTE]  
> Som du ser i följande avsnitt gäller alla exempel för BLOB service-slutpunkten även för webb tjänstens slut punkt.

## <a name="direct-vs-intermediary-cname-mapping"></a>Direkta eller mellanliggande CNAME-mappning

Du kan peka din anpassade domän med en under domän (t. ex. www\.-contoso.com) till BLOB-slutpunkten för ditt lagrings konto på något av två sätt: 
* Använd direkt CNAME-mappning.
* Använd under domänen för *att verifiera* mellanliggande under domän.

### <a name="direct-cname-mapping"></a>Direkt CNAME-mappning

Den första och enklaste metoden är att skapa en post för kanoniskt namn (CNAME) som mappar din anpassade domän och under domän direkt till BLOB-slutpunkten. En CNAME-post är en DNS-funktion (Domain Name System) som mappar en käll domän till en mål domän. I vårt exempel är käll domänen din egen anpassade domän och under domän (till exempel*www\.-contoso.com*). Mål domänen är din BLOB service-slutpunkt (t. ex.*mystorageaccount.blob.Core.Windows.net*).

Den direkta metoden beskrivs i avsnittet "Registrera en anpassad domän".

### <a name="intermediary-mapping-with-asverify"></a>Mellanliggande mappning med *kontrollerad*

Den andra metoden använder också CNAME-poster. För att undvika drift stopp använder den dock en särskild under domän som *kontrollerar* att den känns igen av Azure.

Att mappa din anpassade domän till en BLOB-slutpunkt kan orsaka en kort tids period när du registrerar domänen i [Azure Portal](https://portal.azure.com). Om domänen har stöd för ett program med ett service nivå avtal som kräver noll stillestånd, använder du Azure-gruppen för *att verifiera* under domäner som ett mellanliggande registrerings steg. Det här steget ser till att användarna kan komma åt din domän när DNS-mappningen äger rum.

Den mellanliggande metoden beskrivs i registrera en anpassad domän med hjälp av under domänen för *att verifiera* .

## <a name="register-a-custom-domain"></a>Registrera en anpassad domän
Registrera domänen med hjälp av proceduren i det här avsnittet om följande uttryck gäller:
* Du är inte bekymrad om att domänen är en kort användare som inte är tillgänglig för användarna.
* Den anpassade domänen är inte värd för ett program. 

Du kan använda Azure DNS för att konfigurera ett anpassat DNS-namn för Azure Blob Store. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Om din anpassade domän för närvarande har stöd för ett program som inte kan ha någon avbrotts tid, använder du proceduren i registrera en anpassad domän med hjälp av under domänen för *att verifiera* .

Om du vill konfigurera ett anpassat domän namn skapar du en ny CNAME-post i DNS. Posten CNAME anger ett alias för ett domän namn. I vårt exempel mappas adressen till din anpassade domän till lagrings kontots Blob Storage-slutpunkt.

Du kan vanligt vis hantera din domäns DNS-inställningar på din domän registrators webbplats. Varje registrator har en liknande men något annorlunda metod för att ange en CNAME-post, men begreppet är detsamma. Eftersom vissa grundläggande domän registrerings paket inte erbjuder DNS-konfiguration kan du behöva uppgradera ditt domän registrerings paket innan du kan skapa CNAME-posten.

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

1. I meny fönstret, under **BLOB service**, väljer du **anpassad domän**.  
   Fönstret **anpassad domän** öppnas.

1. Logga in på din domän registrators webbplats och gå sedan till sidan för att hantera DNS.  
   Du kan hitta sidan i ett avsnitt med namnet **domän namn**, **DNS**eller **namn server hantering**.

1. Leta reda på avsnittet för att hantera CNAME.  
   Du kanske måste gå till sidan Avancerade inställningar och leta efter **CNAME**, **alias**eller under **domäner**.

1. Skapa en ny CNAME-post, ange ett under domän namn som **www** eller **foton** (under domän krävs, rot domäner stöds inte) och ange sedan ett värdnamn.  
   Värd namnet är BLOB service-slutpunkten. Formatet är  *\<mystorageaccount >. blob. Core. Windows. net*, där *mystorageaccount* är namnet på ditt lagrings konto. Värd namnet som ska användas visas i objekt #1 i fönstret för den **anpassade domänen** i [Azure Portal](https://portal.azure.com). 

1. I rutan **anpassad domän** , i text rutan, anger du namnet på din anpassade domän, inklusive under domänen.  
   Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger **du\.www-contoso.com**. Om din under domän är *foton*, anger du **photos.contoso.com**.

1. Om du vill registrera din anpassade domän väljer du **Spara**.  
   Om registreringen lyckas meddelar portalen dig att ditt lagrings konto har uppdaterats.

När din nya CNAME-post har spridits via DNS, kan de, om dina användare har rätt behörigheter, Visa BLOB-data med hjälp av din anpassade domän.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registrera en anpassad domän med hjälp av under domänen för *att verifiera*
Om din anpassade domän för närvarande har stöd för ett program med ett service avtal som kräver att det inte finns något avbrott, registrerar du din anpassade domän med hjälp av proceduren i det här avsnittet. Genom att skapa en CNAME som pekar på *Verifiera.\< under domän >. customdomain\<>* för att *Verifiera.\< storageaccount >. blob. Core. Windows. net*, du kan i förväg registrera din domän med Azure. Du kan sedan skapa en andra CNAME som pekar från  *\<under domän >.\< customdomain >* till  *\<storageaccount >. blob. Core. Windows. net*, och trafiken till din anpassade domän dirigeras till din BLOB-slutpunkt.

Under domänen för *att verifiera* är en särskild under domän som identifieras av Azure. Genom att vänta med att *Verifiera* till din egen under domän tillåter du att Azure känner igen din anpassade domän utan att behöva ändra DNS-posten för domänen. När du ändrar DNS-posten för domänen mappas den till BLOB-slutpunkten utan avbrott.

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

1. I meny fönstret, under **BLOB service**, väljer du **anpassad domän**.  
   Fönstret **anpassad domän** öppnas.

1. Logga in på din DNS-leverantörs webbplats och gå sedan till sidan för att hantera DNS.  
   Du kan hitta sidan i ett avsnitt med namnet **domän namn**, **DNS**eller **namn server hantering**.

1. Leta reda på avsnittet för att hantera CNAME.  
   Du kanske måste gå till sidan Avancerade inställningar och leta efter **CNAME**, **alias**eller under **domäner**.

1. Skapa en ny CNAME-post, ange ett under domän Ali Aset som innehåller under domänen för att *Verifiera* , till exempel **Verifiera. www** eller **Verifiera. foton**och ange ett värdnamn.  
   Värd namnet är BLOB service-slutpunkten. Formatet är *verifierat.\< mystorageaccount >. blob. Core. Windows. net*, där *mystorageaccount* är namnet på ditt lagrings konto. Värd namnet som ska användas visas i objekt #2 i fönstret för den *anpassade domänen* i [Azure Portal](https://portal.azure.com).

1. I rutan **anpassad domän** , i text rutan, anger du namnet på din anpassade domän, inklusive under domänen.  
   Ta inte med *kontroll*. Om din domän till exempel är *contoso.com* och ditt under domän Ali Aset är *www*, anger **du\.www-contoso.com**. Om din under domän är *foton*, anger du **photos.contoso.com**.

1. Markera kryss rutan **Använd indirekt CNAME-validering** .

1. Om du vill registrera din anpassade domän väljer du **Spara**.  
   Om registreringen lyckas meddelar portalen dig att ditt lagrings konto har uppdaterats. Din anpassade domän har verifierats av Azure, men trafik till din domän har ännu inte dirigerats till ditt lagrings konto.

1. Gå tillbaka till din DNS-leverantörs webbplats och skapa sedan en annan CNAME-post som mappar under domänen till BLOB service-slutpunkten.  
   Ange till exempel under domänen som *www* eller *foton* (utan att *kontrol lera*) och ange värd namnet som  *\<mystorageaccount >. blob. Core. Windows. net*, där *mystorageaccount* är namnet på din lagrings konto. I det här steget är registreringen av din anpassade domän slutförd.

1. Slutligen kan du ta bort den nyligen skapade CNAME-posten som innehåller under domänen för *att verifiera* , vilket endast krävdes som ett mellanliggande steg.

När din nya CNAME-post har spridits via DNS, kan de, om dina användare har rätt behörigheter, Visa BLOB-data med hjälp av din anpassade domän.

## <a name="test-your-custom-domain"></a>Testa din anpassade domän

För att bekräfta att din anpassade domän är mappad till BLOB service-slutpunkten skapar du en BLOB i en offentlig behållare i ditt lagrings konto. I en webbläsare öppnar du sedan bloben med hjälp av en URI i följande format:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Om du till exempel vill få åtkomst till ett webb formulär i behållaren mina *former* i den anpassade under domänen *photos.contoso.com* kan du använda följande URI:`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Avregistrera en anpassad domän

Använd någon av följande procedurer för att avregistrera en anpassad domän för din Blob Storage-slutpunkt.

### <a name="azure-portal"></a>Azure Portal

Om du vill ta bort den anpassade domän inställningen gör du följande:

1. I [Azure Portal](https://portal.azure.com)går du till ditt lagrings konto.

1. I meny fönstret, under **BLOB service**, väljer du **anpassad domän**.  
   Fönstret **anpassad domän** öppnas.

1. Ta bort innehållet i text rutan som innehåller ditt anpassade domän namn.

1. Välj knappen **Spara**.

När den anpassade domänen har tagits bort visas ett Portal meddelande om att ditt lagrings konto har uppdaterats.

### <a name="azure-cli"></a>Azure CLI

Om du vill ta bort en anpassad domän registrering använder du kommandot [AZ Storage Account Update](https://docs.microsoft.com/cli/azure/storage/account) CLI och anger sedan en tom sträng`""`() för `--custom-domain` argumentvärdet.

* Kommando format:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Kommando exempel:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill ta bort en anpassad domän registrering använder du PowerShell-cmdleten [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) och anger sedan en`""`tom sträng ( `-CustomDomainName` ) för argumentvärdet.

* Kommando format:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Kommando exempel:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Nästa steg
* [Mappa en anpassad domän till en Azure Content Delivery Network-slutpunkt (CDN)](../../cdn/cdn-map-content-to-custom-domain.md)
* [Använda Azure CDN för att få åtkomst till blobbar med hjälp av anpassade domäner över HTTPS](storage-https-custom-domain-cdn.md)
* [Statisk webbplats som är värd för Azure Blob Storage (för hands version)](storage-blob-static-website.md)
