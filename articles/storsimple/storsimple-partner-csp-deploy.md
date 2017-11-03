---
title: "Microsoft Azure StorSimple och Molnlösningsleverantör Programöversikt | Microsoft Docs"
description: "En översikt över hur StorSimple- och CSP för StorSimple-partner."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: c8cb51093142146fc7d43b51a62d949f6cc38988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Distribuera virtuella StorSimple-matrisen för Cloud Solution Provider Program

## <a name="overview"></a>Översikt

StorSimple virtuell matris kan distribueras med partners Cloud Solution Providers (CSP) för sina kunder. En CSP-partner kan skapa en StorSimple Device Manager-tjänst. Den här tjänsten kan sedan användas för att distribuera och hantera virtuella StorSimple-matris och associerade resurser, volymer och säkerhetskopieringar.

Den här artikeln beskrivs hur en CSP-partner kan lägga till en kund eller en ny prenumeration till en befintlig kund och sedan skapa en tjänst för att distribuera en virtuell StorSimple-matris i CSP.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du se till att:

- Du är registrerad i CSP-programmet.
- Du har en giltig [Partnercenter](http://partnercenter.microsoft.com/) inloggningsuppgifter. Autentiseringsuppgifterna kan du logga in på partnerportalen för att lägga till nya kunder, söka efter kunder eller navigera till ett kundkonto från Partner-instrumentpanelen. CSP: N kan fungera som en StorSimple-administratör för kunden i Azure-portalen.
                             
## <a name="add-a-customer"></a>Lägg till en kund

Om du lägger till en kund, skapas automatiskt en prenumeration. Utför följande steg i partnerportalen för att lägga till en kund (och automatiskt skapa en prenumeration).

1. Gå till den [Partnercenter](http://partnercenter.microsoft.com/) och logga in med dina inloggningsuppgifter för CSP. Klicka på **instrumentpanelen**.

     ![Instrumentpanelen Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. I den vänstra rutan klickar du på **kunder**. I den högra rutan, klickar du på **Lägg till kunder**. Ange informationen för kunden. Klicka på **nästa: prenumerationer** att skapa en kundprenumeration.

    ![Lägg till kund](./media/storsimple-partner-csp-deploy/image2.png)

3.  Välj **Microsoft Azure** erbjuder. Bläddra längst ned på sidan och klicka på **granska**.

    ![Granska information om prenumerationen](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Granska informationen och klicka på **skicka**.

    ![Skicka prenumeration](./media/storsimple-partner-csp-deploy/image4.png)

5. Spara bekräftelse-information för framtida bruk.

    ![Spara-bekräftelse](./media/storsimple-partner-csp-deploy/image5.png)

6. Sök eller navigera till kunden du just lagt till. Klicka på den **företagsnamn** och öka detaljnivån till informationen.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. I den vänstra rutan, Välj **Service management**. I den högra rutan under **administrera tjänster**, klickar du på **Microsoft Azure Management Portal** att logga in som administratör Azure för kunden.

    ![Logga in på Azure-portalen](./media/storsimple-partner-csp-deploy/image9.png)

8. Klicka för att skapa en StorSimple-Enhetshanteraren **+ ny** och söka efter eller navigera till **StorSimple virtuell enhet serien**. Mer information finns på [distribuera en tjänst för StorSimple Enhetshanteraren](storsimple-virtual-array-manage-service.md).

    ![Skapa StorSimple enheten Manager-tjänsten](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Lägga till en prenumeration

I vissa fall kan du kanske har en befintlig kund och du måste lägga till en prenumeration. Utför följande steg om du vill lägga till en prenumeration till en befintlig kund i partnerportalen.

1. Gå till den [Partnercenter](http://partnercenter.microsoft.com/) och logga in med dina inloggningsuppgifter för CSP. Klicka på **instrumentpanelen**.

     ![Instrumentpanelen Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. I den vänstra rutan klickar du på **kunder**. Sök eller navigera till kunden som du vill lägga till en prenumeration på. Klicka på den ![Expandera kryssikonen](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikon för att expandera raden för företagsnamnet för kunden. Klicka på information, **lägga till prenumerationer**.

    ![Kunder](./media/storsimple-partner-csp-deploy/image10.png)

3. Kontrollera **Microsoft Azure** för den **uppifrån erbjudanden** i prenumerationen och på **skicka**. Detta skapar en ny prenumeration.

    ![Lägg till ny prenumeration](./media/storsimple-partner-csp-deploy/image11.png)

6. När en ny prenumeration skapas, klickar du på **<--kunder** i den vänstra rutan att återgå till den **kunder** sidan. Söka efter kunder som du just har skapat en prenumeration. Klicka på den **företagsnamn** och öka detaljnivån till informationen.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. I den vänstra rutan, Välj **Service management**. I den högra rutan under **administrera tjänster**, klickar du på **Microsoft Azure Management Portal** att logga in som administratör Azure för kunden.

    ![Logga in på Azure-portalen](./media/storsimple-partner-csp-deploy/image9.png)

8. Klicka för att skapa en StorSimple-Enhetshanteraren **+ ny** och söka efter eller navigera till **StorSimple virtuell enhet serien**. Mer information finns på [distribuera en tjänst för StorSimple Enhetshanteraren](storsimple-virtual-array-manage-service.md).

    ![Skapa StorSimple enheten Manager-tjänsten](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Nästa steg

- Om du har fler frågor om den virtuella StorSimple i CSP, gå till [StorSimple i CSP: vanliga frågor och](storsimple-partner-csp-faq.md).
- Om du är redo att distribuera din StorSimple, gå till [distribuera din StorSimple i CSP](storsimple-partner-csp-deploy.md).
