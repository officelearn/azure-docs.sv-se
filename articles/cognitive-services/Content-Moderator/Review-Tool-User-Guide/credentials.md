---
title: Autentiseringsuppgifter i Azure innehåll kontrollant | Microsoft Docs
description: 'Hantera innehåll kontrollant autentiseringsuppgifter som ska användas med API: erna.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351519"
---
# <a name="manage-credentials"></a>Hantera autentiseringsuppgifter

Autentiseringsuppgifterna innehåll kontrollant skapas på följande platser:

- [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [Verktyget innehåll kontrollant granskning](http://contentmoderator.cognitive.microsoft.com/)

Den här artikeln förklarar var du hittar dem och hur de relaterar till varandra.

## <a name="the-azure-portal"></a>Azure-portalen

Välj ditt innehåll kontrollant konto på Azure portal instrumentpanelen. Under **resurshantering**väljer **nycklar**. Klicka på ikonen till höger om nyckeln för att kopiera nyckeln.

![Innehåll kontrollant nycklar i Azure-portalen](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>Hur du använder ditt Azure-konto med verktyget granskning
Om du vill använda din Azure-nyckel med granska API: er, kopiera resurs-ID som visas på den **egenskaper** skärmen i följande skärmbild och ange den på verktyget granska autentiseringsuppgifter skärm i den **godkända resurs-ID** fält som visas i följande **resurs-ID** avsnitt. 

Om du vill använda din Azure-nyckel för arbetsflöden finns i innehåll kontrollant, anger du den i den **Ocp-Apim-prenumeration-nyckeln** i den **inställningar för arbetsflöde** avsnittet som visas i följande  **Arbetsflöden** avsnitt.

> [!NOTE]
> När du ersätter två platser i verktyget granska med nyckeln och resurs-ID från din Azure-prenumeration din **utvärderingsversion Ocp-Apim-prenumeration-nyckeln** visas på de autentiseringsuppgifter som skärmen inte längre används men alltid är tillgängligt.
> Nyckeln utvärderingsversion begränsar du maximalt 5 000 transaktioner per månad på 1 begäran per sekund (RPS).

![Innehåll kontrollant resurs-ID i Azure-portalen](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>Granska-verktyget

Verktyget instrumentpanelen på granskningen på den **inställningar** väljer **autentiseringsuppgifter**.

![Innehåll kontrollant autentiseringsuppgifter i verktyget granskning](images/credentials-trial-resource-workflow.PNG)

Följande avsnitt undersöks föregående bilden i detalj:


### <a name="api"></a>API

De första delen listorna din **granska API-slutpunkt**, **team ID**, och **Ocp-Apim-prenumeration-nyckel (innehåll kontrollant utvärderingsversion key)** skapas som en del av din grupp för granskning Skapa. Använd dem för att anropa alla innehåll kontrollant API: er, inklusive granska API.

Tänk också på din regionsidentifierare för API-slutpunkt. Till exempel **westus** är regionen i ”https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0”

![Kontrollant innehållsnyckeln i verktyget granskning](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>Resurs-ID

De andra delen startar ut som tom med inga resurs-ID. **Om du vill använda din nyckel för Azure-prenumeration med granska API, gå till skärmen resurs-ID som visas tidigare och kopiera den till fältet visas**. Tryck på **”+”** att spara din resurs-ID.

> [!NOTE]
> Granska teamet region att identifiera din grupp och dataåtkomst team måste överensstämma med prenumerationen innehåll kontrollant region. Till exempel i bilder på den här sidan i **västra USA** region **(4)** innehåller innehåll kontrollant Azure-prenumeration och granska-teamet.

![Innehåll kontrollant resurs-ID i verktyget granskning](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Arbetsflöden

Den tredje delen visar den information som används för att köra arbetsflöden. Startar med den automatisk genererade utvärderingsversion nyckeln som standard. 

**Uppdatera den med din Azure-nyckel när du får en Azure-prenumeration**. De andra två fälten Tillåt med respektive termen och image listor i Skärmtext- och utvärdera åtgärder.

![Innehåll kontrollant arbetsflödet autentiseringsuppgifter i verktyget granskning](images/credentials-workflow.PNG)


## <a name="next-steps"></a>Nästa steg

* Lär dig hur du använder innehåll kontrollant autentiseringsuppgifter i din [arbetsflöden](workflows.md).
