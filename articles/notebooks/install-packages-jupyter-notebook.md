---
title: Installera paket i Jupyter Notebooks – Azure Notebooks för hands version
description: 'Lär dig hur du installerar python-, R-och F #-paket i en Jupyter-anteckningsbok som körs på Azure.'
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 43d38c9f3a4b0095967ab3e103ea729ec86ea2bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831224"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Installera paket inifrån Azure Notebooks för hands version

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Även om du kan konfigurera [miljön för din bärbara dator på projekt nivå](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)kanske du vill installera paket direkt i en enskild antecknings bok.

Paket som installeras från antecknings boken gäller bara för den aktuella serversessionen. Paket installationer sparas inte när servern stängs av.

## <a name="python"></a>Python

Paket i python kan installeras med hjälp av antingen pip eller Conda med kommandon i kod celler:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Om kommandoutdata anger att kravet redan är uppfyllt kan Azure Notebooks inkludera paketet som standard. Paketet kan också installeras via ett [installations steg för projekt miljön](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Paket i R kan installeras från CRAN eller GitHub med hjälp av `install.packages` funktionen i en kod cell:

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

Paket i F # kan installeras från [NuGet.org](https://www.nuget.org) genom att anropa paket beroende hanteraren inifrån kod cellerna. Börja med att läsa in paket Manager:

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

Läs sedan in paket Generator:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Öppna biblioteket:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Nästa steg

- [Gör så här: Konfigurera och hantera projekt](configure-manage-azure-notebooks-projects.md)
- [Så här gör du: Visa ett bild spel](present-jupyter-notebooks-slideshow.md)
