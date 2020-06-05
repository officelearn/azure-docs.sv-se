---
title: Funktionalisering i AutoML experiment
titleSuffix: Azure Machine Learning
description: Lär dig mer om funktionalisering-inställningar Azure Machine Learning erbjudanden och hur funktionen kan användas i automatiserade ml-experiment.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: c81e0a71b23e865ca2938f5fbf3c73cdb5c3aeb1
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434305"
---
# <a name="featurization-with-automated-machine-learning"></a>Funktionalisering med automatisk maskin inlärning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här hand boken får du lära dig vilka funktionalisering-inställningar som erbjuds och hur du anpassar dem för dina [automatiserade maskin inlärnings experiment](concept-automated-ml.md).

Funktions teknik är en process där du använder domän information om data för att skapa funktioner som hjälper ML-algoritmer att lära sig bättre. I Azure Machine Learning används metoder för data skalning och normalisering för att under lätta funktioner. Tillsammans kallas dessa tekniker och funktions teknik funktionalisering i Automatisk maskin inlärning, AutoML, experiment.

Den här artikeln förutsätter att du redan är bekant med hur du konfigurerar ett AutoML experiment. Se följande artiklar för mer information

* För en kod första upplevelsen: [Konfigurera automatiserade ml-experiment med python SDK](how-to-configure-auto-train.md).
* För en låg kod upplevelse: [skapa, granska och distribuera automatiserade maskin inlärnings modeller med Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>Konfigurera funktionalisering

I varje automatiserad maskin inlärnings experiment tillämpas [automatiska skalnings-och normaliserings metoder](#featurization) på dina data som standard. Dessa metoder för skalning och normalisering är typer av funktionalisering som hjälper *vissa* algoritmer som är känsliga för funktioner i olika skalor. Du kan dock också aktivera ytterligare funktionalisering, t. ex. **saknade värden Imputation, encoding** och **transformationer**.

> [!NOTE]
> Automatiserade funktionalisering-steg för Machine Learning (funktions normalisering, hantering av data som saknas, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpades under träningen på dina indata automatiskt.

För experiment som kon figurer ATS med SDK kan du aktivera/inaktivera inställningen `featurization` och ytterligare ange de funktionalisering-steg som ska användas för experimentet. Om du använder Azure Machine Learning Studio kan du läsa om hur du aktiverar funktionalisering [med de här stegen](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

I följande tabell visas de accepterade inställningarna för `featurization` i [klassen AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Funktionalisering-konfiguration | Beskrivning|
------------- | ------------- |
|**`"featurization": 'auto'`**| Anger att [data guardrails och funktionalisering-steg](#featurization) utförs automatiskt när en del av förbearbetningen. **Standardinställning**.|
|**`"featurization": 'off'`**| Anger att funktionalisering-steg inte ska utföras automatiskt.|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| Anger att det anpassade funktionalisering-steget ska användas. [Lär dig hur du anpassar funktionalisering](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatisk funktionalisering

I följande tabell sammanfattas de tekniker som automatiskt tillämpas på dina data. Detta inträffar för experiment som kon figurer ATS via SDK eller Studio. Om du vill inaktivera det här beteendet ställer `"featurization": 'off'` du in i `AutoMLConfig` objektet.

> [!NOTE]
> Om du planerar att exportera dina Auto ML-skapade modeller till en [ONNX-modell](concept-onnx.md), stöds bara de funktionalisering-alternativ som anges med * i ONNX-formatet. Lär dig mer om [att konvertera modeller till ONNX](concept-automated-ml.md#use-with-onnx). 

|Funktionalisering- &nbsp; steg| Beskrivning |
| ------------- | ------------- |
|**Släpp hög kardinalitet eller inga varians funktioner*** |Ta bort dessa från inlärnings-och validerings uppsättningar, inklusive funktioner med alla värden som saknas, samma värde för alla rader eller med hög kardinalitet (till exempel hash-värden, ID: n eller GUID).|
|**Imputerade värden som saknas*** |För numeriska funktioner måste du räkna ut med medelvärdet av värdena i kolumnen.<br/><br/>För kategoriska-funktioner ska du räkna med det mest frekventa värdet.|
|**Generera ytterligare funktioner*** |För DateTime-funktioner: år, månad, dag, veckodag, dag på år, kvartal, vecka på år, timme, minut och sekund.<br/><br/>För text funktioner: term frekvens baserat på unigrams, bi-gram och Tri-Character-gram.|
|**Transformera och koda***|Numeriska funktioner med få unika värden omvandlas till kategoriska-funktioner.<br/><br/>En-frekvent kodning utförs för kategoriska med låg kardinalitet. för hög kardinalitet, en-frekvent-hash-kodning.|
|**Word-inbäddningar**|Text upplärda som konverterar vektorer med text-token till menings vektorer med en förtränad modell. Varje ords inbäddnings vektor i ett dokument sammanställs tillsammans för att skapa en dokument funktions vektor.|
|**Mål kodningar**|För kategoriska-funktioner, mappar varje kategori med genomsnittligt målvärde för Regressions problem och till sannolikheten för varje klass för klassificerings problem. Frekvens-baserad viktning och mellanliggande kors validering används för att minska överbelastningen av mappningen och bruset som orsakas av glesa data kategorier.|
|**Kodning av text mål**|För text inmatare används en staplad linjär modell med ord uppsättnings ord för att generera sannolikheten för varje klass.|
|**Vikt på bevis (WoE)**|Beräknar WoE som ett mått på korrelation av kategoriska-kolumner till mål kolumnen. Det beräknas som loggen för förhållandet mellan sannolikheten i förhållande till inaktuella klasser. Det här steget matar ut en numerisk funktions kolumn per klass och tar bort behovet av att explicit kräva att värden som saknas och avvikare behandlas.|
|**Kluster avstånd**|Tågen a k: kluster modell på alla numeriska kolumner.  Utdata k nya funktioner, en ny numerisk funktion per kluster, som innehåller avståndet från varje exempel till centroid för varje kluster.|

## <a name="data-guardrails"></a>Data guardrails

Med data guardrails kan du identifiera potentiella problem med dina data (t. ex. saknade värden, [klass obalans](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)) och hjälpa till att vidta korrigerande åtgärder för förbättrade resultat. 

Data guardrails tillämpas 

* **För SDK-experiment**, när antingen parametrarna `"featurization": 'auto'` eller `validation=auto` anges i `AutoMLConfig` objektet.
* **För Studio experiment**när *Automatisk funktionalisering* har Aktiver ATS.  

Du kan granska data guardrails som hör till experimentet

* Genom att ange `show_output=True` när du skickar ett experiment med python SDK.

* I Studio på fliken **data guardrails** i AUTOMATISERAd ml-körning.

### <a name="data-guardrail-states"></a>Data guardrail-tillstånd

Data guardrails visar ett av tre tillstånd: **godkänd**, **klar**eller **alertd**.

|Stat| Beskrivning |
|----|---- |
|**Parametrarna**| Inga data problem upptäcktes och ingen användar åtgärd krävs. |
|**Klar**| Ändringarna tillämpades på dina data. Vi rekommenderar att användarna granskar korrigerings åtgärderna automatiserade ML för att se till att ändringarna överensstämmer med de förväntade resultaten. |
|**Aviserad**| Ett data problem som inte kunde åtgärdas har upptäckts. Vi uppmuntrar användare att ändra och åtgärda problemet.| 

I följande tabell beskrivs de data guardrails som stöds för närvarande och de associerade statusvärdena som användarna kan komma att komma åt när de skickar experimentet.

Guardrail|Status|Villkor &nbsp; för &nbsp; utlösare
---|---|---
**Funktions värden saknas Imputation** |*Parametrarna* <br><br><br> *Klar*| Inga funktions värden som saknas hittades i dina tränings data. Läs mer om det [saknade värdet Imputation.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Funktions värden som saknas upptäcktes i dina tränings data och tillräknades.
**Funktions hantering med hög kardinalitet** |*Parametrarna* <br><br><br> *Klar*| Dina indata analyserades och inga funktioner för hög kardinalitet upptäcktes. Lär dig mer om [funktions identifiering med hög kardinalitet.](#automatic-featurization) <br><br> Funktioner för hög kardinalitet upptäcktes i dina indata och hanterades.
**Verifiering av delad hantering** |*Klar*| Validerings konfigurationen har ställts in på Auto och tränings data innehöll **färre än 20 000 rader**. <br> Varje iteration av den tränade modellen verifierades genom kors validering. Läs mer om [verifierings data.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> Validerings konfigurationen har ställts in på Auto och tränings data innehöll **mer än 20 000 rader**. <br> Indata har delats in i en tränings data uppsättning och en validerings data uppsättning för att verifiera modellen.
**Identifiering av klass balansering** |*Parametrarna* <br><br><br><br><br> *Aviserad* | Dina indata analyserades och alla klasser är balanserade i dina tränings data. En data mängd betraktas som balanserade om varje klass har god representation i data uppsättningen, mätt enligt antal och samplings förhållandet. <br><br><br> Obalanserade klasser upptäcktes i dina indata. Åtgärda problem med balanseringen om du vill åtgärda modell kompensation. Läs mer om [obalanserade data.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
**Identifiering av minnes problem** |*Parametrarna* <br><br><br><br> *Klar* |<br> Det valda {horisont-, fördröjnings-, rullande Window}-värdet har analyser ATS och inga potentiella minnes problem har identifierats. Lär dig mer om [Prognosticering-konfigurationer](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) för tids serier. <br><br><br>De valda värdena för {Horisont, fördröjning, rullande fönster} analyserades och kan orsaka att experimentet får slut på minne. Inställningarna för fördröjningen eller rullande fönster har inaktiverats.
**Frekvens identifiering** |*Parametrarna* <br><br><br><br> *Klar* |<br> Tids serien analyserades och alla data punkter justeras med den frekvens som upptäcktes. <br> <br> Tids serien analyserades och data punkter som inte överensstämmer med den identifierade frekvensen upptäcktes. Dessa data punkter togs bort från data uppsättningen. Lär dig mer om [förberedelse av data för tids serie prognoser.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="customize-featurization"></a>Anpassa funktionalisering

Du kan anpassa dina funktionalisering-inställningar för att säkerställa att de data och funktioner som används för att träna din ML-modell leder till relevanta förutsägelser. 

Om du vill anpassa featurizations anger du  `"featurization": FeaturizationConfig` i ditt `AutoMLConfig` objekt. Om du använder Azure Machine Learning Studio för experimentet, se [instruktionen How-to](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Anpassning som stöds omfattar:

|Anpassning|Definition|
|--|--|
|**Uppdatering av kolumn syfte**|Åsidosätt funktions typ för den angivna kolumnen.|
|**Transformering av parameter uppdatering** |Uppdatera parametrar för den angivna transformeraren. Stöder för närvarande imputerade (medel, de vanligaste & median) och HashOneHotEncoder.|
|**Släpp kolumner** |Kolumner att släppa från bearbetas.|
|**Block transformatorer**| Block transformatorer som ska användas för funktionalisering-processen.|

Skapa FeaturizationConfig-objektet med API-anrop:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```
## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar dina automatiserade ML-experiment,

    * För kod upplevelse kunder: [Konfigurera automatiserade ml-experiment med Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * För kunder med låg/ingen kod upplevelse: [skapa dina automatiserade maskin inlärnings experiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

* Lär dig mer om [hur du tränar en Regressions modell med automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar användning av automatiserad maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).
