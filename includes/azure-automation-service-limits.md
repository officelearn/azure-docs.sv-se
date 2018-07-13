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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756284"
---
| Resurs | Övre gräns |Anteckningar|
| --- | --- |---|
| Maximalt antal nya jobb som ska skickas med 30 sekunders mellanrum per Automation-konto (icke schemalagda jobb) |100 |När den här gränsen träffar, misslyckas de efterföljande begärandena om att skapa ett jobb. Klienten får ett felsvar.|
| Maximalt antal samtidiga jobb som körs på samma instans av tid per Automation-konto (icke schemalagda jobb) |200 |När den här gränsen träffar, misslyckas de efterföljande begärandena om att skapa ett jobb. Klienten får ett felsvar.|
| Maximalt antal moduler som kan importeras med 30 sekunders mellanrum per Automation-konto |5 ||
| Maxstorlek på en modul |100 MB ||
| Kör jobbtid - kostnadsfri nivå |500 minuter per prenumeration per kalendermånad ||
| Högsta mängd diskutrymme som tillåts per sandbox**<sup>1</sup>** |1 GB |Gäller för Azure sandboxar|
| Högsta mängd minne som tilldelas en sandbox**<sup>1</sup>** |400 MB |Gäller för Azure sandboxar|
| Maxantal nätverk sockets tillåts per sandbox**<sup>1</sup>** |1000 |Gäller för Azure sandboxar|
| Maxantal Automation-konton i en prenumeration |Obegränsat ||
|Maximalt antal samtidiga jobb som ska köras på en enda Hybrid Runbook Worker|50 ||

**<sup>1</sup>**  en Sandbox-miljön är en delad miljö som kan användas av flera jobb, jobb med hjälp av samma sandlåda är bundna av resursbegränsningar av sandbox-miljön.
