---
title: Namngivna entiteter för hälso vård
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108521"
---
## <a name="health-entity-categories"></a>Kategorier av hälsoentiteter:

Följande enhets kategorier returneras av [textanalys för hälso tillstånd](../../how-tos/text-analytics-for-health.md).  Observera att endast engelsk text stöds i den här förhands granskningen av behållaren och att endast en enda modell version tillhandahålls i varje behållar avbildning.

### <a name="named-entity-recognition"></a>Igenkänning av namngiven enhet

|Kategori  |Description   |
|----------|--------------|
| TIDS | Personer. |
| BODY_STRUCTURE | Delar av människo kroppen, inklusive organ och andra strukturer. | 
| CONDITION_QUALIFIER | Villkors nivåer som till exempel *vissa*, *utökade*eller *diffusa*. | 
| DIAGNOSTIK | Medicinska villkor. Till exempel på en *spänning* . | 
| POSITION | Riktningar som till exempel *vänster* eller *främre*. | 
| LÄKEMEDELS | Storlek eller kvantitet för en medicin.  | 
| EXAMINATION_NAME | En metod eller metod för undersökning. | 
| EXAMINATION_RELATION | en associering mellan en mått enhet och en undersökning.  | 
| EXAMINATION_UNIT | En mått enhet för en undersökning. | 
| EXAMINATION_VALUE | Värdet för kontroll mått enheten. | 
| FAMILY_RELATION | En familial-relation, till exempel *syster*.  | 
| FREKVENS | Täthet.   | 
| KÖN | Kön. | 
| SLÄKT | En generad entitet som *TP53*.   | 
| MEDICATION_CLASS | Medicin klasser. Till exempel *antibiotika*.  | 
| MEDICATION_NAME  | Generiskt och varumärke med namnet medications.| 
| ROUTE_OR_MODE  | Metod för administrering av medicin. | 
| SYMPTOM_OR_SIGN  | Symptom på sjukdom. | 
| TIME  | Angivelser. Till exempel "8 år" eller "2:10:30 denna morgon" |
| TREATMENT_NAME  | Behandlings namn. | 
| PRODUKTVARIANTEN  | En genetisk variant av gen enheten | 

### <a name="relation-extraction"></a>Relations extrahering

Relations extrahering identifierar meningsfulla anslutningar mellan begrepp som anges i text. Till exempel hittas en relation för "villkor" för att associera ett villkors namn med en tid. Textanalys för hälsa kan identifiera följande relationer:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* FÖRKORTNING 
