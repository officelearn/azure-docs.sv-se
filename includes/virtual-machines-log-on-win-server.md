---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 100e0457627a04414ad2f0780a346933b11f4376
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
1. Om du klickar på **Anslut** så skapas och hämtas en protokollfil för fjärrskrivbord (.rdp-fil). Klicka på **Öppna** för att använda filen.
2. Du får en varning att `.rdp`-filen kommer från en okänd utgivare. Detta är normalt. Fortsätt genom att klicka på **Anslut** i fjärrskrivbordsfönstret.
   
    ![Skärmbild med ett varning som meddelar att utgivaren är okänd.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. I fönstret **Windows-säkerhet** väljer du **fler alternativ** och sedan **använd ett annat konto**. Ange autentiseringsuppgifterna för ett konto på den virtuella datorn och klicka sedan på **Ok**.
   
     **Lokalt konto**: detta är vanligtvis det lokala kontots användarnamn och lösenord som du angav när du skapade den virtuella datorn. I det här fallet är domänen namnet på den virtuella datorn och är registrerad som *vmnamn*&#92;*användarnamn*.  
   
    **Domänansluten VM**: Om den virtuella datorn tillhör en domän, anger du användarnamnet i formatet *Domän*&#92;*Användarnamn*. Kontot måste också antingen vara i gruppen administratörer eller ha beviljats behörighet för fjärråtkomst för den virtuella datorn.
   
    **Domänkontroller**: om den virtuella datorn är en domänkontroller, anger di användarnamn och lösenord för ett domänadministratörskonto för den domänen.
4. Klicka på **Ja** för att verifiera den virtuella datorns identitet och slutföra inloggning.
   
   ![Skärmbild som visar ett meddelande om verifieringen av den virtuella datorns identitet.](./media/virtual-machines-log-on-win-server/cert-warning.png)

