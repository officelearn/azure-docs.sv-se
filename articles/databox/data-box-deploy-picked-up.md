---
title: Skicka tillbaka Microsoft Azure Data Box| Microsoft Docs
description: Lär dig hur du skickar Azure Data Box-enheten till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: alkohli
ms.openlocfilehash: 5a72bfccd69a51aa6bafb34bb768b48640e554af
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243016"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Självstudier: Skicka tillbaka Azure Data Box och verifiera datauppladdning till Azure

Den här självstudien beskriver hur du returnerar Azure Data Box och verifierar data som laddats upp till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

- Du har slutfört självstudien [: Kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md). 
- Kopieringsjobben har slutförts. Förbered för att skicka kan inte köras om kopieringsjobb pågår.

## <a name="prepare-to-ship"></a>Förbereda för att skicka

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

1. Kontrollera att enheten är avstängd och kablarna har tagits bort. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
2. Om enheten levereras i USA kontrollerar du att fraktsedeln visas på E-ink-skärmen och bokar hämtning hos ett transportföretag. Om etiketten är skadad eller tappas bort eller om den inte visas på skärmen för E-ink kontaktar du Microsoft-supporten. Gå till **Översikt > Ladda ned adressetikett** på Azure-portalen, men endast om supporten föreslår det. Ladda ned en adressetikett och fäst den på enheten.

    Om enheten levereras i Europa visar E-ink-skärmen inte fraktsedeln. I stället inkluderas returfraktsedeln i det genomskinliga facket under leveransfraktsedeln. Ta bort den gamla fraktsedeln och se till att fraktsedeln är väl synlig.
    
3. Om du returnerar enheten bokar du upphämtning med UPS. Om du vill schemalägga en upphämtning ringer du din lokala UPS (landspecifikt kostnadsfritt nummer) eller lämnar din Data Box på din närmaste samlingsplats.

4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation.

När kontrollen har slutförts ansluts Data Box till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan. När du kopierar data till Data Box laddas data beroende på typ upp till någon av följande sökvägar i ditt Azure Storage-konto.

- För blockblobar och sidblobar: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
- För Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Nödvändiga komponenter
> * Förbereda för att skicka
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

Gå vidare till följande artikeln om hur du hanterar Data Box via det lokala webbgränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)


