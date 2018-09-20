---
title: Experimentering – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Den här artikeln är en guide för testning av Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: eec2c82b779fa5421bc9ac58107ef56f8c71bd1e
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366564"
---
# <a name="experimentation"></a>Experimentering

Följande typ av [sammanhangsberoende bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), Custom Decision Service upprepade gånger iakttar en kontext vidtar en åtgärd och iakttar ersättning för den valda åtgärden. Ett exempel är personanpassning av innehåll: kontexten beskriver en användare, åtgärder är kandidat berättelser och trafik som mäter hur mycket du gillade rekommenderade budskapet.

Custom Decision Service skapar en princip som den mappar från kontexter till åtgärder. Med en specifik princip som du vill veta dess förväntade trafik. Ett sätt att beräkna utmärkelse är att använda en princip online och låt den Välj åtgärder (till exempel rekommenderas artiklar för användare). Sådana online utvärdering kan dock vara kostsamma av två skäl:

* Det visar användare till en princip för otestade, experimentella.
* Den inte kan skalas till att utvärdera flera mål-principer.

Inaktivera principutvärdering är en alternativ paradigm. Om du har loggar från ett befintligt online system som följer en loggningsprincip för uppskattning av principutvärdering av de förväntade fördelarna av nya mål-principer.

Med hjälp av loggfilen, experimentering strävar efter att hitta principen med den högsta uppskattade, förväntat trafik. Principer för Target parametriseras av [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argument. I standardläget, testar skriptet olika Vowpal Wabbit argument genom att lägga till till den `--base_command`. Skriptet utför följande åtgärder:

* Upptäcker automatiskt funktioner namnområden från först `--auto_lines` raderna i indatafilen.
* Utför en första rensning hyperparametrar (`learning rate`, `L1 regularization`, och `power_t`).
* Testar principutvärdering `--cb_type` (inverterade benägenhet poäng (`ips`) eller dubbelt robust (`dr`). Mer information finns i [sammanhangsberoende Bandit exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testerna Marginalanteckningarna.
* Testerna kvadratisk interaktiva funktioner:
   * **Brute force-fasen**: testar alla kombinationer av med `--q_bruteforce_terms` par eller färre.
   * **girig fas**: lägger till den bästa tills det finns ingen förbättring för `--q_greedy_stop` Avrundar.
* Utför en andra Svep hyperparametrar (`learning rate`, `L1 regularization`, och `power_t`).

Parametrar som styr de här stegen är vissa Vowpal Wabbit argument:
- Exempel manipulering av alternativ:
  - delade namnområden
  - åtgärd-namnområden
  - marginell namnområden
  - kvadratisk funktioner
- Alternativ för Klientuppdatering regel
  - inlärningsfrekvens
  - L1 regularisering
  - t power-värde

En detaljerad förklaring av argument som finns i [kommandoradsargument som Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Förutsättningar
- Vowpal Wabbit: Installerad och i sökvägen.
  - Windows: [används den `.msi` installer](https://github.com/eisber/vowpal_wabbit/releases).
  - Andra plattformar: [Hämta källkoden](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Installerat och i sökvägen.
- NumPy: Använd Pakethanteraren valfri.
- Den *Microsoft/mwt-ds* lagringsplats: [klona lagringsplatsen](https://github.com/Microsoft/mwt-ds).
- Beslutet Service JSON-loggfilen: som standard grundläggande kommandot innefattar `--dsjson`, vilket gör att Decision Service JSON-parsning av indata-filen. [Hämta ett exempel på det här formatet](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Användning
Gå till `mwt-ds/DataScience` och kör `Experimentation.py` med relevanta argument, enligt beskrivningen i följande kod:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

En logg över resultatet läggs till i *mwt-ds/DataScience/experiments.csv* fil.

### <a name="parameters"></a>Parametrar
| Indata | Beskrivning | Standard |
| --- | --- | --- |
| `-h`, `--help` | Visa hjälpmeddelande och avsluta. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Sökvägen för filen (`.json` eller `.json.gz` format: varje rad är en `dsjson`). | Krävs |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Grundläggande Vowpal Wabbit kommando.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Antalet parallella processer som ska använda. | Logiska processorer |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Delade funktionen namnområden (till exempel `abc` innebär namnområden `a`, `b`, och `c`).  | Identifiera automatiskt från datafil |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Åtgärden funktionen namnområden. | Identifiera automatiskt från datafil |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Marginell funktionen namnområden. | Identifiera automatiskt från datafil |  
| `--auto_lines AUTO_LINES` | Antal data filen rader som ska genomsökas för att automatiskt identifiera funktioner namnområden. | `100` |  
| `--only_hp` | Svepvinkeln endast med hyperparametrar (`learning rate`, `L1 regularization`, och `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Learning rate-intervallet som positiva värden `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | L1 regularisering intervallet som positiva värden `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Power_t intervallet som positiva värden `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Antal kvadratisk par att testa i brute force-fasen. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Rundar utan förbättringar, efter vilken kvadratisk girig search fas stoppas. | `3` |  

### <a name="examples"></a>Exempel
Att använda förinställda standardvärden:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Equivalently, Vowpal Wabbit kan också hämta `.json.gz` filer:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Att svepvinkeln endast med hyperparametrar (`learning rate`, `L1 regularization`, och `power_t`, stoppar efter steg 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
