---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 842083f0f69552fb9076423386353dbb4dae73b5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "34012819"
---
1. Klicka på den **Anslut** på egenskapssidan för virtuell dator. 
2. I den **Anslut till den virtuella datorn** , hålla väljer lämpliga alternativ och klickar på **ladda ned RDP-filen**.
2. Öppna den hämta RDP-filen och klicka på **Anslut** när du tillfrågas. 
2. Du får en varning att `.rdp`-filen kommer från en okänd utgivare. Detta är normalt. Fortsätt genom att klicka på **Anslut** i fjärrskrivbordsfönstret.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och klicka sedan på **Ok**.
   
     **Lokalt konto**: detta är vanligtvis det lokala kontots användarnamn och lösenord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    **Domänansluten VM**: Om den virtuella datorn tillhör en domän, anger du användarnamnet i formatet *Domän*&#92;*Användarnamn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontroller**: om den virtuella datorn är en domänkontroller, anger di användarnamn och lösenord för ett domänadministratörskonto för den domänen.
4. Klicka på **Ja** för att verifiera den virtuella datorns identitet och slutföra inloggning.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/virtual-machines-log-on-win-server/cert-warning.png)

