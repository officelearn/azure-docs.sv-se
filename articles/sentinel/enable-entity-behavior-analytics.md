---
title: Använd entitets beteende analys för att identifiera avancerade hot | Microsoft Docs
description: Aktivera användar-och enhets beteende analyser i Azure Sentinel och konfigurera data källor
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997114"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Aktivera användar-och enhets beteende analys (UEBA) i Azure Sentinel 



## <a name="prerequisites"></a>Förutsättningar

- Användaren måste tilldelas rollen som **Global administratör** eller **säkerhets administratör** i Azure AD för att kunna aktivera eller inaktivera UEBA, men inte köra den.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Så här aktiverar du användar-och enhets beteende analyser

1. På navigerings menyn i Azure Sentinel väljer du **enhets beteende (för hands version)**.

1. Under rubriken **aktiverar du den**och växlar sedan till **på**.

1. Klicka på knappen **Välj data källor** .

1. Markera kryss rutorna bredvid de data källor som du vill aktivera UEBA för i fönstret **Val av data källa** och välj sedan **Använd**.

    > [!NOTE]
    >
    > I den nedre halvan av fönstret för **Val av data källor** visas en lista över UEBA-stödda data källor som du ännu inte har aktiverat. 
    >
    > När du har aktiverat UEBA kan du välja att när du ansluter nya data källor så att de kan UEBA direkt från data anslutnings fönstret om de är UEBA-kompatibla.

1. Välj **gå till enhets sökning**. Då kommer du till entitetens sökfönster, som nu är det du ser när du väljer **enhets beteende** från huvud menyn i Azure Sentinel.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du aktiverar och konfigurerar användar-och enhets beteende analys (UEBA) i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
