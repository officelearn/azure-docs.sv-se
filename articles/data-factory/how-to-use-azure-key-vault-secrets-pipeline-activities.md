---
title: Använda Azure Key Vault-hemligheter i pipeline-aktiviteter
description: Lär dig hur du hämtar lagrade autentiseringsuppgifter från Azure-nyckelvalvet och använder dem under datafabrikspipelinekörningar.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: f2531ebfd8b1eafc04fa6eda660b0eec3d1147f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417082"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Använda Azure Key Vault-hemligheter i pipeline-aktiviteter

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Du kan lagra autentiseringsuppgifter eller hemliga värden i ett Azure Key Vault och använda dem under pipelinekörning för att gå vidare till dina aktiviteter.

## <a name="prerequisites"></a>Krav

Den här funktionen är beroende av datafabrikens hanterade identitet.  Lär dig hur det fungerar från [Managed identity for Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) och se till att din datafabrik har en associerad.

## <a name="steps"></a>Steg

1. Öppna egenskaperna för datafabriken och kopiera värdet för managed identity application ID.

    ![Värdet för hanterad identitet](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. Öppna nyckelvalvets åtkomstprinciper och lägg till hanterade identitetsbehörigheter i Hämta och lista hemligheter.

    ![Principer för nyckelvalvsåtkomst](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Principer för nyckelvalvsåtkomst](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    Klicka på **Lägg till**och sedan på **Spara**.

3. Navigera till din Key Vault hemlighet och kopiera den hemliga identifieraren.

    ![Hemlig identifierare](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    Anteckna din hemliga URI som du vill få under pipelinekörningen för datafabriken.

4. Lägg till en ny webbaktivitet i pipelinen för Data Factory och konfigurera den på följande sätt.  

    |Egenskap  |Värde  |
    |---------|---------|
    |Säker utmatning     |True         |
    |URL     |[Ditt hemliga URI-värde]?api-version=7.0         |
    |Metod     |HÄMTA         |
    |Autentisering     |MSI         |
    |Resurs        |https://vault.azure.net       |

    ![Webbaktivitet](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > Du måste lägga till **?api-version=7.0** i slutet av din hemliga URI.  

    > [!CAUTION]
    > Ange alternativet Säker utdata till true för att förhindra att det hemliga värdet loggas i oformaterad text.  Alla ytterligare aktiviteter som förbrukar det här värdet bör ha alternativet Säker inmatning inställd på true.

5. Om du vill använda värdet i en annan aktivitet använder du följande koduttryck ** @activity('Web1").ex.value**.

    ![Koduttryck](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Key Vault för att lagra autentiseringsuppgifter för datalager och beräkningar finns [i Lagra autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
