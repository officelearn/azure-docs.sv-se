---
title: Användningsfall för Microsoft Azure Data Box Gateway | Microsoft-dokument
description: Beskriver användningsfallen för Azure Data Box Gateway, en lagringslösning för virtuella enheter som gör att du kan överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/02/2019
ms.author: alkohli
ms.openlocfilehash: e72113313e27949819db567c550401b1f051473f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022689"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Använda ärenden för Azure Data Box Gateway

Azure Data Box Gateway är en molnlagringsgatewayenhet som finns lokalt och skickar din avbildning, media och andra data till Azure. Den här molnlagringsgatewayen är en virtuell dator som etablerats i hypervisorn. Du skriver data till den här virtuella enheten med NFS- och SMB-protokollen, som den sedan skickar till Azure. Den här artikeln innehåller en detaljerad beskrivning av scenarierna där du kan distribuera den här enheten.

Använd Data Box Gateway för följande scenarier:

- Att kontinuerligt inta stora mängder data.
- För molnarkivering av data på ett säkert och effektivt sätt.
- För inkrementell dataöverföring över nätverket efter att den första massöverföringen har gjorts med databoxen.

Vart och ett av dessa scenarier beskrivs i detalj i de efterföljande avsnitten.


## <a name="continuous-data-ingestion"></a>Kontinuerligt datainmatning

En av de främsta fördelarna med Data Box Gateway är möjligheten att kontinuerligt matsätta data i enheten för att kopiera till molnet, oavsett datastorlek.

När data skrivs till gateway-enheten överför enheten data till Azure Storage. Enheten hanterar automatiskt lagring genom att ta bort filerna lokalt samtidigt som metadata behålls när den når ett visst tröskelvärde. Om du behåller en lokal kopia av metadata kan gateway-enheten bara överföra ändringarna när filen uppdateras. De data som överförs till gateway-enheten bör vara enligt riktlinjerna i [Dataöverföringssasatlag](data-box-gateway-limits.md#data-upload-caveats).

När enheten fylls med data börjar den strypa inträngningshastigheten (efter behov) för att matcha den hastighet med vilken data överförs till molnet. Om du vill övervaka det kontinuerliga inmatningen på enheten använder du aviseringar. Dessa aviseringar utlöses när begränsningen startar och rensas när begränsningen har stoppats.

## <a name="cloud-archival-of-data"></a>Molnarkivering av data

Använd Data Box Gateway när du vill behålla dina data på lång sikt i molnet. Du kan använda **lagringsnivån arkiv** för långsiktig kvarhållning.

Arkivnivån är optimerad för att lagra data som sällan används i minst 180 dagar. **Arkivnivån** erbjuder de lägsta lagringskostnaderna men har de högsta åtkomstkostnaderna. Mer information finns på [arkivåtkomstnivån](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Flytta data till arkivnivå

Innan du börjar kontrollerar du att du har en enhet som kör Data Box Gateway. Följ stegen som beskrivs i [självstudiekursen: Förbered distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md) och fortsätt framåt till nästa självstudiekurs tills du har en fungerande enhet.

- Använd Data Box Gateway-enheten för att överföra data till Azure genom den vanliga överföringsproceduren enligt beskrivningen i [Överför data via Data Box Gateway](data-box-gateway-deploy-add-shares.md).
- När data har överförts måste du flytta den till arkivnivån. Du kan ange blob-nivån på två sätt: Azure PowerShell-skript eller en Azure Storage Lifecycle Management-princip.  
    - Om du använder Azure PowerShell följer du dessa [steg](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) för att flytta data till arkivnivån.
    - Om du använder Azure Lifecycle Management följer du dessa steg för att flytta data till arkivnivån.
        - [Registrera dig](/azure/storage/common/storage-lifecycle-management-concepts) för förhandsgranskningen av Blob Lifecycle Management Service för att använda arkivnivå.
        - Använd följande princip för att [arkivera data på intag](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-after-ingest).
- När blobbar har markerats som Arkiv kan de inte längre ändras av gatewayen om de inte flyttas till en frekvent eller kall nivå. Om filen finns i det lokala lagringsutrymmet överförs inte alla ändringar som görs i den lokala kopian (inklusive borttagningar) till arkivnivån.
- Om du vill läsa data i Arkivlagring måste den rehydreras genom att blob-nivån ändras till varm eller sval. [Om du uppdaterar resursen](data-box-gateway-manage-shares.md#refresh-shares) på gatewayen rehydreras inte bloben.

Mer information finns i Hantera [Azure Blob Storage Lifecycle](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Inledande bulköverföring följt av inkrementell överföring

Använd Data Box och Data Box Gateway tillsammans när du vill göra en massöverföring av en stor mängd data följt av inkrementella överföringar. Använd databoxen för massöverföring i offlineläge (första utskick) och Data Box Gateway för inkrementella överföringar (pågående feed) över nätverket.

### <a name="seed-the-data-with-data-box"></a>Så data med databox

Följ dessa steg för att kopiera data till Data Box och ladda upp till Azure Storage.

1. [Beställ din dataruta](/azure/databox/data-box-deploy-ordered).
2. [Konfigurera datarutan](/azure/databox/data-box-deploy-set-up).
3. [Kopiera data till Data Box via SMB](/azure/databox/data-box-deploy-copy-data).
4. [Returnera datarutan, verifiera dataöverföringen till Azure](/azure/databox/data-box-deploy-picked-up).
5. När dataöverföringen till Azure är klar bör alla data finnas i Azure-lagringsbehållare. I lagringskontot för Data Box går du till behållaren Blob (och Arkiv) för att kontrollera att alla data kopieras. Anteckna behållarnamnet eftersom du använder det här namnet senare. Till exempel, i följande `databox` skärmdump, kommer behållaren att användas för den inkrementella överföringen.

    ![Behållare med data i dataruta](media/data-box-gateway-use-cases/data-container1.png)

Denna bulköverföring slutför den inledande såddfasen.

### <a name="ongoing-feed-with-data-box-gateway"></a>Pågående flöde med Data Box Gateway

Följ dessa steg för kontinuerligt inmatning av Data Box Gateway.

1. Skapa en molnresurs på Data Box Gateway. Den här resursen överför automatiskt data till Azure Storage-kontot. Gå till **Resurser** i datarutegatewayresursen och klicka på **+ Lägg till resurs**.

    ![Klicka på +Lägg till resurs](media/data-box-gateway-use-cases/add-share1.png)

2. Kontrollera att den här resursen mappar till behållaren som innehåller de utsöndrade data. För **Select blob-behållare**väljer du **Använd befintlig** och bläddra till behållaren där data från Data Box överfördes.

    ![Dela inställningar](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. När resursen har skapats uppdaterar du resursen. Den här åtgärden uppdaterar den lokala resursen med innehållet från Azure.

    ![Uppdatera resurs](media/data-box-gateway-use-cases/refresh-share1.png)

    När resursen synkroniseras överför Data Box Gateway de inkrementella ändringarna om filerna har ändrats på klienten.

## <a name="next-steps"></a>Nästa steg

- Se [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).
- Förstå [begränsningarna för Data Box Gateway](data-box-gateway-limits.md).
- Distribuera [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i Azure-portalen.