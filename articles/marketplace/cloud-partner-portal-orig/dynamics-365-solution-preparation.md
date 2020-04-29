---
title: Lösnings förberedelse för Dynamics 365
description: Ramverk för paketering, installation och avinstallation av komponenter
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278594"
---
# <a name="dynamics-365-solution-preparation"></a>Lösnings förberedelse för Dynamics 365

Lösnings systemet för Dynamics 365 är ett ramverk för paketering, installation och avinstallation av komponenter som tillhandahåller specifika affärs funktioner. Lösningar används av ISV: er och andra Microsoft Dynamics 365-partner för att distribuera tillägg som de skapar.

Om du är en befintlig xRM-ISV (Dynamics 365) har du förmodligen redan skapat en hanterad lösning och har en lösning. zip-fil. I din lösning kontrollerar du att fälten "visnings namn" och "Beskrivning" visar vad du vill att kunderna ska se. Dessa visas i administrations Center för CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Obs:** I paket exemplet som följer kommer vi att anta att lösningens namn är "SampleSolution. zip"_

Om du är en ny ISV kan du få mer information om hur du skapar en lösning här:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Om din lösning kräver stöd data:

* Skapa exempel data i din test miljö
* Använd verktyget för konfigurations migrering om du vill skapa ett schema med jämförelse regler för dina data.
* Spara ditt konfigurations schema med dina projektfiler. Du kommer att behöva detta senare om du uppdaterar konfigurations data.
* Exportera konfigurations data. Se till att ge export filen ett namn som är meningsfullt för exporten.
