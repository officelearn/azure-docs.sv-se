---
title: Autentiseringsuppgifter i Azure Content Moderator | Microsoft Docs
description: 'Hantera Content Moderator autentiseringsuppgifter som ska användas med API: erna.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 911d62fc2baa50cce6d40f8b5bcc455f308d21d7
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309663"
---
# <a name="manage-credentials"></a>Hantera autentiseringsuppgifter

Dina autentiseringsuppgifter för Content Moderator skapas på följande platser:

- [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Content Moderator-granskningsverktyget](http://contentmoderator.cognitive.microsoft.com/)

Den här artikeln förklarar var du hittar dem och hur de relaterar till varandra.

## <a name="the-azure-portal"></a>Azure Portal

På instrumentpanelen för Azure portal, väljer du ditt konto med Content Moderator. Under **resurshantering**väljer **nycklar**. Välj ikonen till höger om nyckeln för att kopiera nyckeln.

![Content Moderator-nycklar i Azure portal](images/credentials-azure-portal-keys.PNG)

### <a name="use-the-azure-account-with-the-review-tool-and-review-api"></a>Använda Azure-konto med verktyg för granskning och granska API
Om du vill använda din Azure-nyckel med granska API: er, kopiera det resurs-ID som visas på den **egenskaper** skärmen på följande skärmbild och ange det verktyg för granskning autentiseringsuppgifter skärmen i den **godkänd resurs-ID(n)** fält som visas i följande **resurs-ID** avsnittet. 

> [!NOTE]
> Prenumerationen Content Moderator region ska matcha granska gruppens region att identifiera ditt team och dataåtkomst team. Till exempel i bilder på den här sidan i **västra USA** region **(4)** innehåller Content Moderator-Azure-prenumeration och ditt granskningsteam.
>
> När du ersätta de två platser i granskningsverktyget med nyckeln och resurs-ID från Azure-prenumerationen, din **utvärderingsversion Ocp-Apim-Subscription-Key** visas på autentiseringsuppgifterna skärmen används inte längre, men alltid är tillgänglig.
> Nyckeln utvärderingsperioden begränsad till maximalt 5 000 transaktioner per månad med 1 begäran per sekund (RPS).

![Content Moderator resurs-ID i Azure portal](images/credentials-azure-portal-resourceid.PNG)

### <a name="use-the-azure-account-with-the-workflows-in-the-review-tool"></a>Använda Azure-konto med arbetsflöden i granskningsverktyget

Om du vill använda din Azure-nyckel för arbetsflödena som är tillgängliga i Content Moderator, anger du den i den **Ocp-Apim-Subscription-Key** i den **arbetsflödesinställningarna** som visas i följande  **Arbetsflöden** avsnittet. Tryck på den **”+”** att spara din resurs-ID.

![Content Moderator arbetsflöde autentiseringsuppgifter i granskningsverktyget](images/credentials-workflow.PNG)

## <a name="the-review-tool"></a>Granskningsverktyget

På granskningen verktyget instrumentpanelen på den **inställningar** fliken **autentiseringsuppgifter**.

![Content Moderator-autentiseringsuppgifter i granskningsverktyget](images/credentials-trial-resource-workflow.PNG)

Följande avsnitt går igenom den föregående bilden i detalj:

### <a name="api"></a>API

De första delen listorna din **granska API-slutpunkt**, **lag-ID**, och **Ocp-Apim-Subscription-Key (Content Moderator utvärderingsversion nyckel)** genereras som en del av ditt granskningsteam Skapa. Du kan använda dem för att anropa alla Content Moderator API: er, inklusive granska API.

Tänk också på din regionsidentifierare för din API-slutpunkt. Till exempel **westus** är regionen i ”https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0”

![Content Moderator-nyckeln i granskningsverktyget](images/credentials-trialkey.PNG)

### <a name="resource-id"></a>Resurs-ID

Den här uppsättningen fält som beskrivs i det föregående avsnittet [använda Azure-konto med verktyg för granskning och API](credentials.md#use-the-azure-account-with-the-review-tool-and-review-api). Det här fältet är vanligtvis tomt om inte du lägga till din Azure-resurs-Id i det här fältet enligt beskrivningen i föregående avsnitt.

### <a name="workflows"></a>Arbetsflöden

Den här uppsättningen fält som beskrivs i det föregående avsnittet [använda Azure-konto med arbetsflöden i granskningsverktyget](credentials.md#use-the-azure-account-with-the-workflows-in-the-review-tool). Granskningsverktyget använder den automatisk genererade utvärderingsversion nyckeln för att köra arbetsflöden som standard och det är vad som visas att. De två fälten kan med perioden och avbildning listor i Skärmtext- och utvärdera åtgärder respektive.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du använder Content Moderator autentiseringsuppgifterna i din [arbetsflöden](workflows.md).
