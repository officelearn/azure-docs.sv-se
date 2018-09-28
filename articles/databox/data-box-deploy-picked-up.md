---
title: Skicka tillbaka Microsoft Azure Data Box| Microsoft Docs
description: Lär dig hur du skickar Azure Data Box-enheten till Microsoft
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: d649095a6b1b9f692a6795e96c9f15631d36e3e2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974518"
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
2. Kontrollera att adressetiketten visas på E-ink-skärmen och boka hämtning hos ett transportföretag. Om etiketten är skadad, har tappats bort eller inte visas på E-ink-skärmen laddar du ned adressetiketten från Azure-portalen och fäster den på enheten. Öppna **Översikt > Ladda ned adressetikett**.
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


