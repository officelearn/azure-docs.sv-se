---
title: Aktivera stöd för Avere vFXT - Azure
description: Så här aktiverar du stöd för filöverföringar från Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: fe096b2e2a75cc89e3ce5ef905d8e4c347cc153a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409861"
---
# <a name="enable-support-uploads"></a>Aktivera stöd för uppladdningar

Avere vFXT för Azure kan automatiskt ladda upp supportinformation om ditt kluster. Dessa överföringar kan supportpersonal ger bästa möjliga kundservice.

## <a name="steps-to-enable-uploads"></a>Steg för att aktivera uppladdningar

Följ dessa steg från Kontrollpanelen Avere att aktivera stöd för. (Läs [åtkomst till klustret vFXT](avere-vfxt-cluster-gui.md) och lär dig att öppna Avere på Kontrollpanelen.)

1. Navigera till den **inställningar** fliken högst upp.
1. Klicka på den **Support** länka till vänster och Godkänn sekretesspolicyn.

   ![Skärmbild som visar Avere på Kontrollpanelen och popup-fönster med bekräfta-knappen för att acceptera sekretesspolicy](media/avere-vfxt-privacy-policy.png)

1. Klicka på triangeln till vänster om **kundinformation** att expandera avsnittet.
1. Klicka på den **Revalidate överföringsinformation** knappen.
1. Ange klusternamnet stöd i **unika klusternamnet** – Kontrollera att den identifierar ditt kluster för att stödja personal.
1. Markera kryssrutorna för **statistik övervakning**, **allmän Information överför**, och **krascha ladda upp Information**.
1. Klicka på **Skicka**.

   ![Skärmbild som innehåller slutfört kunden info-avsnittet i inställningssidan för support](media/avere-vfxt-support-info.png)

1. Klicka på triangeln till vänster om **skydda proaktiv Support (Service Pack)** att expandera avsnittet.
1. Markera kryssrutan för **aktivera Service Pack-länk**.
1. Klicka på **Skicka**.

   ![Skärmbild som innehåller slutfört skydda proaktiv stöder avsnittet på inställningssidan för support](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Nästa steg

Om du behöver lägga till en lokal eller befintliga cloud storage-system i klustret, följer du anvisningarna i [konfigurerar du lagring](avere-vfxt-add-storage.md). 

Om du är redo att börja ansluta klienter till klustret, läsa [montera Avere vFXT klustret](avere-vfxt-mount-clients.md).