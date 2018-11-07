---
title: Microsoft Azure StorSimple och Molnlösningsleverantör Programöversikt | Microsoft Docs
description: En översikt om StorSimple och CSP för StorSimple-partner.
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
ms.openlocfilehash: 0dac86a696599a391cb243ad11e16931e00b8921
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238938"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Distribuera StorSimple Virtual Array för Cloud Solution Provider-programmet

## <a name="overview"></a>Översikt

StorSimple Virtual Array kan distribueras av partners Cloud Solution Provider (CSP) för sina kunder. CSP-partner kan skapa en StorSimple Device Manager-tjänsten. Den här tjänsten kan sedan användas för att distribuera och hantera StorSimple Virtual Array och associerade resurser, volymer och säkerhetskopior.

Den här artikeln beskrivs hur CSP-partner kan lägga till en kund eller en ny prenumeration till en befintlig kund och sedan skapa en tjänst för att distribuera en StorSimple Virtual Array i CSP.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du se till att:

- Du är registrerad under CSP-programmet.
- Du har giltig [Partnercenter](https://partnercenter.microsoft.com/) inloggningsuppgifter. Autentiseringsuppgifterna kan du logga in på partnerportalen för att lägga till nya kunder, söka efter kunder eller navigera till ett kundkonto från Partner-instrumentpanelen. CSP: N kan fungera som en StorSimple-administratören åt kunden i Azure-portalen.
                             
## <a name="add-a-customer"></a>Lägg till en kund

Om du lägger till en kund skapas automatiskt en prenumeration. Utför följande steg i partnerportalen för att lägga till en kund (och automatiskt skapa en prenumeration).

1. Gå till den [Partnercenter](https://partnercenter.microsoft.com/) och logga in med dina CSP-autentiseringsuppgifter. Klicka på **instrumentpanelen**.

     ![Instrumentpanelen i Partnercenter](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. I den vänstra rutan klickar du på **kunder**. I den högra rutan klickar du på **Lägg till kunder**. Ange informationen för kunden. Klicka på **nästa: prenumerationer** att skapa en kundprenumeration.

    ![Lägg till kund](./media/storsimple-partner-csp-deploy/image2.png)

3.  Välj **Microsoft Azure** erbjuder. Bläddra längst ned på sidan och klicka på **granska**.

    ![Granska information om prenumeration](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Granska informationen och klicka på **skicka**.

    ![Skicka prenumeration](./media/storsimple-partner-csp-deploy/image4.png)

5. Spara bekräftelse-information för framtida bruk.

    ![Spara bekräftelse](./media/storsimple-partner-csp-deploy/image5.png)

6. Hitta eller navigera till kunden du just har lagt till. Klicka på den **företagsnamn** att granska nedåt i detaljerna.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. I den vänstra rutan väljer **Tjänstehantering**. I den högra rutan under **administrera services**, klickar du på **Microsoft Azure-hanteringsportalen** logga in som en Azure-administratör för din kund.

    ![Logga in på Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Klicka för att skapa en StorSimple-enhetshanterare **+ ny** och Sök eller navigera till **StorSimple-serien för virtuella enheter**. Mer information går du till [distribuera en StorSimple Device Manager-tjänst](storsimple-virtual-array-manage-service.md).

    ![Skapa StorSimple Device Manager-tjänsten](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Lägga till en prenumeration

I vissa fall kan du kanske har en befintlig kund och du måste lägga till en prenumeration. Utför följande steg för att lägga till en prenumeration till en befintlig kund i partnerportalen.

1. Gå till den [Partnercenter](https://partnercenter.microsoft.com/) och logga in med dina CSP-autentiseringsuppgifter. Klicka på **instrumentpanelen**.

     ![Instrumentpanelen i Partnercenter](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. I den vänstra rutan klickar du på **kunder**. Hitta eller navigera till kunden som du vill lägga till en prenumeration på. Klicka på den ![Expandera kryssikonen](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikonen för att expandera raden för företagets namn för din kund. Mer information, klicka på **Lägg till prenumerationer**.

    ![Kunder](./media/storsimple-partner-csp-deploy/image10.png)

3. Kontrollera **Microsoft Azure** för den **viktigaste erbjudanden** i prenumerationen och på **skicka**. Detta skapar en ny prenumeration.

    ![Lägg till ny prenumeration](./media/storsimple-partner-csp-deploy/image11.png)

6. När en ny prenumeration skapas, klickar du på **<--kunder** i den vänstra rutan att återgå till den **kunder** sidan. Sök efter kunden som du precis skapat en prenumeration. Klicka på den **företagsnamn** att granska nedåt i detaljerna.

    ![Sök efter kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. I den vänstra rutan väljer **Tjänstehantering**. I den högra rutan under **administrera services**, klickar du på **Microsoft Azure-hanteringsportalen** logga in som en Azure-administratör för din kund.

    ![Logga in på Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Klicka för att skapa en StorSimple-enhetshanterare **+ ny** och Sök eller navigera till **StorSimple-serien för virtuella enheter**. Mer information går du till [distribuera en StorSimple Device Manager-tjänst](storsimple-virtual-array-manage-service.md).

    ![Skapa StorSimple Device Manager-tjänsten](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Nästa steg

- Om du har fler frågor angående StorSimple i CSP, går du till [StorSimple i CSP: vanliga frågor och svar](storsimple-partner-csp-faq.md).
- Om du är redo att distribuera StorSimple, går du till [distribuera StorSimple i CSP](storsimple-partner-csp-deploy.md).
