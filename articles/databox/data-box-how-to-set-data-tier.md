---
title: Använda Azure Data Box för att skicka data till – varm, kall, arkivera - block blob-nivå | Microsoft-Docs i data
description: Beskriver hur du använder Azure Data Box för att skicka data till en spärr blob storage-nivå, till exempel frekvent, lågfrekvent eller Arkiv
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: 44fa81ddf6b0892c6d900fd065589b3d6ac5a0bd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789133"
---
# <a name="use-azure-data-box-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Använda Azure Data Box för att skicka data till lämplig Azure Storage blob-nivå

Azure Data Box flyttar stora mängder data till Azure genom att leverera en tillverkarspecifik lagringsenhet. Du fyller upp enheten med data och lämna tillbaka. Data från Data Box har överförts till en Standardnivå som är associerade med lagringskontot. Du kan sedan flytta data till en annan lagringsnivå.

Den här artikeln beskriver hur de data som överförs i Data Box kan flyttas till en frekvent, lågfrekvent eller Arkiv blob-nivå.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Välj rätt lagringsnivå för dina data

Azure storage kan tre olika nivåer att lagra data på det mest kostnadseffektiva sättet – frekvent, lågfrekvent eller Arkiv. Frekvent lagringsnivå är optimerad för att lagra data som används ofta. Frekvent lagring har högre kostnader för lagring än lågfrekvent lagring och Arkivlagring lagring, men de lägsta åtkomstkostnaderna.

Lågfrekventa lagringsnivån är för sällan använda data som behöver lagras i minst 30 dagar. Lagringskostnaden för kalla nivån är lägre än för frekvent lagringsnivå men avgifter för dataåtkomst är hög jämfört med frekvent nivå.

Azure arkivlagring är offline och erbjuder de lägsta lagringskostnaderna men även de högsta åtkomstkostnaderna. Den här nivån är avsedd för data som finns kvar i arkivering lagringsutrymmet i minst 180 dagar. Mer information om var och en av de här nivåerna och prismodellen går du till [jämförelse av lagringsnivåerna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

Data från Data Box har överförts till en lagringsnivå som är associerad med storage-konto. När du skapar ett lagringskonto kan du ange åtkomstnivån som frekvent eller lågfrekvent. Beroende på åtkomstmönstret av din arbetsbelastning och kostnad kan flytta du dessa data från den standardlagringsnivån till en annan lagringsnivå.

Du kan bara nivå dina data för lagring av objekt i Blob storage- eller generell användning v2 (GPv2)-konton. GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Välj rätt lagringsnivå för dina data genom att gå igenom de överväganden som beskrivs i [Azure Blob storage: Premium, lagringsnivåerna frekvent, lågfrekvent lagring och Arkivlagring](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Ange en standard-blob-nivå

Blob-Standardnivå anges när lagringskontot har skapats i Azure-portalen. När en lagringstyp är markerad som GPv2 eller Blob storage kan attributet för åtkomstnivå anges. Som standard väljs den frekventa nivån.

Nivåerna kan inte anges om du vill skapa ett nytt konto när du beställer en Data Box. När kontot har skapats kan ändra du kontot i portalen för att ange standard åtkomstnivå.

Du kan också skapar du ett lagringskonto först med det angivna attributet för åtkomstnivå. När du skapar Data Box-beställning väljer du det befintliga lagringskontot. Mer information om hur du ställer in standard blob-nivå under lagringskontot har skapats går du till [skapa ett lagringskonto i Azure-portalen](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Flytta data till en icke-standard-nivå

När data från Data Box har överförts till standardlagringsnivån, kan du flytta data till en icke-standard-nivå. Det finns två sätt att flytta data till en icke-standard-nivå.

- **Azure Blob storage-livscykelhantering** – du kan använda en principbaserad metod för att automatiskt nivå data eller upphör att gälla i slutet av livscykeln. Mer information går du till [hanterar livscykeln för Azure Blob storage](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Skript** – du kan använda en skriptbaserade metod via Azure PowerShell för att aktivera blob-nivå. Du kan anropa den `SetBlobTier` åtgärd för att ställa in nivån på blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Använda Azure PowerShell för att ange blob-nivå

Följande steg beskriver hur du kan ange blob-nivå till arkivet med hjälp av Azure PowerShell-skript.

1. Öppna en upphöjd Windows PowerShell-session. Se till att din körs PowerShell 5.0 eller senare. Ange:

   `$PSVersionTable.PSVersion`     

2. Logga in på Azure PowerShell. 

   `Login-AzAccount`  

3. Definiera variabler för storage-konto, åtkomstnyckel, behållare och storage-kontext.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Hämta alla blobbar i behållaren.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Ange nivå för alla blobbar i behållaren till arkivet.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Ett exempel på utdata visas nedan:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Om du vill att data att arkivera på mata in genom att ange standardlagringsnivån för kontot till frekvent. Om den standardlagringsnivån är lågfrekvent, sedan finns det en 30-dagars tidig borttagning av särskilda avgifter om informationen flyttas till arkivet omedelbart.

## <a name="next-steps"></a>Nästa steg

-  Lär dig hur du adress den [vanliga data lagringsnivåer scenarier med reglerna för livscykel](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

