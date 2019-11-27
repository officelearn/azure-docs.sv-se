---
title: Installera paket för Jupyter notebook på Azure
description: Så här installerar du Python, R, och F# paket från inom en Jupyter-anteckningsbok som körs på Azure.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: 5d85c8e936ce7c8bf38ec7bc9c27d9066cc8b155
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277538"
---
# <a name="install-packages-from-within-a-notebook"></a>Installera paket från inom en anteckningsbok

Även om du kan konfigurera [miljön för din bärbara dator på projekt nivå](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)kanske du vill installera paket direkt i en enskild antecknings bok.

Paket som installeras från anteckningsboken gäller endast för den aktuella serversessionen. Paketet är inte beständiga när servern stängs av.

## <a name="python"></a>Python

Paket i Python kan installeras med pip eller conda med hjälp av kommandon i kod celler:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Om utdata från kommandot indikerar att kravet uppfylls redan och anteckningsböcker i Azure kan innehålla paketet som standard. Paketet kan också installeras via ett [installations steg för projekt miljön](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Paket i R kan installeras från CRAN eller GitHub med hjälp av funktionen `install.packages` i en kod cell:

```r
install.packages("package_name")
```

Du kan också installera för hands versioner och andra utvecklings paket från GitHub med hjälp av devtools-biblioteket:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Paket i F# kan installeras från [NuGet.org](https://www.nuget.org) genom att anropa paket-beroende hanteraren inifrån kod cellerna. Läs först in hanteraren för Paket:

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

Läs sedan in paket Generator:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Öppna biblioteksobjekten:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Nästa steg

- [Gör så här: Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Så här gör du: Visa ett bild spel](present-jupyter-notebooks-slideshow.md)
