---
title: "Azure Active Directory Domain Services: Skapa eller välja ett virtuellt nätverk | Microsoft Docs"
description: "Aktivera Azure Active Directory Domain Services med hjälp av den klassiska Azure-portalen"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: c0d3f90c0f15fbb7aad00fe24c2946738e24ffd8
ms.contentlocale: sv-se
ms.lasthandoff: 08/29/2017

---
# <a name="create-or-select-a-virtual-network-for-azure-active-directory-domain-services"></a>Skapa eller välja ett virtuellt nätverk för Azure Active Directory Domain Services

> [!IMPORTANT]
> Den klassiska Azure-portalen för att aktivera Azure AD Domain Services som visas i den här artikeln tas snart bort. För nya distributioner [  **ska du i stället använda den nya (förhandsversionen) Azure-portalen**](active-directory-ds-getting-started.md).
>

## <a name="before-you-begin"></a>Innan du börjar
Se [Nätverksrelaterade aspekter att tänka på med Azure Active Directory Domain Services](active-directory-ds-networking.md).

## <a name="task-2-create-an-azure-virtual-network"></a>Uppgift 2: Skapa ett virtuellt Azure-nätverk
Nästa konfigurationsåtgärd är att skapa ett virtuellt nätverk för Azure och ett undernät i nätverket. Du aktiverar Azure Active Directory Domain Services i detta undernät inom ditt virtuella nätverk. Du kan hoppa över det här steget om du har ett befintligt virtuellt nätverk som du vill använda.

> [!NOTE]
> Se till att det virtuella Azure-nätverk som du skapar eller väljer att använda med Azure Active Directory Domain Services tillhör en Azure-region som stöds av Azure Active Directory Domain Services. Se [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) för att se i vilka Azure-regioner som Azure Active Directory Domain Services är tillgängligt.
>
>Skriv ned namnet på det virtuella nätverket för att säkerställa att du väljer rätt virtuellt nätverk när du aktiverar Azure Active Directory Domain Services i ett efterföljande konfigurationssteg.


Om du vill skapa ett virtuellt Azure-nätverk i vilket du vill aktivera Azure Active Directory Domain Services, följer du nedanstående konfigurationsinstruktioner:

1. Gå till den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj **Nätverk** i den vänstra rutan.

    ![Nätverksnod](./media/active-directory-domain-services-getting-started/networks-node.png)  
    Fönstret **Virtuella nätverk** öppnas.
3. Klicka på **Nytt** i åtgärdsrutan längst ned i fönstret.

    ![Fönstret Virtuellt nätverk](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Klicka på **Nätverkstjänster** och välj sedan **Virtuellt nätverk**.

    ![Virtuellt nätverk – snabbregistrering](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. Klicka på **Snabbregistrering** för att skapa ett virtuellt nätverk.

6. Ange ett **Namn** för ditt virtuella nätverk och överväg att göra något av följande:
    * Du kan välja att konfigurera **Adressutrymme** eller **Maximalt antal virtuella datorer** för det här nätverket.
    * Du kan lämna inställningen för **DNS-server** som **Ingen** för tillfället. Du kan uppdatera inställningen när du har aktiverat Azure Active Directory Domain Services.
7. Välj en Azure-region i den nedrullningsbara listan **Plats**.  
    Se [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) för att se i vilka Azure-regioner som Azure Active Directory Domain Services är tillgängligt.
8. Klicka på **Skapa ett virtuellt nätverk** för att skapa ditt virtuella nätverk.

    ![Skapa ett virtuellt nätverk för Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. När det virtuella nätverket har skapats väljer du namn för det och klickar på fliken **Konfigurera**.

    ![Skapa ett undernät](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Under **adressutrymmen för virtuella nätverk** klickar du på **lägg till undernät** och anger sedan ett undernät med namnet **AaddsSubnet**.

    ![Skapa ett undernät för Azure Active Directory Domain Services](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. Klicka på **Spara** för att skapa undernätet.


## <a name="next-step"></a>Nästa steg
[Uppgift 3: Aktivera Azure Active Directory Domain Services](active-directory-ds-getting-started-enableaadds.md)

