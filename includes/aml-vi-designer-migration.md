---
title: ta med fil
description: ta med fil
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75541808"
---
1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com).

1. Uppgradera din arbets yta till Enterprise Edition.

    Efter uppgraderingen kommer alla dina visuella gränssnitts experiment att konverteras till pipeline-utkast i designern.
    
    > [!NOTE]
    > Du behöver inte uppgradera till Enterprise-versionen för att konvertera webb tjänster för visuella gränssnitt till slut punkter i real tid.
    
1. Gå till design avsnittet i arbets ytan om du vill visa en lista över pipeline-utkast. 
    
    Du hittar de konverterade webb tjänsterna genom att navigera till **slut** > punkter i**real tid i real tid**.

1. Välj ett pipeline-utkast för att öppna det.

    Om det uppstod ett fel under konverterings processen visas ett fel meddelande med instruktioner för att lösa problemet. 

### <a name="known-issues"></a>Kända problem

 Nedan visas kända problem vid migrering som måste åtgärdas manuellt:

- **Importera data** eller **Exportera** Datamoduler
        
    Om du har en **Importera data** -eller **Exportera data** -modul i experimentet måste du uppdatera data källan för att använda ett data lager. Information om hur du skapar ett data lager finns i [så här får du åtkomst till data i Azure Storage-tjänster](../articles/machine-learning/how-to-access-data.md). Informationen om ditt moln lagrings konto har lagts till i kommentarerna i modulen **Importera data** eller **Exportera data** . 
      