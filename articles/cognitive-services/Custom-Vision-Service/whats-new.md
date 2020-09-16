---
title: Vad är nytt i Custom Vision?
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller nyheter om Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 019a9264beddafbd4585810967551e064592e94d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602531"
---
# <a name="whats-new-in-custom-vision"></a>Vad är nytt i Custom Vision

Läs om vad som är nytt i tjänsten. Dessa objekt kan vara viktig information, videor, blogg inlägg och andra typer av information. Skapa ett bok märke för den här sidan för att hålla dig uppdaterad med tjänsten.

## <a name="july-2020"></a>Juli 2020

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

* Custom Vision stöder rollbaserad åtkomst kontroll i Azure (Azure RBAC), ett auktoriserings system för att hantera individuell åtkomst till Azure-resurser. Information om hur du hanterar åtkomst till dina Custom Vision-projekt finns i [rollbaserad åtkomst kontroll](./role-based-access-control.md).

### <a name="subset-training"></a>Del uppsättning utbildning

* När du tränar ett objekt identifierings projekt kan du välja att bara träna på en delmängd av dina använda taggar. Du kanske vill göra detta om du inte har använt tillräckligt med vissa Taggar ännu, men du har tillräckligt med andra. Följ snabb starten för [klient biblioteket](./quickstarts/object-detection.md) för C# eller python om du vill veta mer.

### <a name="azure-storage-notifications"></a>Azure Storage-meddelanden

* Du kan integrera ditt Custom Vision-projekt med en Azure Blob Storage-kö för att få push-meddelanden om projekt utbildning/-export-aktivitet och säkerhets kopior av publicerade modeller. Den här funktionen är användbar för att undvika kontinuerlig avsökning av tjänsten för resultat när tids krävande åtgärder körs. I stället kan du integrera meddelanden i lagrings kön i ditt arbets flöde. Mer information finns i [lagrings integrerings](./storage-integration.md) guiden.

### <a name="copy-and-move-projects"></a>Kopiera och flytta projekt

* Nu kan du kopiera projekt från ett Custom Vision konto till andra. Du kanske vill flytta ett projekt från en utveckling till produktions miljön eller säkerhetskopiera ett projekt till ett konto i en annan Azure-region för ökad data säkerhet. Läs mer i guiden [Kopiera och flytta projekt](./copy-move-projects.md) .

## <a name="september-2019"></a>September 2019

### <a name="suggested-tags"></a>Föreslagna Taggar

* Verktyget Smart Labeler på [Custom vision webbplats](https://www.customvision.ai/) genererar föreslagna taggar för dina utbildnings bilder. På så sätt kan du märka ett stort antal bilder snabbare när du tränar en Custom Vision modell. Instruktioner för hur du använder den här funktionen finns i [föreslagna Taggar](./suggested-tags.md).

## <a name="cognitive-service-updates"></a>Uppdatering av kognitiva tjänster

[Azure Update-meddelanden för Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)