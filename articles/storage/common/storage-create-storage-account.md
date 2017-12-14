---
title: "Skapa, hantera eller ta bort ett lagringskonto på Azure-portalen | Microsoft Docs"
description: "Skapa ett nytt lagringskonto, hantera åtkomstnycklarna för ditt konto eller ta bort ett lagringskonto på Azure-portalen. Läs mer om premium- och standardlagringskonton."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 87c37da0-6cc6-4d88-a330-ef2896a1531d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
f1_keywords: sql13.swb.windowsazurestorage.connect.f1
ms.date: 10/11/2017
ms.author: tamram
ms.openlocfilehash: 6e20cf37f039f6bec32aee712a4d3c564d54cfc6
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="about-azure-storage-accounts"></a>Om Azure-lagringskonton

[!INCLUDE [storage-selector-portal-create-storage-account](../../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Översikt
Ett Azure Storage-konto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure Storage-dataobjekt. Alla objekt i ett lagringskonto faktureras tillsammans som en grupp. Som standard är data i ditt konto endast tillgängliga för dig, kontoägaren.

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Fakturering för lagringskonto

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

> [!NOTE]
> När du skapar en virtuell dator i Azure skapas ett lagringskonto automatiskt åt dig på distributionsplatsen om du inte redan har ett lagringskonto på den platsen. Du behöver alltså inte följa stegen nedan för att skapa ett lagringskonto för dina virtuella datorer. Namnet på lagringskontot baseras på den virtuella datorns namn. Mer information finns i [dokumentationen för Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).
> 
> 

## <a name="storage-account-endpoints"></a>Slutpunkter för lagringskonto
Alla objekt som du lagrar i Azure Storage har en unik URL-adress. Lagringskontots namn bildar underdomänen i den adressen. Kombinationen av underdomän och domännamn, som är specifika för varje tjänst, utgör en *slutpunkt* för ditt lagringskonto.

Om ditt lagringskonto till exempel heter *mittlagringskonto*, så är standardslutpunkterna för ditt lagringskonto:

* Blob-tjänst: http://*mittlagringskonto*.blob.core.windows.net
* Tabelltjänst: http://*mittlagringskonto*.table.core.windows.net
* Kötjänst: http://*mittlagringskonto*.queue.core.windows.net
* Filtjänst: http://*mittlagringskonto*.file.core.windows.net

> [!NOTE]
> Ett Blob Storage-konto exponerar endast blob-tjänstens slutpunkt.
> 
> 

URL:en för att komma åt ett objekt i ett lagringskonto skapas genom att objektets plats i lagringskontot läggs till i slutpunkten. En blobbadress kan till exempel ha följande format: http://*mittlagringskonto*.blob.core.windows.net/*minbehållare*/*minblobb*.

Du kan också konfigurera ett eget domännamn som ska användas med ditt lagringskonto. Mer information finns i [Konfigurera ett eget domännamn för din slutpunkt för Blob Storage](../blobs/storage-custom-domain-name.md). Du kan också konfigurera det med PowerShell. För mer information, se cmdleten [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount).  


## <a name="create-a-storage-account"></a>skapar ett lagringskonto
1. Logga in på [Azure Portal](https://portal.azure.com).
2. I Azure-portalen, expanderar du menyn på vänster sida för att öppna tjänstemenyn och välj **fler tjänster**. Rulla ned till **lagring** och välj **lagringskonton**. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
3. Ange ett namn för lagringskontot. Mer information om hur lagringskontots namn används för att adressera dina objekt i Azure Storage finns i [Slutpunkter för lagringskonto](#storage-account-endpoints).
   
   > [!NOTE]
   > Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener.
   > 
   > Namnet på ditt lagringskonto måste vara unikt i Azure. Azure Portal anger om det namn du valt för lagringskontot redan används.
   > 
   > 
4. Ange vilken distributionsmodell som ska användas: **Resource Manager** eller **Klassisk**. **Resource Manager** är den rekommenderade distributionsmodellen. Mer information finns i [Förstå Resource Manager-distribution och klassisk distribution](../../azure-resource-manager/resource-manager-deployment-model.md).
   
   > [!NOTE]
   > Blob Storage-konton kan bara skapas med Resource Manager-distributionsmodellen.

5. Välj typen av lagringskonto: **Allmänt** eller **Blob Storage**. **Allmänt** är standardinställningen.
   
    Om du valde **Allmänt** anger du prestandanivån: **Standard** eller **Premium**. Standardinställningen är **Standard**. Mer information om standard- och premiumlagringskonton finns i [Introduktion till Microsoft Azure Storage](storage-introduction.md) och [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella Azure-datorer](../../virtual-machines/windows/premium-storage.md).
   
    Om du valde **Blob Storage** anger du åtkomstnivån: **Het** eller **Kall**. Standardinställningen är **Frekvent**. Mer information finns i [Azure Blob Storage: nivåerna Kall och Het](../blobs/storage-blob-storage-tiers.md).
6. Välj replikeringsalternativet för lagringskontot: **LRS**, **GRS**, **RA-GRS** eller **ZRS**. Standardinställningen är **RA-GRS**. Mer information om replikeringsalternativen för Azure Storage finns i [Azure Storage-replikering](storage-redundancy.md).
7. Välj den prenumeration som du vill skapa det nya lagringskontot i.
8. Ange en ny resursgrupp eller välj en befintlig resursgrupp. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
9. Välj den geografiska platsen för ditt lagringskonto. Mer information om vilka tjänster som är tillgängliga i vilken region finns i [Azure-regioner](https://azure.microsoft.com/regions/#services).
10. Skapa lagringskontot genom att klicka på **Skapa**.

## <a name="manage-your-storage-account"></a>Hantera ditt lagringskonto
### <a name="change-your-account-configuration"></a>Ändra kontokonfigurationen
När du har skapat ditt lagringskonto kan du ändra kontots konfiguration och till exempel ändra replikeringsalternativet som används för kontot eller ändra åtkomstnivån för ett Blob Storage-konto. På [Azure Portal](https://portal.azure.com) navigerar du till ditt lagringskonto, klickar på **INSTÄLLNINGAR** och klickar sedan på **Konfiguration** för att visa och/eller ändra kontokonfigurationen.

> [!NOTE]
> Vissa replikeringsalternativ kanske inte är tillgängliga beroende på vilken prestandanivå du valde när du skapade lagringskontot.
> 
> 

Om du ändrar replikeringsalternativ så ändras ditt pris. Mer information finns på sidan med [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

För Blob Storage-konton kan det utgå avgifter för ändringen av åtkomstnivå, förutom prisändringen. Mer information finns i avsnittet om [priser och fakturering för Blob Storage-konton](../blobs/storage-account-options.md#pricing-and-billing).

### <a name="manage-your-storage-access-keys"></a>Hantera dina åtkomstnycklar för lagring
När du skapar ett lagringskonto genererar Azure två 512-bitars åtkomstnycklar för lagring, som används för autentisering när lagringskontot används. Eftersom två åtkomstnycklar för lagring genereras kan du återskapa nycklarna utan avbrott i lagringstjänsten eller i åtkomsten till den tjänsten.

> [!NOTE]
> Vi rekommenderar att du inte delar dina åtkomstnycklar för lagring med andra. Du kan ge åtkomst till lagringsresurser utan att lämna ut dina åtkomstnycklar genom att använda en *signatur för delad åtkomst*. En signatur för delad åtkomst ger åtkomst till en resurs i ditt konto under ett intervall som du definierar och med de behörigheter som du anger. Mer information finns i [Använda signaturer för delad åtkomst (SAS)](storage-dotnet-shared-access-signature-part-1.md).
> 
> 
<a id="view-and-copy-storage-access-keys"/></a>
#### <a name="view-and-copy-storage-access-keys"></a>Visa och kopiera åtkomstnycklar för lagring
På [Azure Portal](https://portal.azure.com) navigerar du till ditt lagringskonto, klickar på **Alla inställningar** och sedan på **Åtkomstnycklar** för att visa, kopiera och återskapa åtkomstnycklarna för ditt konto. Bladet **Åtkomstnycklar** innehåller också förkonfigurerade anslutningssträngar med dina primära och sekundära nycklar som du kan kopiera och använda i dina program.

#### <a name="regenerate-storage-access-keys"></a>Återskapa åtkomstnycklar för lagring
Vi rekommenderar att du ändrar åtkomstnycklarna för ditt lagringskonto med jämna mellanrum för att skydda lagringsanslutningarna. Två åtkomstnycklar tilldelas så att du kan upprätthålla anslutningar till lagringskontot med den ena åtkomstnyckeln medan du återskapar den andra.

> [!WARNING]
> Tjänster i Azure och dina program som är beroende av lagringskontot kan påverkas när du återskapar åtkomstnycklarna. Alla klienter som använder åtkomstnycklar för att komma åt lagringskontot måste uppdateras så att de använder den nya nyckeln.
> 
> 

**Medietjänster** – Om du har medietjänster som är beroende av ditt lagringskonto måste du synkronisera åtkomstnycklarna med medietjänsten igen när du har återskapat nycklarna.

**Program** – Om du har webbprogram och molntjänster som använder lagringskontot går anslutningarna förlorade om du återskapa nycklar, såvida du inte ”rullar” nycklarna.

**Lagringsutforskare** – Om du använder [lagringsutforskare](storage-explorers.md) behöver du förmodligen uppdatera lagringsnyckeln som används av dessa program.

Processen för att rotera åtkomstnycklar för lagring ser ut så här:

1. Uppdatera anslutningssträngarna i programkoden så att de refererar till lagringskontots sekundära åtkomstnyckel.
2. Återskapa lagringskontots primära åtkomstnyckel. I bladet **Åtkomstnycklar** klickar du på **Återskapa Nyckel1** och klickar sedan på **Ja** för att bekräfta att du vill skapa en ny nyckel.
3. Uppdatera anslutningssträngarna i koden så att de refererar till den nya primärnyckeln.
4. Återskapa den sekundära åtkomstnyckeln på samma sätt.

## <a name="delete-a-storage-account"></a>Ta bort ett lagringskonto
Om du vill ta bort ett lagringskonto som du inte längre använder navigerar du till lagringskontot på [Azure Portal](https://portal.azure.com) och klickar på **Ta bort**. Om du tar bort ett lagringskonto tas hela kontot bort, inklusive alla data på kontot.

> [!WARNING]
> Det går inte att återställa ett borttaget lagringskonto eller att hämta innehåll som det innehöll före borttagningen. Var noga med att säkerhetskopiera allt som du vill spara innan du tar bort kontot. Detta gäller även alla resurser i kontot. När du tar bort en blobb, tabell, kö eller fil tas den bort permanent.
> 

Om du försöker ta bort ett lagringskonto som är kopplat till en virtuell Azure-dator kan du få ett felmeddelande om att lagringskontot fortfarande används. Information om hur du felsöker det här felet finns i [Felsöka fel när du tar bort lagringskonton](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Nästa steg
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
* [Azure Blob Storage: nivåerna Kall och Het](../blobs/storage-blob-storage-tiers.md)
* [Azure Storage-replikering](storage-redundancy.md)
* [Konfigurera anslutningssträngar för Azure Storage](../storage-configure-connection-string.md)
* [Överföra data med kommandoradsverktyget AzCopy](storage-use-azcopy.md)
* Besök [Azure Storage-teamets blogg](http://blogs.msdn.com/b/windowsazurestorage/).

