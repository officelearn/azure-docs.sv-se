---
title: Köra exporten genom att anropa $export kommandot på Azure API för FHIR
description: Den här artikeln beskriver hur du konfigurerar och använder avidentifierad export
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ecc2134d1a528ee22710cb447f996e0c5e31a8de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308188"
---
# <a name="how-to-export-fhir-data"></a>Så här exporterar du FHIR-data

Innan du använder $export bör du se till att Azure API för FHIR är konfigurerat för att använda det. Information om hur du konfigurerar export inställningar och skapar Azure Storage-konton finns på [sidan Konfigurera export data](configure-export-data.md).

## <a name="using-export-command"></a>Använda $export kommandot

När du har konfigurerat Azure API för FHIR för export kan du använda kommandot $export för att exportera data från tjänsten. Data lagras i det lagrings konto du angav när du konfigurerade exporten. Om du vill veta mer om hur du anropar $export kommandot i FHIR-servern läser du dokumentationen på [$export specifikationen](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

$Export kommandot i Azure API för FHIR använder en valfri _ \_ container_ parameter som anger behållaren i det konfigurerade lagrings kontot där data ska exporteras.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Scenarier som stöds

Azure API för FHIR har stöd för $export på system-, patient-och grupp nivå. Vid grupp export exporterar vi alla relaterade resurser men exporterar inte egenskaperna för gruppen.

> [!Note] 
> $export kommer att exportera dubbla resurser om resursen finns i ett utrymme med fler än en resurs eller finns i flera grupper.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du exporterar FHIR-resurser med hjälp av kommandot $export. Härnäst kan du se vilka funktioner som stöds
 
>[!div class="nextstepaction"]
>[Funktioner som stöds](fhir-features-supported.md)
