---
title: Microsoft Azure Data Box Gateway usecases | Microsoft Docs
description: Beskriver usecases för Azure Data Box-Gateway, en virtuell installation lagringslösning som gör det möjligt att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 01/17/2019
ms.author: alkohli
ms.openlocfilehash: d1367504182eb8d8335796dc37800c30e0a563b8
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438600"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Användningsfall för Azure Data Box-Gateway

Azure Data Box-Gateway är en cloud storage-gatewayenhet som finns lokalt och skickar din avbildning, media och andra data till Azure. Den här cloud storage-gateway är en virtuell dator som etablerats i din hypervisor-program. Du kan skriva data till den här virtuella enheten med NFS och SMB-protokoll, som sedan skickas till Azure. Den här artikeln innehåller en detaljerad beskrivning av de scenarier där du kan distribuera den här enheten.

Använd Data Box-Gateway för följande scenarier:

- Mata in stora mängder data kontinuerligt.
- För arkivering av data i ett säkert och effektivt sätt.
- För att överföra inkrementella data över nätverket efter den första stora överföringen är klar med hjälp av Data Box.

Var och en av dessa scenarier beskrivs i detalj i följande avsnitt.

> [!IMPORTANT]
> Data Box Gateway är en förhandsversion. Läs [användningsvillkoren för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen.

## <a name="continuous-data-ingestion"></a>Kontinuerlig datainmatning

En av de viktigaste fördelarna med Data Box-Gateway är möjligheten att mata in data i enheten för att kopiera till molnet, oavsett storleken på data kontinuerligt.

När data skrivs till gateway-enheten, överför data till Azure Storage med hjälp av enheten. Enheten hanterar lagringen automatiskt genom att ta bort filerna lokalt samtidigt behålla metadata när den når ett visst tröskelvärde. Att hålla en lokal kopia av metadata kan gateway-enhet att ladda upp ändringarna endast när filen har uppdaterats. Data som överförts till din gatewayenhet ska vara enligt riktlinjerna i [dataöverföring varningar](data-box-gateway-limits.md#data-upload-caveats).

Den startar när enheten fylls med data, begränsning ingress-frekvens (om det behövs) för att matcha den hastighet med vilken data har överförts till molnet. För att övervaka kontinuerlig datainmatningen på enheten, kan du använda aviseringar. Aviseringarna aktiveras när begränsningen startas och raderas när den begränsning har stoppats.

## <a name="cloud-archival-of-data"></a>Molnet arkivering av data

Använd Data Box Gateway när du vill behålla dina data för långsiktig i molnet. Du kan använda den **Arkiv** lagringsnivå för långsiktig kvarhållning.

Arkivnivån optimeras som sällan lagrar data i minst 180 dagar. Den **Arkiv** nivå erbjuder de lägsta lagringskostnaderna men har de högsta åtkomstkostnaderna. Mer information går du till [arkivåtkomstnivå](/azure/storage/blobs/storage-blob-storage-tiers#archive-access-tier).

### <a name="move-data-to-archive-tier"></a>Flytta data till arkivnivån

Innan du börjar måste du kontrollera att du har en aktiv gatewayenhet för Data Box. Följ stegen som beskrivs i [självstudien: Förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md) och hålla avancera till nästa självstudie förrän du har en operational enhet.

- Använda Data Box-Gateway-enheten för att ladda upp data till Azure via vanliga överföringen enligt beskrivningen i [överföra data via rutan Datagateway](data-box-gateway-deploy-add-shares.md).
- När data har överförts kommer du behöva flytta den till arkivnivån. Du kan ange blob-nivå på två sätt: Azure PowerShell-skript eller en princip för livscykelhantering för Azure Storage.  
    - Om du använder Azure PowerShell, följer du dessa [steg](/azure/databox/data-box-how-to-set-data-tier#use-azure-powershell-to-set-the-blob-tier) att flytta data till arkivnivån.
    - Om du använder Azure livscykelhantering, Följ stegen nedan för att flytta data till arkivnivån.
        - [Registrera](/azure/storage/common/storage-lifecycle-management-concepts#register-for-preview) förhandsversionen av Blob Lifecycle management-tjänsten att använda arkivet nivå.
        - Använd följande princip att [Arkivera data på mata in](/azure/storage/blobs/storage-lifecycle-management-concepts#archive-data-at-ingest).
- När blobarna som är markerade som Arkiv, kan de inte längre ändras med gatewayen flytta varma eller kalla nivån. Om filen finns i den lokala lagringen, överförs inte ändringar som görs i den lokala kopian (inklusive borttagningar) för att arkivera nivå.
- Om du vill läsa data i arkivlagring, som ska extraheras genom att ändra blob-nivå för att frekvent eller lågfrekvent. [Uppdatera resursen](data-box-gateway-manage-shares.md#refresh-shares) på gatewayen inte rehydrate blob.

Lär dig mer om hur du vill veta mer [hantera Azure Blob-Lagringslivscykeln](/azure/storage/common/storage-lifecycle-management-concepts).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Inledande bulk överföring följt av inkrementella överföringen

Använd tillsammans Data och Data Box Gateway när du vill göra en bulköverföring av en stor mängd data följt av inkrementella överföringar. Använd Data Box för bulk-överföringen i en offline-läge (inledande seed) och Data Box-Gateway för inkrementella överföringar (pågående matning) över nätverket.

### <a name="seed-the-data-with-data-box"></a>Mata in data med Data Box

Följ stegen nedan för att kopiera data till Data Box och överföra den till Azure Storage.

1. [Beställa Data Box](/azure/databox/data-box-deploy-ordered).
2. [Konfigurera din Data Box](/azure/databox/data-box-deploy-set-up).
3. [Kopiera data till Data Box via SMB](/azure/databox/data-box-deploy-copy-data).
4. [Returnera Data Box, kontrollera ladda upp data till Azure](/azure/databox/data-box-deploy-picked-up).
5. När ladda upp data till Azure är klar ska alla data i Azure storage-behållare. I storage-konto för Data Box går du till behållaren Blob (och fil) för att se till att alla data har kopierats. Notera namnet på behållaren som du använder det här namnet senare. Exempel på följande skärmbild `databox` behållare kommer att användas för den inkrementella överföringen.

    ![Behållare med data på Data Box](media/data-box-gateway-use-cases/data-container1.png)

Den här bulk-överföringen Slutför den inledande seeding fasen.

### <a name="ongoing-feed-with-data-box-gateway"></a>Pågående feed med Data Box-Gateway

Följ dessa steg för pågående inmatning av Data Box-gatewayen.

1. Skapa en cloud-resurs på Data Box-Gateway. Den här resursen Överför automatiskt data till Azure Storage-kontot. Gå till **resurser** i din Data Box Gateway-resursen och klicka på **+ Lägg till resurs**.

    ![Klicka på + Lägg till resurs](media/data-box-gateway-use-cases/add-share1.png)

2. Kontrollera att den här resursen som mappar till den behållare som innehåller förpopulerad data. För **väljer blobbehållare**, Välj **Använd befintlig** och bläddra till den behållare där data från Data Box har överförts.

    ![Resursinställningar](media/data-box-gateway-use-cases/share-settings-select-existing-container1.png)

3. När resursen har skapats kan du uppdatera resursen. Den här åtgärden uppdaterar en lokal resurs med innehåll från Azure.

    ![Uppdatera resurs](media/data-box-gateway-use-cases/refresh-share1.png)

    När resursen har synkroniserats Data Box-Gateway kommer att överföra de inkrementella ändringarna om filerna som har ändrats på klienten.

## <a name="next-steps"></a>Nästa steg

- Se [systemkraven för Data Box Gateway](data-box-gateway-system-requirements.md).
- Förstå [begränsningarna för Data Box Gateway](data-box-gateway-limits.md).
- Distribuera [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) i Azure-portalen.