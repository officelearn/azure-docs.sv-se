---
title: Experiment - kognitiva Azure-tjänster | Microsoft Docs
description: Den här artikeln är en guide för Azure anpassad beslut Service experiment.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/10/2018
ms.author: marossi
ms.openlocfilehash: b0ac0bc049d556423493f0c48dd9a548929bcd41
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354954"
---
# <a name="experimentation"></a>Experimentering

Följande typ av [kontextuella bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/), anpassad beslut tjänsten flera gånger registrerar en kontext, utför en åtgärd och registrerar en ersättning för den valda åtgärden. Ett exempel är innehåll anpassning: kontexten beskriver en användare, åtgärder är kandidat artiklar och trafik som mäter hur mycket användaren tyckte rekommenderade artikeln.

Anpassade beslut tjänsten ger en princip som mappar från kontexter till åtgärder. Med en princip för specifik målsökväg som du vill veta dess förväntade ersättning. Ett sätt att uppskatta ersättning är att använda en princip online och låta den Välj åtgärder (till exempel rekommenderar artiklar för användare). Sådana online utvärderingar kan dock vara kostsamma av två skäl:

* Den visar användare till en princip för otestade, experiment.
* Den inte kan skalas till utvärderingen av principer för flera mål.

Utvärdering av principen är en alternativ paradigmet. Om du har loggar från ett befintligt online system som följer en loggningsprincip för beräkna av principutvärdering förväntade utdelningen av nya principer för målet.

Med hjälp av loggfilen experiment som syftar till att hitta principen med den högsta uppskattade, förväntad trafik. Mål principer parametriserade av [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) argument. I standardläge, testar skriptet för en mängd olika Vowpal Wabbit argument genom att lägga till den `--base_command`. Skriptet utförs följande åtgärder:

* Identifieras funktioner namnområden från först `--auto_lines` raderna i filen.
* Utför en första svep över hyper-parametrar (`learning rate`, `L1 regularization`, och `power_t`).
* Testar principutvärdering `--cb_type` (inverterade benägenheten poäng (`ips`) eller dubbelt robust (`dr`). Mer information finns i [kontextuella Bandit exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testerna Marginalanteckningarna.
* Testerna kvadratisk interaktiva funktioner:
   * **Brute force-fasen**: testar alla kombinationer med `--q_bruteforce_terms` par eller färre.
   * **girig fasen**: lägger till den bästa tills det inte finns några förbättringar för `--q_greedy_stop` Avrundar.
* Utför en andra svep över hyper-parametrar (`learning rate`, `L1 regularization`, och `power_t`).

De parametrar som styr de här stegen är vissa Vowpal Wabbit argument:
- Exempel manipulering alternativ:
  - delade namnområden
  - åtgärden namnområden
  - marginell namnområden
  - kvadratisk funktioner
- Uppdatera alternativ för regel
  - inlärningsfrekvensen
  - L1 regularization
  - t power värde

En detaljerad förklaring av argument som finns i [Vowpal Wabbit kommandoradsargument](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Förutsättningar
- Vowpal Wabbit: Installerad på din sökväg.
  - Windows: [användning av `.msi` installer](https://github.com/eisber/vowpal_wabbit/releases).
  - Andra plattformar: [Hämta källkoden](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Installerad på din sökväg.
- NumPy: Använd Pakethanteraren önskat.
- Den *mwt/Microsoft-ds* databasen: [klona lagringsplatsen](https://github.com/Microsoft/mwt-ds).
- Beslut Service JSON-loggfilen: som standard grundläggande kommandot innehåller `--dsjson`, vilket innebär att beslut Service JSON tolkning av indata-fil. [Hämta ett exempel på det här formatet](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

## <a name="usage"></a>Användning
Gå till `mwt-ds/DataScience` och kör `Experimentation.py` med relevanta argument, enligt anvisningarna i följande kod:

```cmd
python Experimentation.py [-h] -f FILE_PATH [-b BASE_COMMAND] [-p N_PROC]
                          [-s SHARED_NAMESPACES] [-a ACTION_NAMESPACES]
                          [-m MARGINAL_NAMESPACES] [--auto_lines AUTO_LINES]
                          [--only_hp] [-l LR_MIN_MAX_STEPS]
                          [-r REG_MIN_MAX_STEPS] [-t PT_MIN_MAX_STEPS]
                          [--q_bruteforce_terms Q_BRUTEFORCE_TERMS]
                          [--q_greedy_stop Q_GREEDY_STOP]
```

En logg över resultaten läggs till i *mwt-ds/DataScience/experiments.csv* fil.

### <a name="parameters"></a>Parametrar
| Indata | Beskrivning | Standard |
| --- | --- | --- |
| `-h`, `--help` | Visa hjälpmeddelandet och avsluta. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Sökväg till datafil (`.json` eller `.json.gz` format - varje rad är en `dsjson`). | Krävs |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Grundläggande Vowpal Wabbit kommando.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Antalet parallella processer ska användas. | Logiska processorer |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Delade funktionen namnområden (till exempel `abc` innebär namnområden `a`, `b`, och `c`).  | Automatisk identifiering från datafil |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Åtgärden funktionen namnområden. | Automatisk identifiering från datafil |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Marginell funktionen namnområden. | Automatisk identifiering från datafil |  
| `--auto_lines AUTO_LINES` | Antal rader som data fil att söka till automatisk identifiering av funktioner namnområden. | `100` |  
| `--only_hp` | Sopa endast med hyper-parametrar (`learning rate`, `L1 regularization`, och `power_t`). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Learning hastighet intervall som positiva värden `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | L1 regularization intervall som positiva värden `min,max,steps`. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Power_t intervall som positiva värden `min,max,step`. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Antal kvadratisk par att testa i brute force-fasen. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Avrundar utan förbättringar, efter vilken kvadratisk girig Sök fasen stoppas. | `3` |  

### <a name="examples"></a>Exempel
Använda förinställda standardvärden:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Equivalently, Vowpal Wabbit kan också mata in `.json.gz` filer:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Att Sopa endast med hyper-parametrar (`learning rate`, `L1 regularization`, och `power_t`, stoppas efter steg 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
