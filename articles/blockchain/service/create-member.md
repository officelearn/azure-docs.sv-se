---
title: Skapa en Azure blockchain service-medlem med hjälp av Azure Portal
description: Använd Azure blockchain-tjänsten för att skapa en blockchain-medlem.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: c87f9a044d6655b2062f2c2657d3b465699e0dd2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329251"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snabb start: skapa en Azure blockchain-tjänst blockchain-medlem med hjälp av Azure Portal

Azure blockchain-tjänsten är en blockchain-plattform som du kan använda för att köra affärs logiken inom ett smart kontrakt. Den här snabb starten visar hur du kommer igång genom att skapa en blockchain-medlem med hjälp av Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Skapa en blockkedjemedlem

Skapa en blockchain-medlem som kör kvorumresursens huvud protokoll i ett nytt eller befintligt konsortium.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **Blockchain** > **Azure blockchain-tjänsten**.
1. Slutför mallen.

    ![Skapa tjänst](./media/create-member/create-member.png)

    Inställning | Beskrivning
    --------|------------
    Blockchain-medlem | Välj ett unikt namn som identifierar din Azure blockchain service blockchain-medlem. Blockchain-medlems namnet får bara innehålla gemena bokstäver och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken långt.
    Prenumeration | Välj den Azure-prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
    Region | Platsen måste vara samma för alla medlemmar i konsortiet.
    Konto lösen ord för medlem | Medlems kontots lösen ord används för att kryptera den privata nyckeln för det Ethereum-konto som skapas för din medlem. Du använder medlems kontot och medlems kontots lösen ord för hantering av konsortier.
    Namn på konsortium | Ange ett unikt namn för ett nytt konsortium. Om du ansluter till ett konsortium via en inbjudan är värdet det konsortium som du ansluter till.
    Beskrivning | Beskrivning av konsortiet.
    Protokoll |  För hands versionen har stöd för protokollet kvorum.
    Prissättning | Node-konfigurationen för den nya tjänsten. Välj **standard**. 2 validator-noder och 1 Transaction-nod är standardinställningarna.
    Transaction Node-lösenord | Lösen ordet för medlemmens standard transaktions nod. Använd lösen ordet för grundläggande autentisering vid anslutning till blockchain-medlemmens offentliga standard transaktions nod.

1. Välj **skapa** för att etablera tjänsten. Etableringen tar cirka 10 minuter.
1. Välj **meddelanden** i verktygsfältet för att övervaka distributions processen.
1. Efter distributionen navigerar du till din blockchain-medlem.

Välj **Översikt**. du kan visa grundläggande information om tjänsten inklusive RootContract-adress och medlems konto.

![Översikt över blockchain-medlem](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den medlem som du skapade för nästa snabb start eller självstudier. När de inte längre behövs kan du ta bort resurserna genom att ta bort resurs gruppen `myResourceGroup` som du skapade i Azure blockchain-tjänsten.

Ta bort resurs gruppen:

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda MetaMask för att ansluta och distribuera ett smart kontrakt](connect-metamask.md)