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
ms.openlocfilehash: 03a1235b9bf20fa19f378802923d3bab7dbf4900
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
| Resurs | Övre gräns |
| --- | --- |
| Högsta antal nya jobb som kan skickas med 30 sekunders mellanrum per Automation-konto (icke schemalagda jobb) |100 |
| Högsta antal samtidiga jobb som körs på samma instans av tid per Automation-konto (icke schemalagda jobb) |200 |
| Max antal moduler som kan importeras var 30: e sekund per Automation-konto |5 |
| Maxstorlek på en modul |100 MB |
| Jobbet Runtime - kostnadsfri nivå |500 minuter per prenumeration per månad |
| Högsta mängd minne som tilldelas ett jobb <sup>1</sup> |400 MB |
| Högsta antal nätverk sockets tillåts per jobb <sup>1</sup> |1000 |
| Max antal Automation-konton i en prenumeration |Obegränsat |

<sup>1</sup> dessa gränser gäller endast sandboxar i Azure, för hybrider dessa endast begränsas av funktionerna i den dator där worker-hybriden finns.