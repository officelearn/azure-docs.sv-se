---
title: Uppgradera till ett gpv2-lagringskonto – Azure Storage | Microsoft Docs
description: Uppgradera till gpv2-konton.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/11/2018
ms.author: tamram
ms.openlocfilehash: 8a0b823a12178df56417b05de28c2125ec155829
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740819"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Uppgradera till ett gpv2-konto

Generell användning v2-konton stöder de senaste funktionerna i Azure Storage och alla funktioner i allmänna v1 och Blob storage-konton. Gpv2-konton rekommenderas för de flesta lagringsscenarier med. Gpv2-konton leverera lägsta per gigabyte kapacitet priser för Azure Storage, samt bransch konkurrenskraftiga transaktionspriser.

Det är enkelt att uppgradera till ett gpv2-konto från dina allmänna v1- eller Blob storage-konton. Du kan uppgradera med Azure-portalen, PowerShell eller Azure CLI. Uppgradera ett konto kan inte ångras och kan resultera i fakturering avgifter.

## <a name="upgrade-using-the-azure-portal"></a>Uppgradera med Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Navigera till ditt lagringskonto.
3. I den **inställningar** klickar du på **Configuration**.
4. Klicka på **Uppgradera** under **Typ av konto**.
5. Ange namnet på ditt konto under **Bekräfta uppgradering**. 
6. Klicka på **uppgradera** längst ned på bladet.

## <a name="upgrade-with-powershell"></a>Uppgradera med PowerShell

Om du vill uppgradera ett general-purpose v1-konto till ett gpv2-konto med hjälp av PowerShell, först uppdatera PowerShell om du vill använda den senaste versionen av den **AzureRm.Storage** modulen. Mer information om hur du installerar PowerShell finns i [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 

Anropa sedan följande kommando för att uppgradera kontot genom att ersätta namnet på resursgruppen och lagringskontot:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Uppgradera med Azure CLI

Om du vill uppgradera ett general-purpose v1-konto till ett gpv2-konto med Azure CLI, först installera den senaste versionen av Azure CLI. Information om att installera CLI finns i [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

Anropa sedan följande kommando för att uppgradera kontot genom att ersätta namnet på resursgruppen och lagringskontot:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
``` 

## <a name="specify-an-access-tier-for-blob-data"></a>Ange en åtkomstnivå för blob-data

Gpv2-konton stöder alla Azure-lagringstjänster och dataobjekt, men åtkomstnivåerna är endast tillgängligt för blockblobbar i Blob storage. När du uppgraderar till ett gpv2-lagringskonto kan du ange åtkomstnivå för blob-data. 

Åtkomstnivåer kan du välja den mest kostnadseffektiva lagring baserat på ditt förväntade användningsmönster. Blockblob-objekt kan lagras i en frekvent, lågfrekvent eller arkivlagring nivå. Läs mer på åtkomstnivåerna [Azure Blob storage: frekvent, lågfrekvent och arkivlagringsnivå](../blobs/storage-blob-storage-tiers.md).

Som standard skapas ett nytt lagringskonto i frekvent åtkomstnivå och ett lagringskonto i allmänna v1 uppgraderas till frekvent åtkomstnivå. Överväg att ditt scenario om du utforskar vilken åtkomstnivå som ska användas för efter datauppgraderingen. Det finns två vanliga scenarier för att migrera till ett gpv2-konto:

* Du har ett befintligt general-purpose v1-lagringskonto och vill utvärdera en övergång till ett gpv2-lagringskonto med rätt lagringsnivå för blob-data.
* Du har valt att använda ett gpv2-lagringskonto eller redan har ett och vill utvärdera om du ska använda den frekventa eller lågfrekventa lagringsnivån för blob-data.

I båda fallen är högsta prioritet att beräkna kostnaden för lagring, åtkomst och körs på dina data som lagras i ett gpv2-lagringskonto och jämföra det med dina nuvarande kostnader.

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Beräkna kostnader för ditt nuvarande användningsmönster

Om du vill beräkna kostnaden för att lagra och komma åt blobdata i ett gpv2-lagringskonto i en viss nivå måste du utvärdera ditt nuvarande användningsmönster eller göra en uppskattning av ditt förväntade användningsmönster. Vanligtvis vill du veta:

* Ditt Blob storage-användning, i gigabyte, inklusive:
    - Hur mycket data lagras i lagringskontot?
    - Hur ändras datavolymen på månadsbasis; ersätter nya data ständigt gamla data?
* Primär åtkomstmönstret för dina Blob storage-data, inklusive:
    - Hur mycket data läses in och skrivs till storage-kontot? 
    - Hur många läsåtgärder jämfört med skriva utförs på data i storage-konto?

När du ska välja bästa åtkomstnivå för dina behov, kan det vara bra för att avgöra hur mycket kapacitet blobdata för närvarande använder, och hur dessa data används. 

För att samla in användningsdata för ditt storage-konto före migreringen, kan du övervaka till lagringskontot med [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor utför loggning och tillhandahåller mätvärden för Azure-tjänster, inklusive Azure Storage. 

Aktivera kapacitetsmåtten i Azure Monitor för att övervaka förbrukningsdata för blobbar i ditt lagringskonto. Kapacitet mått registrera information om hur mycket lagringsutrymme som blobbar i ditt konto använder dagligen. Kapacitet mått kan användas för att beräkna kostnaden för datalagring i lagringskontot. Läs hur Blob storage-kapacitet prissätts för för varje typ av konto i [priser för Block blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

Aktivera transaktionsmått i Azure Monitor för att övervaka dataåtkomstmönstren för Blob storage. Du kan filtrera på olika Azure Storage-åtgärder för att uppskatta hur ofta varje kallas. Att lära dig hur olika typer av transaktioner prissätts för block och tilläggsblobbar för varje typ av konto, finns i [priser för Block blob](https://azure.microsoft.com/pricing/details/storage/blobs/).  

Läs mer om att samla in mått från Azure Monitor, [Azure Storage-mått i Azure Monitor](storage-metrics-in-azure-monitor.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](storage-quickstart-create-account.md)
- [Hantera Azure storage-konton](storage-account-manage.md)