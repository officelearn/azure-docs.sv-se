---
title: Aktivera stöd för Avere vFXT - Azure
description: Så här aktiverar du stöd för filöverföringar från Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: bc21e06b704bfe3d25132092efbbf23f342acb14
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634310"
---
# <a name="enable-support-uploads"></a>Aktivera stöd för överföring

Avere vFXT för Azure kan automatiskt ladda upp supportinformation om ditt kluster. Dessa överföringar kan supportpersonal ger bästa möjliga kundservice.

## <a name="steps-to-enable-uploads"></a>Steg för att aktivera uppladdningar

Följ dessa steg från Kontrollpanelen Avere att aktivera stöd för. (Läs [åtkomst till klustret vFXT](avere-vfxt-cluster-gui.md) och lär dig att öppna Avere på Kontrollpanelen.)

1. Navigera till den **inställningar** fliken högst upp.
1. Klicka på den **Support** länka till vänster och Godkänn sekretesspolicyn.

   ![Skärmbild för att bekräfta godkännande av sekretesspolicy](media/avere-vfxt-privacy-policy.png)
1. Klicka på triangeln till vänster om **kundinformation** att expandera avsnittet.
1. Ange klusternamnet stöd i **unika klusternamnet** – Kontrollera att den identifierar ditt kluster för att stödja personal.
1. Markera kryssrutorna för **statistik övervakning**, **allmän Information överför**, och **krascha ladda upp Information**.
1. Klicka på den **verifiera överföringsinformation** knappen.
1. Klicka på **skicka**.
1. Klicka på triangeln till vänster om **skydda proaktiv Support (Service Pack)** att expandera avsnittet.
1. Markera kryssrutan för **aktivera Service Pack-länk**.
1. Klicka på **skicka**.

   ![Skärmbild som innehåller alla steg för att aktivera stöd](media/avere-vfxt-support-info-steps.png)


## <a name="next-steps"></a>Nästa steg

Om du vill lägga till en lokal lagringssystemet i klustret, följ instruktionerna i [konfigurerar du lagring](avere-vfxt-add-storage.md). 

Om du är redo att börja ansluta klienter till klustret, läsa [montera Avere vFXT klustret](avere-vfxt-mount-clients.md).