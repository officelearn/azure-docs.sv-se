---
title: Microsoft Azure Data Box-enhet självhanterad leverans | Microsoft Docs i data
description: Beskriver självhanterade leverans arbets flöden för Azure Data Box enheter
services: databox
author: priestlg
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-grpr
ms.openlocfilehash: 2933eb216b7faed7b28fb53d79fec2f50bc4458d
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749419"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Använd självhanterad leverans för Azure Data Box i Azure Portal

I den här artikeln beskrivs självhanterade leverans uppgifter för att beställa, hämta och ta bort en Azure Data Box enhet. Du kan hantera Data Box-enhet-enheten med hjälp av Azure Portal.

## <a name="prerequisites"></a>Krav

Självhanterad leverans är tillgängligt som ett alternativ när du [beställer Azure Data Box](data-box-deploy-ordered.md). Självhanterad leverans är endast tillgängligt i följande regioner:

* US Government
* Västra Europa
* Japan
* Singapore
* Sydkorea

## <a name="use-self-managed-shipping"></a>Använd självhanterad leverans

När du placerar en Data Box-enhet order kan du välja alternativet för självhanterad leverans.

1. I din Azure Data Box ordning väljer du **+ Lägg till leverans adress**under **kontakt uppgifter**.
   ![Självhanterad leverans](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. När du väljer leverans typ väljer du alternativet för **själv hantering av leverans** . Det här alternativet är bara tillgängligt om du befinner dig i en region som stöds enligt beskrivningen i kraven.

3. När du har angett din leverans adress måste du verifiera den och slutföra beställningen.
   ![Självhanterad leverans](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. När enheten har förberetts kan du schemalägga en upphämtning.

   I din Azure Data Box ordning går du till **Översikt** och väljer sedan **Schemalägg hämtning**.

   ![Beställa en Data Box-enhet enhet för upphämtning](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Följ instruktionerna i **schemat för Azure**.

   Innan du kan hämta din auktoriseringskod måste du ha ett e-postmeddelande [adbops@microsoft.com](mailto:adbops@microsoft.com) för att schemalägga enheten från din regions Data Center.

   ![Boka upphämtning](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. När du har schemalagt enhets upphämtningen kan du Visa enhetens auktoriseringskod i fönstret **Schemalägg hämtning för Azure** .

   ![Visa din auktoriseringskod](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Anteckna den här **auktoriseringskod**. Enligt säkerhets kraven, vid tidpunkten för schemaläggning och inaktive ring, är det nödvändigt att presentera namnet på den person som skulle anlända för att hämta och släppa.

   Du måste också ange information om vem som ska gå till data centret för hämtning. Du eller kontakt punkten måste ha ett godkänt foto-ID för myndigheter som ska val IDE ras i data centret.

   Dessutom måste den person som söker efter enheten också ha **auktoriseringskod**. Auktoriseringskod verifieras i Data Center tiden för hämtning.

7. Din beställning flyttas automatiskt till det **plockade** läget när enheten har hämtats från data centret.

    ![Hämtat](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. När enheten har hämtats kopierar du data till Data Box-enhet på din plats. När data kopieringen är klar kan du förbereda för att leverera Data Box-enhet. Mer information finns i [Förbered för att skicka](data-box-deploy-picked-up.md#prepare-to-ship).

   **Förbered för att skicka** steget måste slutföras utan allvarliga fel, annars behöver du köra det här steget igen när du har gjort nödvändiga korrigeringar. När Förbered att leverera är slutförd kan du Visa auktoriseringskod för List rutan på enhetens lokala användar gränssnitt.

   > [!NOTE]
   > Dela inte auktoriseringskod via e-post. Detta är endast att verifieras i data centret under avlämning.

9. Om du har tagit emot en avtalad tid för inaktive ring ska ordern vara **redo att ta emot på Azure datacenter** -tillstånd i Azure Portal. Följ anvisningarna under **Schemaläggning** för att returnera enheten.

   ![Visa din auktoriseringskod](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. När ditt ID och din auktoriseringskod har verifierats och du har tappat bort enheten i data centret ska order statusen **tas emot**.

    ![Har tagits emot](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. När enheten har tagits emot fortsätter data kopieringen. När kopieringen är klar är ordern slutförd.

## <a name="next-steps"></a>Nästa steg

* [Kom igång med Azure Data Box](data-box-quickstart-portal.md)
