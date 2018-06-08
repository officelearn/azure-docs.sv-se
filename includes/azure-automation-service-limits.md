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
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852049"
---
| Resurs | Övre gräns |Anteckningar|
| --- | --- |---|
| Högsta antal nya jobb som kan skickas med 30 sekunders mellanrum per Automation-konto (icke schemalagda jobb) |100 |När den här gränsen träffar, misslyckas de efterföljande begäranden om att skapa ett jobb. Klienten får ett felsvar.|
| Högsta antal samtidiga jobb som körs på samma instans av tid per Automation-konto (icke schemalagda jobb) |200 |När den här gränsen träffar, misslyckas de efterföljande begäranden om att skapa ett jobb. Klienten får ett felsvar.|
| Max antal moduler som kan importeras var 30: e sekund per Automation-konto |5 ||
| Maxstorlek på en modul |100 MB ||
| Jobbet Runtime - kostnadsfri nivå |500 minuter per prenumeration per månad ||
| Högsta mängd diskutrymme som tillåts per sandbox**<sup>1</sup>** |1 GB |Gäller för Azure sandboxar|
| Högsta mängd minne som tilldelas en sandbox**<sup>1</sup>** |400 MB |Gäller för Azure sandboxar|
| Högsta antal nätverk sockets tillåts per sandbox**<sup>1</sup>** |1000 |Gäller för Azure sandboxar|
| Max antal Automation-konton i en prenumeration |Obegränsat ||
|Högsta antal samtidiga jobb som ska köras på en enda Hybrid Runbook Worker|50 ||

**<sup>1</sup>**  en sandbox är en delad miljö som kan användas av flera jobb, jobb med hjälp av samma sandbox är bundna av resursbegränsningar av sandbox.
