---
title: Kopiera eller klona en datafabrik i Azure Data Factory
description: Lär dig hur du kopierar eller klonar en datafabrik i Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414109"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiera eller klona en datafabrik i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs hur du kopierar eller klonar en datafabrik i Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Användningsfall för kloning av en datafabrik

Här är några av de omständigheter under vilka du kan finna det lämpligt att kopiera eller klona en datafabrik:

-   **Byta namn på resurser**. Azure stöder inte att du byter namn på resurser. Om du vill byta namn på en datafabrik kan du klona datafabriken med ett annat namn och sedan ta bort den befintliga.

-   **Felsökningen ändras** när felsökningsfunktionerna inte är tillräckliga. Ibland för att testa dina ändringar, kanske du vill testa dina ändringar i en annan fabrik innan du använder dem på din huvudsakliga. I de flesta fall kan du använda Felsökning. Ändringar i utlösare, till exempel hur dina ändringar beter sig när en utlösare anropas automatiskt, eller under ett tidsfönster, kanske inte kan testas lätt utan att checka in. I dessa fall, kloning av fabriken och tillämpa dina ändringar där gör en hel del mening. Eftersom Azure Data Factory debiteras främst av antalet körningar, leder den andra fabriken inte till några ytterligare avgifter.

## <a name="how-to-clone-a-data-factory"></a>Så här klonar du en datafabrik

1. Med datafabriksgränssnittet i Azure-portalen kan du exportera hela nyttolasten för datafabriken till en Resource Manager-mall, tillsammans med en parameterfil som gör att du kan ändra alla värden som du vill ändra när du klonar din fabrik.

1. Som en förutsättning måste du skapa din måldatafabrik från Azure-portalen.

1. Om du har en SelfHosted IntegrationRuntime i källfabriken måste du förskapa den med samma namn i målfabriken. Om du vill dela SelfHosted IRs mellan olika fabriker, kan du använda mönstret publiceras [här](source-control.md#best-practices-for-git-integration).

1. Om du är i GIT-läge sparas fabrikens Resource Manager-mall i GIT i databasens adf_publish gren varje gång du publicerar från portalen.

1. För andra scenarier kan Resource Manager-mallen hämtas genom att klicka på **mallknappen Exportera Resurshanteraren** i portalen.

1. När du har hämtat Resource Manager-mallen kan du distribuera den via standarddistributionsmetoder för Resource Manager-malldistribution.

1. Av säkerhetsskäl innehåller den genererade Resource Manager-mallen ingen hemlig information, till exempel lösenord för länkade tjänster. Därför måste du ange dessa lösenord som distributionsparametrar. Om det inte är önskvärt att tillhandahålla parametrar måste du hämta anslutningssträngar och lösenord för de länkade tjänsterna från Azure Key Vault.

## <a name="next-steps"></a>Nästa steg

Läs vägledningen för att skapa en datafabrik i Azure-portalen i [Skapa en datafabrik med hjälp av Azure Data Factory UI](quickstart-create-data-factory-portal.md).
