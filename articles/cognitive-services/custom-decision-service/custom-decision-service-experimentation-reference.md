---
title: Experimentering – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Den här artikeln är en hand bok för experimentering med Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: e6e8e7d0d5b969464ba9183ccae9080f58f786a0
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707286"
---
# <a name="experimentation"></a>Experimentering

När du följer teorin i [sammanhangsbaserade Bandits (CB)](https://www.microsoft.com/en-us/research/blog/contextual-bandit-breakthrough-enables-deeper-personalization/)kommer Custom Decision service upprepade gånger att följa en kontext, vidta en åtgärd och se en belöning för den valda åtgärden. Ett exempel är anpassning av innehåll: kontexten beskriver en användare, åtgärder är kandidat berättelser och belöningen mäter hur mycket användaren gillade den rekommenderade artikeln.

Custom Decision Service skapar en princip, som den mappar från kontexter till åtgärder. Med en speciell mål princip vill du veta den förväntade belöningen. Ett sätt att uppskatta belöningen är att använda en princip online och låta den välja åtgärder (till exempel rekommendera berättelser till användare). En sådan online-utvärdering kan dock vara kostsam av två skäl:

* Den visar användare till en testad, experimentell princip.
* Den skalar inte för att utvärdera flera mål principer.

Utvärderingen av principen är ett alternativ paradigm. Om du har loggar från ett befintligt online-system som följer en loggnings princip kan utvärderingen av principer för avstängning uppskatta de förväntade fördelarna med nya mål principer.

Genom att använda logg filen söker experimentet efter principen med den högsta beräknade, förväntade belöningen. Mål principerna är parameterstyrda av [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki) -argument. I standard läget testar skriptet en rad Vowpal Wabbit-argument genom att lägga till i `--base_command`. Skriptet utför följande åtgärder:

* Identifierar automatiskt funktionernas namn områden från de första `--auto_lines` raderna i indatafilen.
* Utför en första rensning över Hyper-Parameters`learning rate`( `L1 regularization`, och `power_t`).
* Testar princip utvärderingen `--cb_type` (inversen benägenhet score`ips`() eller dubbla robust (`dr`). Mer information finns i [sammanhangsbaserade bandit-exempel](https://github.com/JohnLangford/vowpal_wabbit/wiki/Contextual-Bandit-Example).
* Testar marginaler.
* Testa kvadratisk interaktions funktioner:
   * **Brute-tvångs fas**: Testar alla kombinationer med `--q_bruteforce_terms` par eller färre.
   * **girig-fas**: Lägger till det bästa paret tills det inte finns någon `--q_greedy_stop` förbättring av avrundning.
* Utför en andra svep över Hyper-Parameters`learning rate`( `L1 regularization`, och `power_t`).

De parametrar som styr dessa steg innehåller vissa Vowpal Wabbit-argument:
- Exempel på modifierings alternativ:
  - delade namn områden
  - åtgärds namn rymder
  - marginal namn rymder
  - Kvadratiska funktioner
- Uppdatera regel alternativ
  - inlärnings pris
  - L1-regulariseringshastigheten
  - t-energi värde

En djupgående förklaring av ovanstående argument finns i [Vowpal Wabbit kommando rads argument](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).

## <a name="prerequisites"></a>Förutsättningar
- Vowpal Wabbit: Installerat och på din sökväg.
  - Windows: Använd installations programmet. [ `.msi` ](https://github.com/eisber/vowpal_wabbit/releases)
  - Andra plattformar: [Hämta käll koden](https://github.com/JohnLangford/vowpal_wabbit/releases).
- Python 3: Installerat och på din sökväg.
- NumPy: Använd valfri paket hanterare.
- *Microsoft/MWT-DS-* lagringsplatsen: [Klona lagrings platsen](https://github.com/Microsoft/mwt-ds).
- JSON-loggfil för besluts tjänst: Som standard innehåller `--dsjson`kommandot Base, som aktiverar JSON-parsning av indatafilen för besluts tjänsten. [Hämta ett exempel på det här formatet](https://github.com/JohnLangford/vowpal_wabbit/blob/master/test/train-sets/decisionservice.json).

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

En logg med resultaten läggs till i filen *MWT-DS/DataScience/Experiments. csv* .

### <a name="parameters"></a>Parametrar
| Indata | Beskrivning | Standard |
| --- | --- | --- |
| `-h`, `--help` | Visa hjälp meddelande och avsluta. | |
| `-f FILE_PATH`, `--file_path FILE_PATH` | Sökväg till data fil`.json` ( `.json.gz` eller format – varje rad är `dsjson`en). | Obligatorisk |  
| `-b BASE_COMMAND`, `--base_command BASE_COMMAND` | Kommandot Base Vowpal Wabbit.  | `vw --cb_adf --dsjson -c` |  
| `-p N_PROC`, `--n_proc N_PROC` | Antal parallella processer som ska användas. | Logiska processorer |  
| `-s SHARED_NAMESPACES, --shared_namespaces SHARED_NAMESPACES` | Namn områden för delad funktion (till exempel `abc` , `a`namn rymder `b`, och `c`).  | Identifiera automatiskt från data fil |  
| `-a ACTION_NAMESPACES, --action_namespaces ACTION_NAMESPACES` | Funktions namn rymder. | Identifiera automatiskt från data fil |  
| `-m MARGINAL_NAMESPACES, --marginal_namespaces MARGINAL_NAMESPACES` | Namn rymder för marginal funktionen. | Identifiera automatiskt från data fil |  
| `--auto_lines AUTO_LINES` | Antal data fils rader som ska genomsökas för att automatiskt identifiera funktioner namn områden. | `100` |  
| `--only_hp` | Endast svep över Hyper-Parameters`learning rate`( `L1 regularization`,, `power_t`och). | `False` |  
| `-l LR_MIN_MAX_STEPS`, `--lr_min_max_steps LR_MIN_MAX_STEPS` | Inlärnings intervallet är positiva värden `min,max,steps`. | `1e-5,0.5,4` |  
| `-r REG_MIN_MAX_STEPS`, `--reg_min_max_steps REG_MIN_MAX_STEPS` | L1-regulariseringshastigheten intervall som positiva `min,max,steps`värden. | `1e-9,0.1,5` |  
| `-t PT_MIN_MAX_STEPS`, `--pt_min_max_steps PT_MIN_MAX_STEPS` | Power_t-intervall som positiva `min,max,step`värden. | `1e-9,0.5,5` |  
| `--q_bruteforce_terms Q_BRUTEFORCE_TERMS` | Antal lösnings par som ska testas i Brute-Force-fasen. | `2` |  
| `--q_greedy_stop Q_GREEDY_STOP` | Avrundar utan förbättringar, efter vilken kvadratisk girig search-fasen stoppas. | `3` |  

### <a name="examples"></a>Exempel
Så här använder du förinställda standardvärden:
```cmd
python Experimentation.py -f D:\multiworld\data.json
```

Vowpal Wabbit kan också `.json.gz` hämta filer:
```cmd
python Experimentation.py -f D:\multiworld\data.json.gz
```

Endast svepa över Hyper-Parameters`learning rate`( `L1 regularization`,, `power_t`och, stoppa efter steg 2):
```cmd
python Experimentation.py -f D:\multiworld\data.json --only_hp
```
