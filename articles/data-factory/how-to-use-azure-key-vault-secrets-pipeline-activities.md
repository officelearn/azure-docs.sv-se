---
title: Använd Azure Key Vault hemligheter i pipeline-aktiviteter
description: Lär dig hur du hämtar lagrade autentiseringsuppgifter från Azure Key Vault och använder dem under Data Factory-pipeline-körningar.
services: data-factory
author: ChrisLound
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 83d0981a0d277eab1aae8654343ab34661b3e88b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672883"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Använd Azure Key Vault hemligheter i pipeline-aktiviteter

Du kan lagra autentiseringsuppgifter eller hemliga värden i en Azure Key Vault och använda dem under pipeline-körningen för att skicka till dina aktiviteter.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här funktionen använder den hanterade identiteten för Data Factory.  Lär dig hur det fungerar från [hanterad identitet för Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) och se till att data fabriken har en associerad.

## <a name="steps"></a>Steg

1. Öppna egenskaperna för din data fabrik och kopiera ID-värdet för den hanterade identiteten.

    ![Hanterat identitets värde](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Öppna åtkomst principer för nyckel valv och Lägg till hanterade identitets behörigheter för att hämta och lista hemligheter.

    ![Key Vault åtkomst principer](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault åtkomst principer](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Klicka på **Lägg till**och sedan på **Spara**.

3. Navigera till Key Vault hemlighet och kopiera den hemliga identifieraren.

    ![Hemlig identifierare](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Anteckna din hemliga URI som du vill få under körningen av Data Factory-pipeline.

4. I Data Factory pipeline lägger du till en ny webb aktivitet och konfigurerar den enligt följande.  

    |Egenskap  |Värde  |
    |---------|---------|
    |Säkra utdata     |True         |
    |URL     |[Ditt hemliga URI-värde]? API-version = 7.0         |
    |Metod     |HÄMTA         |
    |Autentisering     |MSI         |
    |Resurs        |https://vault.azure.net       |

    ![Webb aktivitet](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Du måste lägga till **? API-version = 7.0** i slutet av din hemliga URI.  

    > [!CAUTION]
    > Ange alternativet för säkra utdata till sant för att förhindra att det hemliga värdet loggas som oformaterad text.  Alla ytterligare aktiviteter som använder det här värdet ska ha sina säkra indatatyps-alternativ inställt på sant.

5. Använd följande kod uttryck om du vill använda värdet i en annan aktivitet **@activity("webb"). output. Value)** .

    ![Kod uttryck](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Nästa steg

Information om hur du använder Azure Key Vault för att lagra autentiseringsuppgifter för data lager och beräkningar finns [i lagra autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
