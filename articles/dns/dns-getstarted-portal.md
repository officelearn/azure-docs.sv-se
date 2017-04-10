---
title: "Komma igång med Azure DNS med hjälp av Azure Portal | Microsoft Docs"
description: "Läs om hur du skapar en DNS-zon och en DNS-post i Azure DNS. Detta är en steg-för-steg-guide om hur du skapar och hanterar din första DNS-zon och DNS-post på Azure Portal."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 79f0c9297c4be70f705f325274f3d9241ea4bc3f
ms.lasthandoff: 03/29/2017

---

# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>Komma igång med Azure DNS med hjälp av Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Den här artikeln visar hur du skapa din första DNS-zon och DNS-post med Azure Portal. Du kan också utföra dessa steg med Azure PowerShell eller plattformsoberoende Azure CLI.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera din DNS-zon på Internet måste du konfigurera namnservrarna för domänen. Dessa steg beskrivs nedan.

## <a name="create-a-dns-zone"></a>Skapa en DNS-zon

1. Logga in på Azure Portal
2. Klicka på **Nytt > Nätverk >** på navmenyn och klicka sedan på **DNS-zon** för att öppna bladet Skapa DNS-zon.

    ![DNS-zon](./media/dns-getstarted-portal/openzone650.png)

4. Namnge DNS-zonen på bladet **Skapa DNS-zon**. Till exempel *contoso.com*.
 
    ![Skapa zon](./media/dns-getstarted-portal/newzone250.png)

5. Ange den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som redan finns. Om du vill skapa en ny resursgrupp använder du listrutan **Plats** för att ange platsen för resursgruppen. Observera att inställningen refererar till platsen för resursgruppen och har ingen inverkan på DNS-zonen. Platsen för DNS-zonen är alltid "global" och visas inte.

6. Du kan låta kryssrutan **Fäst på instrumentpanelen** vara markerad så att du enkelt kan hitta den nya zonen på instrumentpanelen. Klicka sedan på **Skapa**.

    ![Fäst vid instrumentpanelen](./media/dns-getstarted-portal/pindashboard150.png)

7. När du klickar på Skapa ser du din nya zon som konfigureras på instrumentpanelen.

    ![Skapar](./media/dns-getstarted-portal/creating150.png)

8. När den nya zonen har skapats öppnas bladet för den nya zonen på instrumentpanelen.


## <a name="create-a-dns-record"></a>Skapa en DNS-post

Följande exempel visar hur du skapar en ny "A"-post. Information om andra posttyper och hur du ändrar befintliga poster finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska). 


1. Välj **+ Postuppsättning** längst upp på bladet **DNS-zon** för att öppna bladet **Lägg till uppsättning av poster**.

    ![Ny postuppsättning](./media/dns-getstarted-portal/newrecordset500.png)

4. Namnge din postuppsättning på bladet **Lägg till uppsättning av poster**. Du kan till exempel ge postuppsättningen namnet "**www**".

    ![Lägga till en postuppsättning](./media/dns-getstarted-portal/addrecordset500.png)

5. Välj vilken typ av post som du vill skapa. I det här exemplet väljer du **A**.
6. Ange **TTL**. Standard-TTL (Time To Live) är en timme.
7. Lägg till IP-adressen för posten.
8. Klicka på **OK** längst ned på bladet för att skapa DNS-posten.


## <a name="view-records"></a>Visa poster

På den nedre delen av bladet DNS-zon visas posterna för DNS-zonen. Du bör se DNS- och SOA-standardposterna (dessa skapas i varje zon) plus eventuella nya poster som du har skapat.

![zon](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>Uppdatera namnservrar

När du är nöjd med konfigurationen av DNS-zonen och DNS-posterna måste du konfigurera ditt domännamn för användning med Azure DNS-namnservrarna. Det gör att andra användare på Internet kan hitta dina DNS-poster.

Namnservrarna för din zon anges på Azure Portal:

![zon](./media/dns-getstarted-portal/viewzonens500.png)

Dessa namnservrar ska konfigureras med domännamnsregistratorn (där du köpte domännamnet). Registratorn erbjuder möjligheten att konfigurera namnservrar för domänen. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).


## <a name="next-steps"></a>Nästa steg

Läs mer om Azure DNS i [Översikt över Azure DNS](dns-overview.md).

Mer information om hur du hanterar DNS-poster i Azure DNS finns i [Hantera DNS-poster och postuppsättningar med Azure Portal](dns-operations-recordsets-portal.md) (på engelska).


