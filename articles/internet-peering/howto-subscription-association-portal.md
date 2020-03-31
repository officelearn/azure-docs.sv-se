---
title: Associera peer-ASN till en Azure-prenumeration med hjälp av portalen
titleSuffix: Azure
description: Associera peer-ASN till en Azure-prenumeration med hjälp av portalen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912153"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associera peer-ASN till en Azure-prenumeration med hjälp av portalen

Innan du skickar en peering-begäran bör du först associera din ASN med Azure-prenumeration med hjälp av stegen nedan.

Om du vill kan du slutföra den här guiden med [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Skapa PeerAsn för att associera din ASN med Azure-prenumeration

### <a name="sign-in-to-the-portal"></a>Logga in på portalen
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registrera dig för peering-resursprovider
Registrera dig för peering-resursleverantör i din prenumeration genom att följa stegen nedan. Om du inte kör detta är Azure-resurser som krävs för att konfigurera peering inte tillgängliga.

1. Klicka på **Prenumerationer** längst upp till vänster på portalen. Om du inte ser det, klicka på **Fler tjänster** och sök efter den.

    > [!div class="mx-imgBorder"]
    > ![Öppna prenumerationer](./media/rp-subscriptions-open.png)

1. Klicka på den prenumeration som du vill använda för peering.

    > [!div class="mx-imgBorder"]
    > ![Starta prenumeration](./media/rp-subscriptions-launch.png)

1. När prenumerationen öppnas, till vänster, klicka på **Resursleverantörer**. Sök sedan *efter peering* i sökfönstret i den högra rutan eller använd rullningslisten för att hitta **Microsoft.Peering** och titta på **status**. Om statusen är ***Registrerad***hoppar du över stegen nedan och fortsätter till avsnittet **Skapa PeerAsn**. Om statusen inte ***är registrerad***väljer du **Microsoft.Peering** och klickar på **Registrera**.

    > [!div class="mx-imgBorder"]
    > ![Registreringen startar](./media/rp-register-start.png)

1. Observera att statusen ***ändras***till Registrering .

    > [!div class="mx-imgBorder"]
    > ![Pågående registrering](./media/rp-register-progress.png)

1. Vänta på en min eller så för att slutföra registreringen. Klicka sedan på **Uppdatera** och kontrollera att statusen är ***registrerad***.

    > [!div class="mx-imgBorder"]
    > ![Registreringen har slutförts](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Skapa PeerAsn
Du kan skapa en ny PeerAsn-resurs för att associera ett autonomt systemnummer (ASN) med Azure-prenumeration. Du kan associera flera ASN till en prenumeration genom att skapa en **PeerAsn** för varje ASN som du behöver associera.

1. Klicka på **Skapa en resurs** > **Visa alla**.

    > [!div class="mx-imgBorder"]
    > ![Sök PeerAsn](./media/peerasn-seeall.png)

1. Sök efter *PeerAsn* i sökrutan och tryck på *Retur* på tangentbordet. Från resultaten klickar du på **PeerAsn-resursen.**

    > [!div class="mx-imgBorder"]
    > ![Starta PeerAsn](./media/peerasn-launch.png)

1. När **PeerAsn** startas klickar du på **Skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skapa PeerAsn](./media/peerasn-create.png)

1. Fyll i fälten som visas nedan **under** fliken Associera ett asn på sidan Associera ett **peer ASN.**

    > [!div class="mx-imgBorder"]
    > ![Fliken PeerAsn-grunderna](./media/peerasn-basics-tab.png)

    * **Namn** motsvarar resursnamn och kan vara vad du vill.  
    * Välj den **prenumeration** som du behöver associera ASN med.
    * **Peer-namn** motsvarar ditt företags namn och måste vara så nära din PeeringDB-profil som möjligt. Observera att värdet endast stöder tecken a-z, A-Ö och blanksteg
    * Ange ditt ASN i fältet **Peer ASN.**
    * Klicka på **Skapa ny** och ange **E-postadress** och **TELEFONNUMMER** för ditt Network Operations Center (NOC)
1. Klicka sedan på **Granska + skapa** och observera att portalen kör grundläggande validering av den information du angav. Detta visas i ett band på toppen, som *Kör slutlig validering...*.

    > [!div class="mx-imgBorder"]
    > ![Fliken PeerAsn-granskning](./media/peerasn-review-tab-validation.png)

1. När meddelandet i menyfliksområdet har valts till *Godkänd validering*verifierar du din information och skickar begäran genom att klicka på **Skapa**. Om valideringen inte går, klicka sedan på **Föregående** och upprepa stegen ovan för att ändra din begäran och se till att de värden du anger inte har några fel.

    > [!div class="mx-imgBorder"]
    > ![Fliken PeerAsn-granskning](./media/peerasn-review-tab.png)

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen misslyckas kontaktar du [Microsoft peering](mailto:peering@microsoft.com). En lyckad distribution visas enligt nedan.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn framgång](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Visa status för en PeerAsn
När PeerAsn-resursen har distribuerats måste du vänta tills Microsoft godkänner associationsbegäran. Det kan ta upp till 12 timmar för godkännande. När du har godkänts får du ett meddelande till den e-postadress som anges i ovanstående avsnitt.

> [!IMPORTANT]
> Vänta tills ValidationState har vänt "Godkänd" innan du skickar en peering-begäran. Det kan ta upp till 12 timmar för detta godkännande.

## <a name="modify-peerasn"></a>Ändra PeerAsn
Det går inte att ändra PeerAsn. Om du behöver ändra kontaktar du [Microsoft peering](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Ta bort PeerAsn
Det går inte att ta bort en PeerAsn. Om du behöver ta bort PeerAsn kontaktar du [Microsoft peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Direct-peering med hjälp av portalen](howto-direct-portal.md)
* [Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen](howto-legacy-direct-portal.md)
* [Skapa eller ändra Exchange-peering med hjälp av portalen](howto-exchange-portal.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns på [vanliga frågor om internet peering](faqs.md)