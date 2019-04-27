---
title: LogDownloader – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Ladda ned loggfiler som genereras av Azure Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8a8f669c33f40fb80dc826ec04203880dee74d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829998"
---
# <a name="logdownloader"></a>LogDownloader

Ladda ned loggfiler som produceras av Azure Custom Decision Service och generera de *.gz* filer som används av experimentering.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Python 3: Installerad och i sökvägen. Vi rekommenderar den 64-bitars-versionen för att hantera stora filer.
- Den *Microsoft/mwt-ds* lagringsplats: [Klona lagringsplatsen](https://github.com/Microsoft/mwt-ds).
- Den *azure blobblagring* paketet: För information om installationen går du till [Microsoft Azure Storage-biblioteket för Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Ange din anslutningssträng för Azure storage i *mwt-ds/DataScience/ds.config*: Följ den *my_app_id: my_connectionString* mall. Du kan ange flera `app_id`. När du kör `LogDownloader.py`om indata `app_id` hittades inte i `ds.config`, `LogDownloader.py` använder den `$Default` anslutningssträngen.

## <a name="usage"></a>Användning

Gå till `mwt-ds/DataScience` och kör `LogDownloader.py` med relevanta argument, enligt beskrivningen i följande kod:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametrar

| Indata | Beskrivning | Standard |
| --- | --- | --- |
| `-h`, `--help` | Visa hjälpmeddelande och avsluta. | |
| `-a APP_ID`, `--app_id APP_ID` | App-ID (det vill säga namnet på Azure Storage blob-behållare). | Obligatoriskt |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Den grundläggande katalogen för att ladda ned data (en undermapp skapas).  | Obligatoriskt |
| `-s START_DATE`, `--start_date START_DATE` | Hämtning startdatum (inkluderat) är i *åååå-MM-DD* format. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Hämtar slutdatumet (ingår), i *åååå-MM-DD* format. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Överskrivningsläge att använda. | |
| | `0`: Skriv aldrig över; Be användaren om BLOB-objekt används för närvarande. | Standard |
| | `1`: Be användaren hur du fortsätter när filerna har olika storlekar eller när blobarna som används för närvarande. | |
| | `2`: Alltid över; ladda ned används blobar. | |
| | `3`: Skriv aldrig över och till om den är större, utan att be; ladda ned används blobar. | |
| | `4`: Skriv aldrig över och till om den är större, utan att be; Hoppa över för närvarande används blobbar. | |
| `--dry_run` | Skriv ut vilka blobar skulle har laddats ned, utan att hämta. | `False` |
| `--create_gzip` | Skapa en *gzip* -filen för Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Tidsfönster, i sekunder, för att upptäcka om en fil är för närvarande används. | `3600` SEK (`1` timme) |
| `--verbose` | Skriva ut mer information. | `False` |
| `-v VERSION`, `--version VERSION` | Installationshämtaren loggversion du använder. | |
| | `1`: För rå loggar (endast för bakåtkompatibilitet). | Inaktuell |
| | `2`: För kokta loggar. | Standard |

### <a name="examples"></a>Exempel

Använd följande kod för en testsändning för att hämta alla data i Azure Storage blob-behållaren:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Så här hämtar endast loggar som skapats sedan den 1 januari 2018 med `overwrite_mode=4`, Använd följande kod:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Skapa en *gzip* fil sammanslagning hämtade filer ska du använda följande kod:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
