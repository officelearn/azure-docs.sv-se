---
title: Skapa en Azure Blockchain-tjänst med Azure portal
description: Använd Azure Blockchain-tjänsten för att skapa medlem consortium.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 51775c5534a13fb2515fafa182658beafd38c1eb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026895"
---
# <a name="quickstart-create-an-azure-blockchain-service-using-the-azure-portal"></a>Snabbstart: Skapa en Azure Blockchain-tjänst med Azure portal

Azure Blockchain-Service är en blockchain-plattform som du kan även köra affärslogiken i ett smarta kontrakt. Den här snabbstarten visar hur du kommer igång genom att skapa en hanterad redovisning med Azure portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-managed-ledger"></a>Skapa en hanterad redovisning

Azure Blockchain Service skapas med en definierad uppsättning beräknings- och lagringsresurser.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **Blockchain** > **Azure Blockchain-tjänsten**.
1. Slutför mallen.

    ![Skapa tjänst](./media/create-member/create-member.png)

    Inställning | Beskrivning
    --------|------------
    Blockchain medlem | Välj ett unikt namn som identifierar Azure Blockchain Service-medlem. Blockchain medlemsnamn kan bara innehålla gemena bokstäver och siffror. Det första tecknet måste vara en bokstav. Värdet måste vara mellan 2 och 20 tecken.
    Prenumeration | Välj den prenumeration som du vill använda för din tjänst. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
    Region | Platsen måste vara samma för alla medlemmar i consortium.
    Kontolösenord för medlem | Ange ett nytt lösenord för kontot medlem. Lösenordet för medlem används för att autentisera till medlemmen blockchain offentlig slutpunkt som använder grundläggande autentisering.
    Consortium namn | Ange ett unikt namn för en ny consortium. Om du vill koppla ett konsortium via en inbjudan är värdet consortium som du ansluter till.
    Beskrivning | Beskrivning av consortium.
    Protokoll |  Förhandsversionen stöder kvorum-protokollet.
    Prissättning | Nodkonfiguration för din nya tjänst. Välj **standard**. 2 verifieraren noder och 1 transaktion noden är standardinställningen.

1. Välj **skapa** att etablera tjänsten. Etableringen tar cirka 10 minuter.
1. Välj **meddelanden** i verktygsfältet för att övervaka distributionsprocessen.
1. Efter distributionen kan du gå till blockchain-medlem.

Välj **översikt**, du kan visa grundläggande information om din tjänst, inklusive kontot RootContract adress och medlemmen.

![Blockchain medlem översikt](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan använda den medlemmen som du skapade för nästa Snabbstart eller självstudie. När den inte längre behövs kan du ta bort resurserna genom att ta bort den `myResourceGroup` resursgruppen du skapade med Azure Blockchain-tjänsten.

Ta bort resursgruppen:

1. I Azure-portalen går du till **resursgrupp** i det vänstra navigeringsfönstret och välj den resursgrupp som du vill ta bort.
2. Välj **Ta bort resursgrupp**. Kontrollera borttagningen genom att ange resursgruppens namn och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd MetaMask att ansluta och distribuera ett smarta kontrakt](connect-metamask.md)