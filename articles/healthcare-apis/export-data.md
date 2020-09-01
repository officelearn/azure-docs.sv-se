---
title: Köra exporten genom att anropa $export kommandot på Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar och använder avidentifierad export
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 10d901f73006051e8b1ddd02aeb36b229c6a7761
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270286"
---
# <a name="how-to-export-fhir-data"></a>Så här exporterar du FHIR-data

Om du vill konfigurera export inställningar och skapa Azure Storage-konto kan du läsa [här](configure-export-data.md).

## <a name="exporting-fhir-resources-using-export-command"></a>Exportera FHIR-resurser med hjälp av kommandot $export

När vi har konfigurerat Azure API for FHIR för export kan vi nu gå och använda kommandot $export för att exportera data från tjänsten till det lagrings konto som vi angav när du konfigurerade exporten. Information om hur du anropar $export kommandot på FHIR-servern finns i dokumentationen om $export specifikation på [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

$Export kommandot i Azure API för FHIR använder en valfri _ \_ Conatiner_ -parameter som kan användas för att ange behållaren i det konfigurerade lagrings kontot som data ska exporteras till.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Observera att Azure API för FHIR endast stöder export på system nivå enligt definitionen i $export specifikationen på [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Även _ \_ eftersom_ Frågeparametern stöds för närvarande.

## <a name="exporting-de-identified-data-preview"></a>Exportera avidentifierade data (för hands version)

Kommandot $export kan också användas för att exportera de data som identifieras från FHIR-servern. Den använder anonymisering-motorn från [FHIR-verktyg för anonymisering](https://github.com/microsoft/FHIR-Tools-for-Anonymization)och tar anonymisering konfigurations information i frågeparametrar. Du kan skapa en egen anonymisering-konfigurationsfil eller använda [exempel konfigurations filen](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) för HIPAA Safe Harbor-metoden som en start punkt. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Frågeparameter            | Exempel |Valfrihet| Beskrivning|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jspå|Krävs för avidentifierad export |Namn på konfigurations filen. Se konfigurations fil formatet [här](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Den här filen bör behållas i en behållare med namnet **anonymisering** inom samma Azure Storage-konto som har kon figurer ATS som export plats. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Valfritt för avidentifierad export|Detta är etag i konfigurations filen. Du kan hämta etag med Azure Storage Explorer från BLOB-egenskapen|

> [!IMPORTANT]
> Observera att både rå export och de avidentifierade export skrivningar till samma Azure Storage-konto som anges i export konfigurationen. Vi rekommenderar att du använder olika behållare som motsvarar olika avidentifierade konfigurations-och hanterings användar åtkomst på behållar nivån.
