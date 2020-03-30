---
title: Distribuera StorSimple Virtual Array för molnlösningsproviderprogram
description: En översikt över StorSimple och CSP för StorSimple-partners.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: 7f1927a67127766c72be463c283225135b2a2aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77466917"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Distribuera StorSimple Virtual Array för molnlösningsproviderprogram

## <a name="overview"></a>Översikt

StorSimple Virtual Array kan distribueras av CSP-partner (Cloud Solution Provider) för sina kunder. En CSP-partner kan skapa en StorSimple Device Manager-tjänst. Den här tjänsten kan sedan användas för att distribuera och hantera StorSimple Virtual Array och tillhörande resurser, volymer och säkerhetskopior.

I den här artikeln beskrivs hur en CSP-partner kan lägga till en kund eller en ny prenumeration på en befintlig kund och sedan skapa en tjänst för att distribuera en StorSimple Virtual Array i CSP.

## <a name="prerequisites"></a>Krav

Innan du börjar, se till att:

- Du är inskriven under CSP-programmet.
- Du har giltiga inloggningsuppgifter för [Partner Center.](https://partnercenter.microsoft.com/) Med autentiseringsuppgifterna kan du logga in på partnerportalen för att lägga till nya kunder, söka efter kunder eller navigera till ett kundkonto från partnerinstrumentpanelen. CSP kan fungera som StorSimple-administratör för kundens räkning i Azure-portalen.
                             
## <a name="add-a-customer"></a>Lägga till en kund

Om du lägger till en kund skapas en prenumeration automatiskt. Om du vill lägga till en kund (och automatiskt skapa en prenumeration) utför du följande steg i partnerportalen.

1. Gå till [Partner center](https://partnercenter.microsoft.com/) och logga in med dina CSP-autentiseringsuppgifter. Klicka på **Instrumentpanel**.

     ![Instrumentpanel i Partnercenter](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klicka på **Kunder**i den vänstra rutan . Klicka på **Lägg till kunder**i den högra rutan . Ange kundens information. Klicka på **Nästa: Prenumerationer** för att skapa en kundprenumeration.

    ![Lägg till kund](./media/storsimple-partner-csp-deploy/image2.png)

3.  Välj **Microsoft Azure-erbjudande.** Bläddra längst ned på sidan och klicka på **Granska**.

    ![Granska prenumerationsinformation](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Granska informationen och klicka på **Skicka**.

    ![Skicka prenumeration](./media/storsimple-partner-csp-deploy/image4.png)

5. Spara bekräftelseinformationen för framtida referens.

    ![Spara bekräftelse](./media/storsimple-partner-csp-deploy/image5.png)

6. Hitta eller navigera till kunden du just lagt till. Klicka på **företagsnamnet** om du vill öka detaljnivån i informationen.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. Välj **Tjänsthantering**i den vänstra rutan . Klicka på **Microsoft Azure Management Portal** i den högra rutan för att logga in som Azure-administratör för kunden under Administrera **tjänster.**

    ![Logga in på Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Om du vill skapa en StorSimple-enhetshanterare klickar du på **+ Ny** och söker efter eller navigerar till **StorSimple Virtual Device Series**. Mer information finns i [Distribuera en StorSimple Enhetshanteraren-tjänst](storsimple-virtual-array-manage-service.md).

    ![Skapa tjänsten StorSimple Enhetshanteraren](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Lägga till en prenumeration

I vissa fall kan du ha en befintlig kund och du måste lägga till en prenumeration. Om du vill lägga till en prenumeration på en befintlig kund utför du följande steg i partnerportalen.

1. Gå till [Partner center](https://partnercenter.microsoft.com/) och logga in med dina CSP-autentiseringsuppgifter. Klicka på **Instrumentpanel**.

     ![Instrumentpanel i Partnercenter](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klicka på **Kunder**i den vänstra rutan . Sök eller navigera till kunden som du vill lägga till en prenumeration på. Klicka ![på ikonen](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) Expandera kontroll om du vill expandera raden för företagets namn för kunden. Klicka på **Lägg till prenumerationer**i informationen .

    ![Kunder](./media/storsimple-partner-csp-deploy/image10.png)

3. Sök i **Microsoft Azure** efter de **bästa erbjudandena** i prenumerationen och klicka på **Skicka**. Detta skapar en ny prenumeration.

    ![Lägga till ny prenumeration](./media/storsimple-partner-csp-deploy/image11.png)

6. När en ny prenumeration har skapats klickar du på **<– Kunder** i den vänstra rutan för att återgå till sidan **Kunder.** Sök efter kunden som du just har skapat en prenumeration för. Klicka på **företagsnamnet** om du vill öka detaljnivån i informationen.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. Välj **Tjänsthantering**i den vänstra rutan . Klicka på **Microsoft Azure Management Portal** i den högra rutan för att logga in som Azure-administratör för kunden under Administrera **tjänster.**

    ![Logga in på Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Om du vill skapa en StorSimple-enhetshanterare klickar du på **+ Ny** och söker efter eller navigerar till **StorSimple Virtual Device Series**. Mer information finns i [Distribuera en StorSimple Enhetshanteraren-tjänst](storsimple-virtual-array-manage-service.md).

    ![Skapa tjänsten StorSimple Enhetshanteraren](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Nästa steg

- Om du har fler frågor om StorSimple i CSP, gå till [StorSimple i CSP: Vanliga frågor](storsimple-partner-csp-faq.md).
- Om du är redo att distribuera din StorSimple går du till [Distribuera din StorSimple i CSP](storsimple-partner-csp-deploy.md).
