---
title: Kopiera eller klona en datafabrik i Azure Data Factory | Microsoft Docs
description: Lär dig hur du kopiera eller klona en datafabrik i Azure Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: c62581447cd395bd48a787fa7dc89659d5172486
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192302"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopiera eller klona en datafabrik i Azure Data Factory

Den här artikeln beskriver hur du kopiera eller klona en datafabrik i Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Användningsområden för kloning av en data factory

Här följer några omständigheter där det kan vara bra att kopiera eller klona en data factory:

-   **Byta namn på resurser**. Azure stöder inte byta namn på resurser. Om du vill byta namn på en data factory kan du klona datafabriken med ett annat namn och ta sedan bort den befintliga påverkas.

-   **Felsöka ändringar** när debug-funktioner inte är tillräckligt. Ibland för att testa ändringarna, kan du testa dina ändringar i en annan fabrik innan du tillämpar dem till den huvudsakliga. I de flesta fall kan du använda Debug. Ändringar i utlösare, men som hur ändringarna beter sig när en utlösare anropas automatiskt, eller via ett tidsfönster kanske inte kan testas enkelt utan att checka in. I dessa fall kan gör kloning fabriken och tillämpa ändringarna och det du klokt. Eftersom Azure Data Factory kostnader främst genom antal körningar, leder den andra fabriken inte till ytterligare avgifter.

## <a name="how-to-clone-a-data-factory"></a>Hur du klonar en data factory

1. Användargränssnittet för Data Factory i Azure-portalen kan du exportera hela nyttolasten för din datafabrik i Resource Manager-mall, tillsammans med en parameterfil som du kan ändra alla värden som du vill ändra när du klonar din datafabrik.

1. Som ett krav måste du skapa datafabriken mål från Azure-portalen.

1. Om du använder GIT-läge, varje gång du publicerar från portalen kan sparas den fabriken Resource Manager-mall till GIT i grenen adf_publish av databasen.

1. För andra scenarier, Resource Manager-mall kan hämtas genom att klicka på den **exportera Resource Manager-mall** -knappen i portalen.

1. När du har hämtat Resource Manager-mall kan du distribuera den via standard distributionsmetoder för Resource Manager-mall.

1. Av säkerhetsskäl innehåller genererade Resource Manager-mallen inte någon hemlig information, till exempel lösenord för länkade tjänster. Du måste därför ange lösenorden som parametrar för distribution. Du måste hämta anslutningssträngar och lösenorden för de länkade tjänsterna från Azure Key Vault om att ange parametrar inte är önskvärt.

## <a name="next-steps"></a>Nästa steg

Läser du igenom informationen för att skapa en datafabrik i Azure-portalen i [skapa en datafabrik med hjälp av Användargränssnittet för Azure Data Factory](quickstart-create-data-factory-portal.md).
