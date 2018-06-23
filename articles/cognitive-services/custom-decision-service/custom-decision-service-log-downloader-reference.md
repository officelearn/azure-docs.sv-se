---
title: LogDownloader - kognitiva Azure-tjänster | Microsoft Docs
description: Hämta loggfiler som genereras av Azure anpassad beslut Service.
services: cognitive-services
author: marco-rossi29
manager: marco-rossi29
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 783b534b3b3f4bb7f5d9f073f491690759edfea5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354552"
---
# <a name="logdownloader"></a>LogDownloader

Hämta loggfiler som produceras av Azure anpassad beslut Service och generera den *.gz* filer som används av experiment.

## <a name="prerequisites"></a>Förutsättningar

- Python 3: Installerad på din sökväg. Vi rekommenderar 64-bitarsversionen att hantera stora filer.
- Den *mwt/Microsoft-ds* databasen: [klona lagringsplatsen](https://github.com/Microsoft/mwt-ds).
- Den *azure storage blob* paket: information om installationen, går du till [Microsoft Azure Storage-biblioteket för Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Ange anslutningssträngen för Azure-lagring i *mwt-ds/DataScience/ds.config*: följer den *my_app_id: my_connectionString* mall. Du kan ange flera `app_id`. När du kör `LogDownloader.py`, om indata `app_id` hittades inte i `ds.config`, `LogDownloader.py` använder den `$Default` anslutningssträngen.

## <a name="usage"></a>Användning

Gå till `mwt-ds/DataScience` och kör `LogDownloader.py` med relevanta argument, enligt anvisningarna i följande kod:

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>Parametrar

| Indata | Beskrivning | Standard |
| --- | --- | --- |
| `-h`, `--help` | Visa hjälpmeddelandet och avsluta. | |
| `-a APP_ID`, `--app_id APP_ID` | App-ID (det vill säga Azure Storage blob behållarens namn). | Krävs |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Baskatalog för att ladda ned data (en undermapp skapas).  | Krävs |
| `-s START_DATE`, `--start_date START_DATE` | Hämtning startdatum (ingår) i *åååå-MM-DD* format. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Hämtar slutdatumet (ingår), i *åååå-MM-DD* format. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Skriv överskrivningsläge ska använda. | |
| | `0`: Skriv över aldrig; fråga användaren om blobbar används. | Standard | |
| | `1`: Be användaren hur du ska fortsätta när filerna har olika storlekar eller när den BLOB-objekt används för närvarande. | |
| | `2`: Alltid över; ladda ned blobbar som för närvarande används. | |
| | `3`: Aldrig skrivas över och Lägg till om storleken är större, utan att be; ladda ned blobbar som för närvarande används. | |
| | `4`: Aldrig skrivas över och Lägg till om storleken är större, utan att be; Hoppa över används blobbar. | |
| `--dry_run` | Skriv ut vilka blobbar skulle har laddats ned, utan att hämta. | `False` |
| `--create_gzip` | Skapa en *gzip* -filen för Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Tidsfönstret i sekunder, för att upptäcka om en fil är för närvarande används. | `3600` SEK (`1` timme) |
| `--verbose` | Skriv ut mer information. | `False` |
| `-v VERSION`, `--version VERSION` | Installationshämtaren Loggversionen ska användas. | |
| | `1`: För rå loggar (endast för bakåtkompatibilitet). | Inaktuell |
| | `2`: För kokta loggar. | Standard |

### <a name="examples"></a>Exempel

För en testsändning för att hämta alla data i ditt Azure Storage blob-behållare, använder du följande kod:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Hämta endast loggar som skapats sedan den 1 januari 2018 med `overwrite_mode=4`, använda följande kod:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Så här skapar du en *gzip* fil slå samman alla hämtade filer ska du använda följande kod:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```