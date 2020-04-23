---
title: Lägga till och hantera bibliotek för Apache Spark i Azure Synapse Analytics
description: Lär dig hur du lägger till och hanterar bibliotek som används av Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870368"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Lägga till och hantera bibliotek för Apache Spark i Azure Synapse Analytics

Apache Spark är beroende av många bibliotek för att tillhandahålla funktioner. Dessa bibliotek kan utökas eller ersättas med ytterligare bibliotek eller uppdaterade versioner av äldre.

Python-paket kan läggas till på Spark-poolnivå (förhandsversion) och .jar-baserade paket kan läggas till på spark-jobbdefinitionsnivå.

## <a name="adding-or-updating-python-libraries"></a>Lägga till eller uppdatera Python-bibliotek

Apache Spark i Azure Synapse Analytics har en fullständig Anacondas-installation plus ytterligare bibliotek. Den fullständiga bibliotekslistan finns på [Apache Spark version support](apache-spark-version-support.md).

När en Spark-instans startar skapas en ny virtuell miljö med den här installationen som bas. Dessutom kan en *requirements.txt-fil* `pip freeze` (utdata från kommandot) användas för att uppgradera den virtuella miljön. Paketen som anges i den här filen för installation eller uppgradering hämtas från PyPi vid tidpunkten för klusterstart. Den här kravfilen används varje gång en Spark-instans skapas från sparkpoolen.

> [!IMPORTANT]
>
> - Om paketet du installerar är stort eller tar lång tid att installera, påverkar detta Spark-instansens starttid.
> - Paket som kräver kompilatorstöd vid installation, till exempel GCC, stöds inte.
> - Paket kan inte nedgraderas, bara läggas till eller uppgraderas.

### <a name="requirements-format"></a>Format för krav

Följande kodavsnitt visar formatet för kravfilen. PyPi-paketnamnet visas tillsammans med en exakt version. Den här filen följer det format som beskrivs i referensdokumentationen [för pipfrysning.](https://pip.pypa.io/en/stable/reference/pip_freeze/) Det här exemplet fäster en viss version. Du kan också ange "inte större än" och "mindre än" versioner i den här filen.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Användargränssnitt för Python-bibliotek

Användargränssnittet för att lägga till bibliotek finns på fliken **Ytterligare inställningar** på **poolsidan Skapa Apache Spark** på Azure-portalen.

Ladda upp **konfigurationsfilen** för miljön med hjälp av filväljaren i avsnittet Paket på sidan.

![Lägg till Python-bibliotek](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Lägg till Python-bibliotek")

### <a name="verifying-installed-libraries"></a>Verifiera installerade bibliotek

För att kontrollera om rätt versioner av rätt bibliotek är installerade kör följande kod

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark-dokumentation](https://spark.apache.org/docs/2.4.4/)
