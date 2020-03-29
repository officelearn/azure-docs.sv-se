---
title: Aktivera stöd för Avere vFXT - Azure
description: Aktivera supportuppladdningar från Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: d12bbd1708ceb948aea982f9ed1ab36879e3751c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75415370"
---
# <a name="enable-support-uploads"></a>Aktivera stöd för uppladdningar

Avere vFXT för Azure kan automatiskt ladda upp supportdata om ditt kluster. Dessa uppladdningar låter supportpersonalen ge bästa möjliga kundservice.

## <a name="steps-to-enable-uploads"></a>Steg för att aktivera uppladdningar

Följ dessa steg från Avere Kontrollpanelen för att aktivera stöd. (Läs [Access vFXT-klustret](avere-vfxt-cluster-gui.md) för att lära dig hur du öppnar kontrollpanelen.)

1. Navigera till fliken **Inställningar** högst upp.
1. Klicka på **länken Support** till vänster och acceptera sekretesspolicyn.

   ![Skärmbild som visar Avere Kontrollpanel och popup-fönster med knappen Bekräfta för att acceptera sekretesspolicyn](media/avere-vfxt-privacy-policy.png)

1. Öppna avsnittet Kundinformation på sidan **Supportkonfiguration** genom att klicka på triangeln till vänster.
1. Klicka på knappen **Ladda upp information om revalidate.**
1. Ange klustrets supportnamn i **Unikt klusternamn**. Kontrollera att det här namnet unikt identifierar klustret för att stödja personal.
1. Markera rutorna för **statistikövervakning,** **allmän informationsuppladdning**och **kraschinformationsöverföring**.
1. Klicka på **Skicka**.

   ![Skärmbild som innehåller avsnittet med slutförd kundinformation på sidan supportinställningar](media/avere-vfxt-support-info.png)

1. Klicka på triangeln till vänster om **SPS (Secure Proactive Support)** för att expandera avsnittet.
1. Markera kryssrutan Aktivera **SPS Link**.
1. Klicka på **Skicka**.

   ![Skärmbild som innehåller det slutförda avsnittet Säker proaktiv support på sidan Supportinställningar](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Nästa steg

Om du behöver lägga till ett lokalt eller befintligt molnlagringssystem i klustret följer du anvisningarna i [Konfigurera lagring](avere-vfxt-add-storage.md).

Om du är redo att börja koppla klienter till klustret läser du [Montera Avere vFXT-klustret](avere-vfxt-mount-clients.md).
