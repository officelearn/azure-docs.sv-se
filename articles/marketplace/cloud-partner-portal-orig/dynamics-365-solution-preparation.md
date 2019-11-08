---
title: Lösnings förberedelse för Dynamics 365
description: Ramverk för paketering, installation och avinstallation av komponenter
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: bcb3bb63f305aeb98efda3baf0f6661bd7f67a7c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824231"
---
# <a name="dynamics-365-solution-preparation"></a>Lösnings förberedelse för Dynamics 365

Lösnings systemet för Dynamics 365 är ett ramverk för paketering, installation och avinstallation av komponenter som tillhandahåller specifika affärs funktioner. Lösningar används av ISV: er och andra Microsoft Dynamics 365-partner för att distribuera tillägg som de skapar.

Om du är en befintlig xRM-ISV (Dynamics 365) har du förmodligen redan skapat en hanterad lösning och har en lösning. zip-fil. I din lösning kontrollerar du att fälten "visnings namn" och "Beskrivning" visar vad du vill att kunderna ska se. Dessa visas i administrations Center för CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Obs:** I paket exemplet som följer kommer vi att anta att lösningens namn är "SampleSolution. zip"_

Om du är en ny ISV kan du få mer information om hur du skapar en lösning här: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Om din lösning kräver stöd data:

* Skapa exempel data i din test miljö
* Använd verktyget för konfigurations migrering om du vill skapa ett schema med jämförelse regler för dina data.
* Spara ditt konfigurations schema med dina projektfiler. Du kommer att behöva detta senare om du uppdaterar konfigurations data.
* Exportera konfigurations data. Se till att ge export filen ett namn som är meningsfullt för exporten.
