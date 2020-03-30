---
title: Installera paket i Jupyter-anteckningsböcker – förhandsversionen av Azure Notebooks
description: Lär dig hur du installerar Python-, R- och F#-paket från en Jupyter-anteckningsbok som körs på Azure.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646236"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Installera paket från förhandsversionen av Azure Notebooks

Även om du kan konfigurera [miljön för anteckningsboken på projektnivå](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)kanske du vill installera paket direkt i en enskild anteckningsbok.

Paket som är installerade från anteckningsboken gäller endast för den aktuella serversessionen. Paketinstallationer sparas inte när servern har stängts av.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

Paket i Python kan installeras med antingen pip eller conda med kommandon i kodceller:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Om kommandoutdata anger att kravet redan är uppfyllt kan Azure Notebooks inkludera paketet som standard. Paketet kan också installeras via ett [steg för projektmiljöinställningar](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Paket i R kan installeras från CRAN eller `install.packages` GitHub med hjälp av funktionen i en kodcell:

```r
install.packages("package_name")
```

Du kan också installera förhandsversioner och andra utvecklingspaket från GitHub med hjälp av devtools-biblioteket:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Paket i F# kan installeras från [nuget.org](https://www.nuget.org) genom att anropa beroendehanteraren Paket inifrån kodceller. Läs först pakethanteraren:

```fsharp
#load "Paket.fsx"
```

Installera sedan paket:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Ladda sedan Paket-generatorn:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Öppna biblioteket:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar och hanterar du projekt](configure-manage-azure-notebooks-projects.md)
- [Så här presenterar du ett bildspel](present-jupyter-notebooks-slideshow.md)
