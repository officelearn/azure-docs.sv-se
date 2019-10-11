---
title: Aktivera stöd för AVERT vFXT – Azure
description: Så här aktiverar du support överföringar från AVERT vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: ac7db46a681fcde6bfcbb7695e2d66724f738918
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256226"
---
# <a name="enable-support-uploads"></a>Aktivera stöd för uppladdningar

AVERT vFXT för Azure kan automatiskt överföra support data om klustret. De här överföringarna gör att support personalen ger bästa möjliga kund service.

## <a name="steps-to-enable-uploads"></a>Steg för att aktivera uppladdningar

Följ de här stegen på kontroll panelen aver för att aktivera stöd. (Läs [åtkomst till vFXT-klustret](avere-vfxt-cluster-gui.md) för att lära dig hur du öppnar AVERT-kontroll panelen.)

1. Gå till fliken **Inställningar** längst upp.
1. Klicka på **support** länken till vänster och godkänn sekretess policyn.

   ![Skärm bild som visar fliken "Ange kontroll panel" och popup-fönster med knappen Bekräfta för att godkänna sekretess policyn](media/avere-vfxt-privacy-policy.png)

1. Klicka på triangeln till vänster om **kund information** för att expandera avsnittet.
1. Klicka på knappen **Verifiera om upload information** .
1. Ange klustrets support namn i **unikt kluster namn** – se till att det unikt identifierar klustret så att det stöder personal.
1. Markera kryss rutorna för **statistik övervakning**, **allmän informations uppladdning**och **krasch informations uppladdning**.
1. Klicka på **Skicka**.

   ![Skärm bild som innehåller avsnittet om slutförd kund information på sidan support inställningar](media/avere-vfxt-support-info.png)

1. Klicka på triangeln till vänster om **Secure proactive support (SPS)** för att expandera avsnittet.
1. Markera kryss rutan **Aktivera SPS-länk**.
1. Klicka på **Skicka**.

   ![Skärm bild som innehåller ett slutfört Secure proactive support-avsnitt på sidan stöd inställningar](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Nästa steg

Om du behöver lägga till ett lokalt eller befintligt moln lagrings system i klustret följer du anvisningarna i [Konfigurera lagring](avere-vfxt-add-storage.md). 

Om du är redo att börja ansluta klienter till klustret kan du läsa [det Avera vFXT-klustret](avere-vfxt-mount-clients.md).