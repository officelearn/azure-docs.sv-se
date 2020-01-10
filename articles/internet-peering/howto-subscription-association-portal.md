---
title: Koppla peer-ASN till Azure-prenumerationen med hjälp av portalen
titleSuffix: Azure
description: Koppla peer-ASN till Azure-prenumerationen med hjälp av portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d3737be5a3186774f230aef9d932464a27a764f4
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775646"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Koppla peer-ASN till Azure-prenumerationen med hjälp av portalen

Innan du skickar in en peering-begäran bör du först associera ditt ASN med Azure-prenumerationen genom att följa stegen nedan.

Om du vill kan du slutföra den här guiden med hjälp av [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Skapa PeerAsn för att koppla ditt ASN med Azure-prenumeration

### <a name="sign-in-to-the-portal"></a>Logga in på portalen
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-peerasn"></a>Skapa PeerAsn
Du kan skapa en ny PeerAsn-resurs för att associera ett autonomt system nummer (ASN) med Azure-prenumerationen. Du kan koppla flera ASN: er till en prenumeration genom att skapa en **PeerAsn** för varje ASN som du behöver associera.

1. Klicka på **skapa en resurs** > **Se alla**.

    > [!div class="mx-imgBorder"]
    > ![Sök PeerAsn](./media/peerasn-seeall.png)

1. Sök efter *PeerAsn* i sökrutan och tryck på *RETUR* på tangent bordet. Klicka på **PeerAsn** -resurs i resultatet.

    > [!div class="mx-imgBorder"]
    > ![starta PeerAsn](./media/peerasn-launch.png)

1. När **PeerAsn** har startats klickar du på **skapa**.

    > [!div class="mx-imgBorder"]
    > ![skapa PeerAsn](./media/peerasn-create.png)

1. På fliken **associera en peer-ASN** , under fliken **grundläggande** , fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![fliken grundläggande för PeerAsn](./media/peerasn-basics-tab.png)

    * **Namnet** motsvarar resurs namnet och kan vara allt du väljer.  
    * Välj den **prenumeration** som du behöver för att associera ASN med.
    * **Peer-namnet** motsvarar företagets namn och måste vara så nära som möjligt för din PeeringDB-profil. Observera att värdet endast stöder tecknen a – z, A-Z och blank steg
    * Ange ditt ASN i fältet **peer-ASN** .
    * Klicka på **Skapa ny** och ange **e-postadress** och **telefonnummer** för din nätverks åtgärds Center (NOC)
1. Klicka sedan på **Granska + skapa** och Observera att portalen kör grundläggande verifiering av den information som du har angett. Detta visas i ett menyfliksområde överst, som *körning av slutgiltig verifiering...* .

    > [!div class="mx-imgBorder"]
    > ![fliken Granska](./media/peerasn-review-tab-validation.png) PeerAsn

1. När meddelandet i menyfliksområdet är klar med *verifieringen*verifierar du din information och skickar in begäran genom att klicka på **skapa**. Om verifieringen inte godkänns klickar du på **föregående** och upprepar stegen ovan för att ändra din begäran och se till att de värden du anger inte innehåller några fel.

    > [!div class="mx-imgBorder"]
    > ![fliken Granska](./media/peerasn-review-tab.png) PeerAsn

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas som nedan.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn lyckades](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Visa status för en PeerAsn
När PeerAsn-resursen har distribuerats måste du vänta på att Microsoft ska godkänna associerings förfrågan. Det kan ta upp till 12 timmar för godkännande. När den har godkänts får du ett meddelande till den e-postadress som anges i avsnittet ovan.

> [!IMPORTANT]
> Vänta tills ValidationState för att aktivera "godkänd" innan du skickar en peering-begäran. Det kan ta upp till 12 timmar innan detta godkännande.

## <a name="modify-peerasn"></a>Ändra PeerAsn
Det finns för närvarande inte stöd för att ändra PeerAsn. Kontakta [Microsoft-peering](mailto:peering@microsoft.com)om du behöver ändra.

## <a name="delete-peerasn"></a>Ta bort PeerAsn
Det finns för närvarande inte stöd för att ta bort en PeerAsn. Om du behöver ta bort PeerAsn kontaktar du [Microsoft-peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en direkt peering med hjälp av portalen](howto-direct-portal.md)
* [Konvertera en äldre direkt peering till Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)
* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till Azure-resursen med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md)