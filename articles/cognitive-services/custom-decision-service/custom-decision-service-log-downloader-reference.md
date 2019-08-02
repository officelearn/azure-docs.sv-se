---
title: LogDownloader – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Hämta loggfiler som skapas av Azure Custom Decision Service.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ROBOTS: NOINDEX
ms.openlocfilehash: 33cc0d0dcf16ff82ac128507566427e123020236
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707228"
---
# <a name="logdownloader"></a>LogDownloader

Hämta loggfiler som skapas av Azure Custom Decision Service och generera de *. gz* -filer som används av experimentering.

## <a name="prerequisites"></a>Förutsättningar

- Python 3: Installerat och på din sökväg. Vi rekommenderar 64-bitars versionen för att hantera stora filer.
- *Microsoft/MWT-DS-* lagringsplatsen: [Klona lagrings platsen](https://github.com/Microsoft/mwt-ds).
- *Azure-Storage-BLOB-* paketet: Installations information finns i [Microsoft Azure Storage Library för python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Ange anslutnings strängen för Azure Storage i *MWT-DS/DataScience/DS. config*: Följ mallen *my_app_id: my_connectionString* . Du kan ange flera `app_id`. När du kör `LogDownloader.py`, om inmatade `app_id` inte `$Default` hittas i `ds.config`, `LogDownloader.py` använder anslutnings strängen.

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
| `-h`, `--help` | Visa hjälp meddelandet och avsluta. | |
| `-a APP_ID`, `--app_id APP_ID` | App-ID: t (det vill säga Azure Storage namn på BLOB-behållare). | Obligatorisk |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Bas katalogen för att hämta data (en undermapp skapas).  | Obligatorisk |
| `-s START_DATE`, `--start_date START_DATE` | Start datum för hämtning (ingår) i formatet *åååå-mm-dd* . | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Slutdatumet för hämtningen (inkluderat) i *åååå-mm-dd* -format. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Det överskrivnings läge som ska användas. | |
| | `0`: Skriv aldrig över; fråga användaren om blobbar används för närvarande. | Standard |
| | `1`: Be användaren att fortsätta när filerna har olika storlekar eller när blobbar används för närvarande. | |
| | `2`: Skriv alltid över; Hämta aktuella blobar som används. | |
| | `3`: Skriv aldrig över och Lägg till om storleken är större, utan att fråga; Hämta aktuella blobar som används. | |
| | `4`: Skriv aldrig över och Lägg till om storleken är större, utan att fråga; hoppa över blobar som används för närvarande. | |
| `--dry_run` | Skriv ut vilka blobbar som har hämtats, utan att ladda ned. | `False` |
| `--create_gzip` | Skapa en *gzip* -fil för Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Tids perioden, i sekunder, för att identifiera om en fil används för närvarande. | `3600`SEK (`1` timme) |
| `--verbose` | Skriv ut mer information. | `False` |
| `-v VERSION`, `--version VERSION` | Den version av logg hämtaren som ska användas. | |
| | `1`: För återkokta loggar (endast för bakåtkompatibilitet). | Inaktuell |
| | `2`: För kokta loggar. | Standard |

### <a name="examples"></a>Exempel

För en torr körning av nedladdning av alla data i Azure Storage BLOB-behållaren använder du följande kod:
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Om du bara vill hämta loggar som skapats sedan 1 januari `overwrite_mode=4`2018 med använder du följande kod:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Använd följande kod för att skapa en *gzip* -fil som sammanfogar alla hämtade filer:
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
