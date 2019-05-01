---
title: Hur du fyller i formuläret erbjuder inställningar | Azure Marketplace
description: Beskriver de olika fälten som kräver värden i formuläret erbjuder inställningar för ett nytt Dynamics 365 Business Central-program.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pabutler
ms.openlocfilehash: d29b17e1a109b37a51a0e6bd2af2a7bb02b977a9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64934910"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>Hur du fyller i formuläret erbjuder inställningar
=======================================

Erbjudandet inställningar formuläret är en grundläggande och ange inställningar för erbjudandet.
De obligatoriska fälten beskrivs nedan.

### <a name="offer-id"></a>ID för erbjudande

`OfferId` är en unik identifierare för erbjudandet i en utgivarprofil för.
Detta ID syns i URL: er i produkten. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett bindestreck och kan ha högst 50 tecken. Det här fältet är låst när ett erbjudande lanseras.

Om partnern ”Contoso” skapar till exempel ett erbjudande-ID som kallas ”sample-Web App”, visas den i AppSource som:

&emsp; `https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>Utgivar-ID

Den här listrutan kan du välja publisher-profil som du vill publicera det här erbjudandet under. Det här fältet är låst när ett erbjudande lanseras.


### <a name="name"></a>Namn

Det här är visningsnamnet för din app och erbjudanden, som visas i Microsoft [AppSource](https://appsource.microsoft.com/). Det får innehålla högst 50 tecken.

> [!NOTE]
> Det korta namnet måste vara samma som utgivarens namn anges i appmanifestet.

Klicka på **spara** spara förloppet. Nästa steg är att lägga till teknisk information för ditt erbjudande.
