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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541808"
---
1. Logga in på [Azure Machine Learning studio](https://ml.azure.com).

1. Uppgradera arbetsytan till Enterprise Edition.

    Efter uppgraderingen konverteras alla experiment med visuellt gränssnitt till pipeline-utkast i designern.
    
    > [!NOTE]
    > Du behöver inte uppgradera till Enterprise-utgåvan för att konvertera webbtjänster för visuellt gränssnitt till slutpunkter i realtid.
    
1. Gå till designeravsnittet på arbetsytan och visa listan över pipeline-utkast. 
    
    Konverterade webbtjänster kan hittas genom att navigera till **slutpunkterslutpunkter** > **i realtid**.

1. Välj ett pipeline-utkast för att öppna det.

    Om det uppstod ett fel under konverteringsprocessen visas ett felmeddelande med instruktioner för att lösa problemet. 

### <a name="known-issues"></a>Kända problem

 Nedan finns kända migreringsproblem som måste åtgärdas manuellt:

- **Importera data-** eller **exportdatamoduler**
        
    Om du har en **module för importdata** eller **exportdata** i experimentet måste du uppdatera datakällan för att använda ett datalager. Mer information om hur du skapar ett datalager finns [i Så här kommer du åt data i Azure-lagringstjänster](../articles/machine-learning/how-to-access-data.md). Din molnlagringskontoinformation har lagts till i kommentarerna i modulen **Importera data** eller **Exportera data** för din bekvämlighet. 
      