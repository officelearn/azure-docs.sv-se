---
title: Skicka tillbaka Microsoft Azure Data Box| Microsoft Docs
description: Lär dig hur du skickar Azure Data Box-enheten till Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/03/2018
ms.author: alkohli
ms.openlocfilehash: 7676360d71dab4da58693221645517c69b56dff8
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49090696"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Självstudie: Skicka tillbaka Azure Data Box och verifiera datauppladdning till Azure

Den här självstudien beskriver hur du returnerar Azure Data Box och verifierar data som laddats upp till Azure.

I den här självstudien får du lära dig om ämnen som:

> [!div class="checklist"]
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du se till att du har slutfört [självstudien om att kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md).

## <a name="ship-data-box-back"></a>Skicka tillbaka Data Box

1. Kontrollera att enheten är avstängd och kablarna har tagits bort. Rulla upp strömkabeln som levererades med enheten och placera kabeln säkert på baksidan av enheten.
2. Om enheten levereras i USA kontrollerar du att fraktsedeln visas på E-ink-skärmen och bokar hämtning hos ett transportföretag. Om etiketten är skadad, har tappats bort eller inte visas på E-ink-skärmen laddar du ned adressetiketten från Azure-portalen och fäster den på enheten. Öppna **Översikt > Ladda ned adressetikett**. 

    Om enheten levereras i Europa visar E-ink-skärmen inte fraktsedeln. I stället inkluderas returfraktsedeln i det genomskinliga facket under leveransfraktsedeln. Ta bort den gamla fraktsedeln och se till att fraktsedeln är väl synlig.
    
3. Om du returnerar enheten i USA bokar du upphämtning med UPS. Om du returnerar enheten i Europa via DHL bokar du upphämtning på DHL:s webbplats. Navigera till DHL-webbplatsen för ditt land. Under Våra divisioner klickar du på DHL Express. Välj **Skicka > Skicka online**. 

    Identifiera ditt fraktsedelsnummer och klicka på **Boka upphämtning**.

4. Nära Data Box-enheten hämtas och skannas av transportföretaget uppdateras orderstatusen i portalen till **hämtad**. Du får också ett spårnings-ID.

## <a name="verify-data-upload-to-azure"></a>Kontrollera datauppladdning till Azure

När Microsoft tar emot och genomsöker enheten uppdateras orderstatusen till **mottagen**. Sedan görs en fysisk kontroll av skador eller tecken på manipulation. 

När kontrollen har slutförts ansluts Data Box till nätverket i Azure-datacentret. Datakopieringen startar automatiskt. Beroende på datastorleken kan kopieringen ta några timmar upp till dagar att slutföra. Du kan övervaka kopieringsförloppet i portalen.

När kopieringen är slutförd uppdateras statusen till **slutförd**.

Kontrollera att alla data finns på lagringskontot innan du tar bort dem från källan. 

## <a name="erasure-of-data-from-data-box"></a>Radera data från Data Box
 
 När uppladdningen till Azure är klar raderar Data Box-enheten data på diskarna enligt [NIST SP 800-88 Revision 1-riktlinjerna](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi). 

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig om Azure Data Box-ämnen som att:

> [!div class="checklist"]
> * Skicka Data Box till Microsoft
> * Kontrollera datauppladdning till Azure
> * Radera data från Data Box

Gå vidare till följande artikeln om hur du hanterar Data Box via det lokala webbgränssnittet.

> [!div class="nextstepaction"]
> [Använda lokalt webbgränssnitt för att administrera Azure Data Box](./data-box-local-web-ui-admin.md)


