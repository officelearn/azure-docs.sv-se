---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005646"
---
## <a name="message-headers"></a>Meddelandehuvuden
Detta är de egenskaper du får i meddelande huvudena:

| Egenskapsnamn | Description |
| ------------- | ----------- | 
| AEG-prenumeration-namn | Namn på händelse prenumerationen. |
| AEG – antal | Antal försök som har gjorts för händelsen. |
| AEG-händelse-Type | <p>Typ av händelse.</p><p>Det kan vara något av följande värden:</p><ul><li>SubscriptionValidation</li><li>Meddelande</li><li>SubscriptionDeletion</li></ul> | 
| AEG – metadata-version | <p>Händelsens metadata-version.<p> För **Event Grid händelse schema** representerar den här egenskapen metadata-versionen och för **moln händelse schema**, den representerar **Specifikations versionen**. </p>|
| AEG – data version | <p>Händelsens data version.</p><p>För **Event Grid händelse schema** representerar den här egenskapen data versionen och för **moln händelse schema**, den gäller inte.</p> |
| AEG-utdata-Event-ID | ID för Event Grid händelsen. |


