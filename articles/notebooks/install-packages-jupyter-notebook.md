---
title: Installera paket för Jupyter notebook på Azure
description: Så här installerar du Python, R, och F# paket från inom en Jupyter-anteckningsbok som körs på Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: ed57cb458cc1f71c5d6fd2475c54cc712257dd84
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084880"
---
# <a name="install-packages-from-within-a-notebook"></a>Installera paket från inom en anteckningsbok

Du kan konfigurera den [miljö för anteckningsboken vilken projektet](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), kan du installera paket direkt i en enskild anteckningsbok.

Paket som installeras från anteckningsboken gäller endast för den aktuella serversessionen. Paketet är inte beständiga när servern stängs av.

## <a name="python"></a>Python

Paket i Python kan installeras med pip eller conda med hjälp av kommandon i kod celler:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Om utdata från kommandot indikerar att kravet uppfylls redan och anteckningsböcker i Azure kan innehålla paketet som standard. Paketet kan också installeras via en [projekt miljö installationssteget](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Paket i R kan installeras från CRAN eller Github med hjälp av den `install.packages` funktion i en kodcell:

```r
install.packages("package_name")
```

Du kan också installera förhandsversioner och andra paket som utveckling från Github med hjälp av devtools-bibliotek:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

-Paket i F# kan installeras från [nuget.org](https://www.nuget.org) genom att anropa Paket beroendet manager från inom kod celler. Läs först in hanteraren för Paket:

```fsharp
#load "Paket.fsx"
```

Sedan installera paket:

```fsharp
Paket.Package
[ "MathNet.Numerics"
"MathNet.Numerics.FSharp"
]
```

## <a name="next-steps"></a>Nästa steg

- [Så här: konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Så här: presentera ett bildspel](present-jupyter-notebooks-slideshow.md)
