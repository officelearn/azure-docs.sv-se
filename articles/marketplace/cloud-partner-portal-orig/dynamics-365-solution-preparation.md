---
title: Beredning av dynamik 365-lösning
description: Ramverk för paketering, installation och avinstallation av komponenter
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278594"
---
# <a name="dynamics-365-solution-preparation"></a>Beredning av dynamik 365-lösning

Dynamics 365-lösningssystemet är ett ramverk för att paketera, installera och avinstallera komponenter som tillhandahåller specifika affärsfunktioner. Lösningar används av ISV:er och andra Microsoft Dynamics 365-partner för att distribuera tillägg som de skapar.

Om du är en befintlig Dynamics 365 (xRM) ISV har du troligen redan skapat en hanterad lösning och har en solution.zip-fil. I din lösning, se till att fälten "Visningsnamn" och "Beskrivning" återspeglar vad du vill att kunderna ska se. Dessa visas i CRM Online Administration Center.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Anm.:** I paketexemplet som följer antar vi att lösningsnamnet är "SampleSolution.zip"_

Om du är en ny ISV kan du få mer information om hur du skapar en lösning här:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Om din lösning kräver stöddata:

* Skapa exempeldata i testmiljön
* Använd verktyget konfigurationsmigrering för att skapa ett schema med jämförelseregler för dina data.
* Spara konfigurationsschemat med projektfilerna. Du behöver detta senare om du uppdaterar dina konfigurationsdata.
* Exportera dina konfigurationsdata. Var noga med att ge exportfilen ett namn som är meningsfullt för din export.
