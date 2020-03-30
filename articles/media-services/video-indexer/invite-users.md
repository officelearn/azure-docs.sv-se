---
title: Bjuda in användare till Video Indexer - Azure
titleSuffix: Azure Media Services
description: Den här artikeln visar hur du bjuder in användare till Video Indexer.
services: media-services
author: ReutAmior
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: juliako
ms.openlocfilehash: e9a4575ce2f353d61654263aea8d968c650a689e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499758"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>Snabbstart: Bjud in användare till Video Indexer

Om du vill samarbeta med dina kollegor kan du bjuda in dem till ditt Video Indexer-konto. 

> [!NOTE]
> Endast kontots administratör kan lägga till eller ta bort användare.

## <a name="invite-new-users"></a>Bjud in nya användare

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen. Kontrollera att du är ansluten till ett administratörskonto.
1. Klicka på knappen **Bjud in andra** på den övre menyn:

   ![Bjud in nya användare](./media/invite-users/invite-users.png)

1. Lägg till e-postadresserna till de personer som du vill lägga till i ditt videoindexerkonto:

    ![Bjud in användare till det här kontot](./media/invite-users/invite-to-account.png)
        
    >[!NOTE]
    > Alla användare du bjuder in kommer att ha läs- och skrivbehörighet till alla videor i ditt konto.
1. De användare du bjöd in kommer att få ett e-postmeddelande med en länk och kommer att kunna komma åt kontot när de klickar på **join video indexerare** länken:

    ![Bekräftelse](./media/invite-users/invite-msg.png)

    Användaren måste klicka på länken för att gå med för att få tillgång till kontot. 

## <a name="removing-existing-users"></a>Ta bort befintliga användare

Om du vill ta bort användare som har åtkomst **X** till ditt konto kan du klicka på X-tecknet bredvid deras namn:

![Ta bort användare](./media/invite-users/remove-users.png)

Användare får ingen avisering när de tas bort. När användarna har tagits bort har de inte behörighet att logga in.

## <a name="next-steps"></a>Nästa steg

Du kan nu använda [webbplatsen Video Indexer](video-indexer-view-edit.md) eller [Video Indexer Developer Portal](video-indexer-use-apis.md) för att se videons insikter.

## <a name="see-also"></a>Se även

- [Översikt över Video Indexer](video-indexer-overview.md)
- [Så registrerar du dig och laddar upp din första video](video-indexer-get-started.md)
- [Börja använda API:er](video-indexer-use-apis.md)
