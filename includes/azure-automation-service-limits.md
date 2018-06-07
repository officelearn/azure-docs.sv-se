---
title: ta med fil
description: ta med fil
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 8242f2bb16b52e8c319027788d5b937d7f79ad3d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665095"
---
| Resurs | Övre gräns |
| --- | --- |
| Högsta antal nya jobb som kan skickas med 30 sekunders mellanrum per Automation-konto (icke schemalagda jobb) |100 |
| Högsta antal samtidiga jobb som körs på samma instans av tid per Automation-konto (icke schemalagda jobb) |200 |
| Max antal moduler som kan importeras var 30: e sekund per Automation-konto |5 |
| Maxstorlek på en modul |100 MB |
| Jobbet Runtime - kostnadsfri nivå |500 minuter per prenumeration per månad |
| Högsta mängd minne som tilldelas ett jobb  **<sup>1</sup>** |400 MB |
| Högsta antal nätverk sockets tillåts per jobb  **<sup>1</sup>** |1000 |
| Max antal Automation-konton i en prenumeration |Obegränsat |

**<sup>1</sup>**  dessa gränser gäller endast sandboxar i Azure, för hybrider dessa endast begränsas av funktionerna i den dator där worker-hybriden finns.