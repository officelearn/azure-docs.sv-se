---
title: ta med fil
description: inkludera en fil med text för Microsoft Commercial Marketplace standard-kontrakt
documentationcenter: partner-center-commercial-marketplace
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: e7692d8c90d71e76628ecb44ade529eabedfa909
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691525"
---
Microsoft tillhandahåller en standard kontrakts mal len för den kommersiella marknads platsen.

- **Använd standard kontraktet för Microsoft Commercial Marketplace?**

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft ett standard kontrakt för Microsofts kommersiella marknads plats för att under lätta transaktioner i Marketplace. I stället för att utforma anpassade villkor, kan kommersiella Marketplace-utgivare välja att erbjuda sin program vara under standard kontraktet, som kunderna bara behöver Undersök och godkänna en gång. Du hittar standard kontraktet här: https://go.microsoft.com/fwlink/?linkid=2041178.

Du kan välja att använda standard kontraktet i stället för att ange egna anpassade villkor genom att markera kryss rutan Använd standard kontraktet för den kommersiella Marketplace.

![Använda standard kontrakt kryss rutan](./media/use-standard-contract.png)

> [!NOTE]
> När du har publicerat ett erbjudande med standard kontraktet för Microsoft Marketplace kan du inte använda dina egna anpassade villkor. Det är ett "eller"-scenario. Du erbjuder antingen din lösning enligt standard avtalet **eller** dina egna villkor. Om du vill ändra villkoren i standard kontraktet kan du göra det via standard kontrakts ändringar.

**Standard kontrakts ändringar**

Med standard kontrakts ändringar kan utgivare välja standard avtals villkoren för enkelhetens skull och anpassa villkoren för deras produkt eller verksamhet. Kunderna behöver bara granska ändringarna i kontraktet, om de redan har granskat och godkänt Microsofts standard kontrakt.

Det finns två typer av ändringar som är tillgängliga för kommersiella Marketplace-utgivare:

- Universella ändringar: dessa ändringar tillämpas universellt på standard kontraktet för alla kunder. Universella ändringar visas för varje kund av erbjudandet i inköps flödet. Kunderna måste godkänna villkoren i standard kontraktet och ändringen innan de kan använda ditt erbjudande.
- Anpassade ändringar: dessa ändringar är särskilda ändringar i standard kontraktet som endast är riktade till specifika kunder via klient-ID: n för Azure. Utgivare kan välja den klient som de vill rikta in sig på. Endast kunder från klient organisationen visas med de anpassade ändrings villkoren i erbjudandets inköps flöde.  Kunderna måste godkänna villkoren i standard kontraktet och ändringarna innan de kan använda ditt erbjudande.

>[!NOTE]
> Dessa två typer av ändringar staplas ovanpå varandra. Kunder som är riktade mot anpassade ändringar får också den universella ändringen av standard kontraktet under köpet.

**Generella ändrings villkor för standard kontraktet för Microsofts kommersiella marknads plats**: Ange villkoren för generell ändring i den här rutan. Du kan ange en enda universell ändring per erbjudande. Du kan ange ett obegränsat antal tecken i den här rutan. Dessa villkor visas för kunder i AppSource, Azure Marketplace och/eller Azure Portal under identifierings-och inköps flödet.

**Anpassade ändrings villkor till standard kontraktet för Microsofts kommersiella marknads**plats: börja genom att välja **Lägg till anpassade ändrings villkor**. Du kan ange upp till 10 anpassade ändrings villkor per erbjudande.

- **Anpassade ändrings villkor**: Ange dina anpassade ändrings villkor i rutan anpassade ändrings villkor. Du kan ange ett obegränsat antal tecken i den här rutan. Endast kunder från klient-ID: n som du anger för de här anpassade villkoren visas med villkoren för anpassad ändring i erbjudandets inköps flöde i Azure Portal.  
- **Klient-ID** (obligatoriskt): varje anpassad ändring kan riktas mot upp till 20 klient-ID: n. Om du lägger till en anpassad ändring måste du ange minst ett klient-ID. Klient-ID: t identifierar din kund i Azure. Du kan be kunden om detta ID och de kan hitta det genom att gå till portal.azure.com > Azure Active Directory > egenskaper. Värdet för katalog-ID är klient-ID (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp organisationens klient-ID för din kund genom att använda deras domän namns-URL i [Vad är mitt Microsoft Azure-och Office 365-klient-ID?](https://www.whatismytenantid.com).
- **Beskrivning** (valfritt): om du vill kan du ange en egen beskrivning för klient-ID: t som hjälper dig att identifiera kunden som du är mål för med ändringen.

**Villkor**

Om du vill ange egna anpassade villkor kan du välja att ange dem i fältet allmänna villkor. Du kan ange upp till 10 000 tecken text i det här fältet. Om dina villkor kräver en längre Beskrivning anger du en enda URL-länk i det här fältet där du kan hitta dina villkor. Den kommer att visas för kunder som en aktiv länk.

Kunder måste acceptera dessa villkor innan de kan testa ditt erbjudande.

Välj **Spara utkast** innan du fortsätter.
