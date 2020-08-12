---
title: Namngivna entiteter för hälso vård
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122403"
---
## <a name="health-entity-categories"></a>Kategorier av hälsoentiteter:

Följande enhets kategorier returneras av [textanalys för hälso tillstånd](../../how-tos/text-analytics-for-health.md).  Observera att endast engelsk text stöds i den här förhands granskningen av behållaren och att endast en enda modell version tillhandahålls i varje behållar avbildning.

### <a name="named-entity-recognition"></a>Igenkänning av namngiven enhet

|Kategori  |Beskrivning   |
|----------|--------------|
| Ålder | Personer. Till exempel *30 år gammal*. |
| AdministrativeEvent | En administrativ händelse. |
| BodyStructure | Delar av människo kroppen, inklusive organ och andra strukturer. Till exempel *arm*eller *hjärta*. | 
| CareEnvironment | Miljön där vård eller behandling administreras. Till exempel *nöd rum* | 
| ConditionQualifier | Villkors nivåer. Till exempel *vissa*, *utökade*eller *diffusa*. | 
| Diagnostik | Medicinska villkor. Till exempel på en *spänning*. | 
| Riktning | Direction. Till exempel *vänster* eller *främre*. | 
| Läkemedels | Storlek eller kvantitet för en medicin. Till exempel *25mg*.  | 
| ExaminationName | En metod eller metod för undersökning. Till exempel *X-ray*. | 
| RelationalOperator | En operator som definierar en relation mellan två entiteter. Till exempel *mindre än*, eller `>=` .  | 
| MeasurementUnit | En mått enhet (som en procent andel). | 
| MeasurementValue | Det numeriska värdet för en mått enhet. | 
| FamilyRelation | En familial-relation. Till exempel *syster*.  | 
| Frequency | Täthet.   | 
| Kön | Kön. | 
| Släkt | En generad entitet som *TP53*.   | 
| HealthcareProfession | Metod för administrering av medicin. Till exempel *oral administration*. | 
| MedicationClass | Medicin klasser. Till exempel *antibiotika*.  | 
| MedicationForm | En form av medicin. Till exempel *kapsel*. | 
| MedicationName  | Generiskt och varumärke med namnet medications. Till exempel *ibuprofen*. | 
| MedicationRoute | Metod för administrering av medicin. Till exempel *oral administration*. | 
| SymptomOrSign  | Symptom på sjukdom. Till exempel *Sore Throat*. | 
| Tid | Angivelser. Till exempel *8 år* eller *2:10:30 den här morgonn* |
| TreatmentName  | Behandlings namn. Till exempel för *behandling*. | 
| Variant | En genetisk variant av den gen enheten. | 

### <a name="relation-extraction"></a>Relations extrahering

Relations extrahering identifierar meningsfulla anslutningar mellan begrepp som anges i text. Till exempel hittas en relation för "villkor" för att associera ett villkors namn med en tid. Textanalys för hälsa kan identifiera följande relationer:

|Kategori  |Beskrivning   |
|----------|--------------|
| DirectionOfBodyStructure | Riktning för en brödtext struktur. |
| DirectionOfCondition | Riktning för ett villkor. |
| DirectionOfExamination | Riktning för en undersökning. |
| DirectionOfTreatment | Riktning för en behandling. |
| TimeOfCondition | Tiden som är kopplad till uppställningen av ett villkor. |
| QualifierOfCondition | Den associerade kvalificeraren för ett villkor. |
| DosageOfMedication | En dosering av medicin. |
| FormOfMedication | En form av medicin. |
| RouteOfMedication | En väg eller ett läge för att konsumera en medicin. Till exempel *muntligt*. |
| FrequencyOfMedication | Den frekvens med vilken en medicin förbrukas. | 
| ValueOfCondition | Ett numeriskt värde som är kopplat till ett villkor. |
| UnitOfCondition | En enhet (till exempel tid) som är associerad med ett villkor. |
| TimeOfMedication | Tiden då en medicin förbrukades. |
| TimeOfTreatment | Tiden då en behandling administrerades. | 
| FrequencyOfTreatment | Den frekvens med vilken en behandling administreras. |
| ValueOfExamination | Ett numeriskt värde som är kopplat till en undersökning. | 
| UnitOfExamination | En enhet (till exempel en procents ATS) som är associerad med en undersökning. |
| RelationOfExamination | En relation mellan en entitet och en undersökning. | 
| TimeOfExamination | Tiden som associeras med en undersökning. |
| Förkortning | En förkortning.  | 
