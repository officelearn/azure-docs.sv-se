---
title: Skicka data till hot, cold, archive blob-nivå via Azure Data Box/Azure Data Box Heavy
description: Beskriver hur du använder Azure Data Box eller Azure Data Box Heavy för att skicka data till en lämplig block blob lagringsnivå som varmt, kallt eller arkiv
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 31178284313ad7dafb094b109a75d4550cabd39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560379"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Använda Azure Data Box eller Azure Data Box Heavy för att skicka data till lämplig Azure Storage-blob-nivå

Azure Data Box flyttar stora mängder data till Azure genom att skicka en egen lagringsenhet till dig. Du fyller upp enheten med data och returnerar den. Data från Data Box överförs till en standardnivå som är associerad med lagringskontot. Du kan sedan flytta data till en annan lagringsnivå.

I den här artikeln beskrivs hur data som överförs av Data Box kan flyttas till en blob-nivå för varmt, kallt eller arkiv. Den här artikeln gäller både Azure Data Box och Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Välj rätt lagringsnivå för dina data

Azure-lagring gör att tre olika nivåer kan lagra data på det mest kostnadseffektiva sättet - Hot, Cold eller Archive. Hot storage-nivån är optimerad för lagring av data som används ofta. Frekvent lagring har högre lagringskostnader än lagring av kyl- och arkiv, men de lägsta åtkomstkostnaderna.

Cool lagringsnivå är för sällan använda data som måste lagras i minst 30 dagar. Lagringskostnaden för kall nivå är lägre än för frekvent lagringsnivå, men dataåtkomstavgifterna är höga jämfört med hot-nivå.

Azure Archive-nivån är offline och erbjuder de lägsta lagringskostnaderna men även de högsta åtkomstkostnaderna. Den här nivån är avsedd för data som finns kvar i arkiveringslagring i minst 180 dagar. Mer information om var och en av dessa nivåer och prismodellen finns [i Jämförelse av lagringsnivåerna](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

Data från datarutan eller databoxen tung överförs till en lagringsnivå som är associerad med lagringskontot. När du skapar ett lagringskonto kan du ange åtkomstnivån som Varm eller Kall. Beroende på åtkomstmönstret för din arbetsbelastning och kostnad kan du flytta dessa data från standardnivån till en annan lagringsnivå.

Du får endast nivå dina objektlagringsdata i Blob storage eller General Purpose v2 (GPv2) konton. GPv1-konton (General Purpose v1) har inte stöd för flera lagringsnivåer. Om du vill välja rätt lagringsnivå för dina data läser du de överväganden som beskrivs i [Azure Blob storage: Premium, Hot, Cool och Archive storage tiers](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Ange en standardblolobnivå

Standard blob-nivån anges när lagringskontot skapas i Azure-portalen. När en lagringstyp har valts som GPv2- eller Blob-lagring kan attributet Access-nivå anges. Som standard är hot-nivån markerad.

Nivåerna kan inte anges om du försöker skapa ett nytt konto när du beställer en dataruta eller dataruta tung. När kontot har skapats kan du ändra kontot i portalen för att ange standardåtkomstnivån.

Du kan också skapa ett lagringskonto först med attributet angiven åtkomstnivå. När du skapar datarutan eller datarutan Tung ordning väljer du det befintliga lagringskontot. Mer information om hur du anger standard blob-nivån när lagringskonto skapas finns i [Skapa ett lagringskonto i Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Flytta data till en icke-standardnivå

När data från Data Box-enheten har överförts till standardnivån kanske du vill flytta data till en icke-standardnivå. Det finns två sätt att flytta dessa data till en icke-standardnivå.

- **Livscykelhantering för Azure Blob-lagring** – Du kan använda en principbaserad metod för att automatiskt nivåla in data eller upphöra att gälla i slutet av livscykeln. Mer information finns [i Hantera azure blob-lagringslivscykeln](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Skript -** Du kan använda en skriptmetod via Azure PowerShell för att aktivera blob-nivånivå. Du kan `SetBlobTier` anropa åtgärden för att ange nivån på blobben.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Använda Azure PowerShell för att ange blob-nivån

I följande steg beskrivs hur du kan ställa in blob-nivån till Arkiv med ett Azure PowerShell-skript.

1. Öppna en förhöjd Windows PowerShell-session. Kontrollera att du kör PowerShell 5.0 eller senare. Ange:

   `$PSVersionTable.PSVersion`     

2. Logga in på Azure PowerShell. 

   `Login-AzAccount`  

3. Definiera variablerna för lagringskonto, åtkomstnyckel, behållare och lagringskontexten.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Hämta alla blobbar i behållaren.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Ange nivån för alla blobbar i behållaren till Arkiv.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Nedan visas ett exempel på utdata:

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
   > Om du vill att data ska arkiveras vid intag anger du standardkontonivån till Hot. Om standardnivån är Cool, då finns det en 30-dagars tidig borttagning straff om data flyttas till Arkiv omedelbart.

## <a name="next-steps"></a>Nästa steg

-  Lär dig hur du hanterar de [vanliga scenarierna för datanivådelning med livscykelprincipregler](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

