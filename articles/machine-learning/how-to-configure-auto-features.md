---
title: Funktionalisering i AutoML experiment
titleSuffix: Azure Machine Learning
description: Lär dig vilka funktionalisering-inställningar Azure Machine Learning erbjudanden och hur funktions teknik stöds i automatiserade ML-experiment.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: aa348728cd4e9ac0ce5d70cb293ac850cc549666
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817135"
---
# <a name="featurization-in-automated-machine-learning"></a>Funktionalisering i Automatisk maskin inlärning

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här guiden får du lära dig:

- Vilka funktionalisering-inställningar Azure Machine Learning erbjudanden.
- Så här anpassar du funktionerna för dina [automatiserade maskin inlärnings experiment](concept-automated-ml.md).

*Funktions teknik* är en process där du använder domän information om data för att skapa funktioner som hjälper Machine Learning-algoritmer (ml) att lära sig bättre. I Azure Machine Learning tillämpas teknikerna för data skalning och normalisering för att göra det enklare att använda funktionen. Tillsammans kallas dessa tekniker och den här typen av teknik för *funktionalisering* i automatiserad maskin inlärning eller *AutoML*, experiment.

Den här artikeln förutsätter att du redan vet hur du konfigurerar ett AutoML experiment. Information om konfiguration finns i följande artiklar:

- För en kod-första upplevelse: [Konfigurera automatiserade ml-experiment med hjälp av Azure Machine Learning SDK för python](how-to-configure-auto-train.md).
- För att få en kod med låg kod eller ingen kod: [skapa, granska och distribuera automatiserade maskin inlärnings modeller med hjälp av Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Konfigurera funktionalisering

I varje automatiserad maskin inlärnings experiment tillämpas [automatiska skalnings-och normaliserings metoder](#featurization) på dina data som standard. Dessa metoder är typer av funktionalisering som hjälper *vissa* algoritmer som är känsliga för funktioner i olika skalor. Du kan dock också aktivera ytterligare funktionalisering, t. ex. *saknade värden Imputation*, *encoding*och *transformationer*.

> [!NOTE]
> Steg för automatisk Machine Learning-funktionalisering (till exempel funktion normalisering, hantering av data som saknas eller konvertering av text till numeriska) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpas på din indata automatiskt.

För experiment som du konfigurerar med python SDK, kan du aktivera eller inaktivera funktionalisering-inställningen och ytterligare ange de funktionalisering-steg som ska användas för experimentet. Om du använder Azure Machine Learning Studio, se [stegen för att aktivera funktionalisering](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

I följande tabell visas de accepterade inställningarna för `featurization` i [klassen AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Funktionalisering-konfiguration | Beskrivning|
------------- | ------------- |
|`"featurization": 'auto'`| Anger att steg som en del av processen för att bearbeta [data guardrails och funktionalisering](#featurization) ska göras automatiskt. Den här inställningen är standardinställningen.|
|`"featurization": 'off'`| Anger att funktionalisering-steg inte ska göras automatiskt.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Anger att anpassade funktionalisering-steg ska användas. [Lär dig hur du anpassar funktionalisering](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatisk funktionalisering

I följande tabell sammanfattas de tekniker som automatiskt tillämpas på dina data. Dessa tekniker används för experiment som har kon figurer ATS med hjälp av SDK eller Studio. Om du vill inaktivera det här beteendet ställer `"featurization": 'off'` du in i `AutoMLConfig` objektet.

> [!NOTE]
> Om du planerar att exportera AutoML-modeller till en ONNX- [modell](concept-onnx.md)stöds bara de funktionalisering-alternativ som anges med en asterisk ("*") i ONNX-formatet. Lär dig mer om [att konvertera modeller till ONNX](concept-automated-ml.md#use-with-onnx).

|Funktionalisering- &nbsp; steg| Beskrivning |
| ------------- | ------------- |
|**Släpp hög kardinalitet eller inga varians funktioner*** |Släpp dessa funktioner från utbildning och validerings uppsättningar. Gäller för funktioner med alla värden som saknas, med samma värde för alla rader eller med hög kardinalitet (till exempel hash-värden, ID: n eller GUID).|
|**Imputerade värden som saknas*** |För numeriska funktioner måste du räkna ut med medelvärdet av värdena i kolumnen.<br/><br/>För kategoriska-funktioner ska du räkna med det vanligaste värdet.|
|**Generera ytterligare funktioner*** |För DateTime-funktioner: år, månad, dag, veckodag, dag på år, kvartal, vecka på år, timme, minut och sekund.<br/><br/>För text funktioner: term frekvens baserat på unigrams, bigram och trigrams.|
|**Transformera och koda***|Transformera numeriska funktioner med några få unika värden i kategoriska-funktioner.<br/><br/>En-frekvent kodning används för kategoriska-funktioner med låg kardinalitet. En-frekvent-hash-kodning används för kategoriska-funktioner med hög kardinalitet.|
|**Word-inbäddningar**|En text upplärda konverterar vektorer med text-token till menings vektorer med hjälp av en förtränad modell. Varje ords inbäddnings vektor i ett dokument sammanställs med resten för att skapa en dokument funktions vektor.|
|**Mål kodningar**|För kategoriska-funktioner mappar det här steget varje kategori med ett genomsnittligt målvärde för Regressions problem, och till sannolikheten för varje klass för klassificerings problem. Frekvens-baserad viktning och n:te kors validering används för att minska överanpassningen av mappningen och bruset som orsakas av glesa data kategorier.|
|**Kodning av text mål**|För text inmatare används en staplad linjär modell med ord uppsättnings ord för att generera sannolikheten för varje klass.|
|**Vikt på bevis (WoE)**|Beräknar WoE som ett mått på korrelation av kategoriska-kolumner till mål kolumnen. WoE beräknas som loggen för förhållandet mellan sannolikheten för inaktuella klasser och sannolikheter. Det här steget skapar en numerisk funktions kolumn per klass och tar bort behovet av att uttryckligen kräva in saknade värden och avvikare-behandling.|
|**Kluster avstånd**|Tågen a k: kluster modell på alla numeriska kolumner. Ger nya *k* nya funktioner (en ny numerisk funktion per kluster) som innehåller avståndet för varje exempel till centroid för varje kluster.|

## <a name="data-guardrails"></a>Data guardrails

*Data guardrails* hjälper dig att identifiera potentiella problem med dina data (till exempel saknade värden eller [obalanser i klassen](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). De hjälper dig också att vidta korrigerande åtgärder för förbättrade resultat.

Data guardrails tillämpas:

- **För SDK-experiment**: När parametrarna `"featurization": 'auto'` eller `validation=auto` anges i `AutoMLConfig` objektet.
- **För Studio experiment**: när automatisk funktionalisering har Aktiver ATS.

Du kan granska data guardrails för ditt experiment:

- Genom att ställa in `show_output=True` när du skickar ett experiment med hjälp av SDK.

- I Studio går du till fliken **data guardrails** i din AUTOMATISERAde ml-körning.

### <a name="data-guardrail-states"></a>Data guardrail-tillstånd

Data guardrails visar ett av tre tillstånd:

|Stat| Beskrivning |
|----|---- |
|**Parametrarna**| Inga data problem upptäcktes och ingen åtgärd krävs av dig. |
|**Klar**| Ändringarna tillämpades på dina data. Vi rekommenderar att du läser igenom de korrigerande åtgärder som AutoML vidtog, för att säkerställa att ändringarna överensstämmer med de förväntade resultaten. |
|**Aviserad**| Ett data problem upptäcktes men kunde inte åtgärdas. Vi rekommenderar att du ändrar och åtgärdar problemet.|

### <a name="supported-data-guardrails"></a>Guardrails data som stöds

I följande tabell beskrivs de data guardrails som för närvarande stöds och de associerade status som du kan se när du skickar in experimentet:

Guardrail|Status|Villkor &nbsp; för &nbsp; utlösare
---|---|---
**Funktions värden saknas Imputation** |Parametrarna <br><br><br> Klart| Inga funktions värden som saknas hittades i dina tränings data. Läs mer om [saknat värde för Imputation.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> De funktions värden som saknas har identifierats i dina utbildnings data och tillräknades.
**Funktions hantering med hög kardinalitet** |Parametrarna <br><br><br> Klart| Dina indata analyserades och inga funktioner för hög kardinalitet upptäcktes. Läs mer om [funktions identifiering med hög kardinalitet](#automatic-featurization). <br><br> Funktioner med hög kardinalitet upptäcktes i dina indata och hanterades.
**Verifiering av delad hantering** |Klart| Verifierings konfigurationen har angetts till `'auto'` och tränings data innehöll *färre än 20 000 rader*. <br> Varje iteration av den tränade modellen verifierades med hjälp av kors validering. Läs mer om [verifierings data](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> Verifierings konfigurationen har angetts till `'auto'` och tränings data innehöll *mer än 20 000 rader*. <br> Indata har delats in i en tränings data uppsättning och en validerings data uppsättning för att verifiera modellen.
**Identifiering av klass balansering** |Parametrarna <br><br><br><br><br> Aviserad | Dina indata analyserades och alla klasser är balanserade i dina tränings data. En data uppsättning anses vara bal anse rad om varje klass har en god representation i data uppsättningen, mätt enligt antal och samplings förhållandet. <br><br><br> Obalanserade klasser upptäcktes i dina indata. Åtgärda problem med balanseringen om du vill åtgärda modell kompensation. Läs mer om [obalanserade data](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).
**Identifiering av minnes problem** |Parametrarna <br><br><br><br> Klart |<br> De valda värdena (horisont, fördröjning, rullande fönster) har analyser ATS och inga potentiella minnes problem har identifierats. Lär dig mer om [Prognosticering-konfigurationer](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)för tids serier. <br><br><br>De valda värdena (horisont, fördröjning, rullande fönster) har analyser ATS och kan orsaka att experimentet får slut på minne. Inställningarna för fördröjning eller rullande fönster har inaktiverats.
**Frekvens identifiering** |Parametrarna <br><br><br><br> Klart |<br> Tids serien analyserades och alla data punkter justeras med den frekvens som upptäcktes. <br> <br> Tids serien analyserades och data punkter som inte överensstämmer med den identifierade frekvensen upptäcktes. Dessa data punkter togs bort från data uppsättningen. Lär dig mer om [förberedelse av data för tids serie prognoser](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Anpassa funktionalisering

Du kan anpassa dina funktionalisering-inställningar för att säkerställa att de data och funktioner som används för att träna din ML-modell leder till relevanta förutsägelser.

Om du vill anpassa featurizations anger du  `"featurization": FeaturizationConfig` i ditt `AutoMLConfig` objekt. Om du använder Azure Machine Learning Studio för experimentet går du till [instruktions artikeln](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Anpassningar som stöds är:

|Anpassning|Definition|
|--|--|
|**Uppdatering av kolumn syfte**|Åsidosätt funktions typen för den angivna kolumnen.|
|**Transformering av parameter uppdatering** |Uppdatera parametrarna för den angivna transformeraren. Stöder för närvarande *imputerade* (medel, mest frekventa och median) och *HashOneHotEncoder*.|
|**Släpp kolumner** |Anger kolumner som ska släppas från att bearbetas.|
|**Block transformatorer**| Anger block-transformatorer som ska användas i funktionalisering-processen.|

Skapa `FeaturizationConfig` objektet med hjälp av API-anrop:

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

* Lär dig hur du konfigurerar dina automatiserade ML-experiment:

    * För en kod-första upplevelse: [Konfigurera automatiserade ml-experiment med hjälp av Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * För en miljö med låg kod eller ingen kod: [skapa dina automatiserade ml-experiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

* Lär dig mer om [hur du tränar en Regressions modell med hjälp av automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar genom att använda Automatisk maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).
