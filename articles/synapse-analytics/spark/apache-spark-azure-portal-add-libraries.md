---
title: Lägga till och hantera bibliotek för Apache Spark i Azure Synapse Analytics
description: Lär dig hur du lägger till och hanterar bibliotek som används av Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5eb67752a57ce7745b1128f5f5eb36414bfe993c
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392615"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Lägga till och hantera bibliotek för Apache Spark i Azure Synapse Analytics

Apache Spark är beroende av många bibliotek för att tillhandahålla funktioner. Dessa bibliotek kan utökas eller ersättas med ytterligare bibliotek eller uppdaterade versioner av äldre versioner.

Python-paket kan läggas till på nivån Spark-pool (för hands version) och. jar-baserade paket kan läggas till på jobb definitions nivån Spark.

## <a name="adding-or-updating-python-libraries"></a>Lägga till eller uppdatera python-bibliotek

Apache Spark i Azure Synapse Analytics har en fullständig Anacondas-installation plus ytterligare bibliotek. Du hittar den fullständiga biblioteks listan på [Apache Spark versions stöd](apache-spark-version-support.md).

När en spark-instans startar skapas en ny virtuell miljö med den här installationen som bas. Dessutom kan en *requirements.txt* -fil (utdata från `pip freeze` kommandot) användas för att uppgradera den virtuella miljön. Paketen som anges i den här filen för installation eller uppgradering laddas ned från PyPi vid tidpunkten för klustrets start. Den här krav filen används varje gång en spark-instans skapas från den Spark-poolen.

> [!IMPORTANT]
>
> - Om paketet som du installerar är stort eller tar lång tid att installera, påverkar detta start tiden för Spark-instansen.
> - Paket som kräver stöd för kompilator vid installations tillfället, till exempel GCC, stöds inte.
> - Paket kan inte nedgraderas, bara läggas till eller uppgraderas.

### <a name="requirements-format"></a>Krav format

I följande kodfragment visas formatet för krav filen. PyPi-paketets namn visas tillsammans med en exakt version. Den här filen följer formatet som beskrivs i referens dokumentationen för [pip Freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Det här exemplet fäster en speciell version. Du kan också ange versionerna "inte större än" och "mindre än" i den här filen.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Användar gränssnitt för python-bibliotek

Användar gränssnittet för att lägga till bibliotek finns på fliken **ytterligare inställningar** på sidan **skapa Apache Spark pool** på Azure Portal.

Ladda upp miljö konfigurations filen med fil väljaren i avsnittet **paket** på sidan.

![Lägg till Python-bibliotek](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Lägg till Python-bibliotek")

### <a name="verifying-installed-libraries"></a>Verifierar installerade bibliotek

Kontrol lera att rätt versioner av rätt bibliotek är installerade genom att köra följande kod

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Nästa steg

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark dokumentation](https://spark.apache.org/docs/2.4.4/)
