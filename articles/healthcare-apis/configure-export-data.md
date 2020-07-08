---
title: Konfigurera export inställningar i Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar export inställningar i Azure API för FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871808"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Konfigurera export inställning och exportera data till ett lagrings konto

Azure API för FHIR stöder $export kommandot som gör att du kan exportera data från Azure API för FHIR-konto till ett lagrings konto.

Det finns fyra steg som ingår i att utföra export i Azure API för FHIR:

1. Aktivera hanterad identitet på Azure API för FHIR-tjänsten
2. Skapa ett Azure Storage-konto (om det inte gjorts tidigare) och tilldela behörighet till Azure API för FHIR till lagrings kontot
3. Välja lagrings kontot i Azure API för FHIR som export Storage-konto
4. Köra exporten genom att anropa $export kommandot på Azure API för FHIR

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Aktivera hanterad identitet på Azure API för FHIR

Det första steget i att konfigurera Azure API för FHIR för export är att aktivera systemomfattande hanterad identitet för tjänsten. Du kan läsa mer om hanterade identiteter i Azure [här](../active-directory/managed-identities-azure-resources/overview.md).

Det gör du genom att gå till Azure API för FHIR-tjänsten och välja identitets blad. Genom att ändra status till på aktive ras hanterad identitet i Azure API för FHIR-tjänsten.

![Aktivera hanterad identitet](media/export-data/fhir-mi-enabled.png)

Nu kan vi gå vidare till nästa steg och skapa ett lagrings konto och tilldela behörighet till vår tjänst.

## <a name="adding-permission-to-storage-account"></a>Lägger till behörighet till lagrings kontot

Nästa steg i exporten är att tilldela behörighet för Azure API för FHIR-tjänsten att skriva till lagrings kontot.

När vi har skapat ett lagrings konto navigerar du till bladet Access Control (IAM) i lagrings kontot och väljer Lägg till roll tilldelningar

![Aktivera hanterad identitet](media/export-data/fhir-export-role-assignment.png)

Här lägger vi till roll lagrings data deltagare i vårt tjänst namn.

![Aktivera hanterad identitet](media/export-data/fhir-export-role-add.png)

Nu är vi redo för nästa steg där vi kan välja lagrings kontot i Azure API för FHIR som ett standard lagrings konto för $export.

## <a name="selecting-the-storage-account-for-export"></a>Välja lagrings konto för $export

Sista steget innan du anropar $export kommandot är att tilldela det Azure Storage-konto som Azure API för FHIR använder för att exportera data till. Det gör du genom att gå till integrations bladet i Azure API for FHIR service i Azure Portal och välja lagrings kontot 

![Aktivera hanterad identitet](media/export-data/fhir-export-storage.png)

När vi är redo att exportera data med hjälp av kommandot $export.

## <a name="exporting-the-data-using-export-command"></a>Exportera data med hjälp av kommandot $export

När vi har konfigurerat Azure API för FHIR för export kan vi nu gå och använda kommandot $export för att exportera data från tjänsten till det lagrings konto som vi har angett. Information om hur du anropar $export kommandot på FHIR-servern finns i dokumentationen om $export specifikation på[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Observera att Azure API för FHIR endast stöder export på system nivå enligt definitionen i export specifikationen på [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Vi stöder för närvarande inte frågeparametrar med $export.

>[!div class="nextstepaction"]
>[Ytterligare inställningar](azure-api-for-fhir-additional-settings.md)