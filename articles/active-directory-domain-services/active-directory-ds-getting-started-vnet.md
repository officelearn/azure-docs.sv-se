---
title: 'Azure AD Domain Services: Skapa eller välja ett virtuellt nätverk | Microsoft Docs'
description: Komma igång med Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand

ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/03/2016
ms.author: maheshu

---
# Skapa eller välja ett virtuellt nätverk för Azure AD Domain Services
## Riktlinjer för att välja ett virtuellt Azure-nätverk
> [!NOTE]
> **Innan du börjar**: Läs [Nätverksrelaterade aspekter att tänka på med Azure AD Domain Services](active-directory-ds-networking.md).
> 
> 

## Uppgift 2: Skapa ett virtuellt Azure-nätverk
Nästa konfigurationsåtgärd är att skapa ett virtuellt nätverk för Azure och ett undernät i nätverket. Du aktiverar Azure AD Domain Services i detta undernät inom ditt virtuella nätverk. Du kan hoppa över det här steget om du redan har ett befintligt virtuellt nätverk som du vill använda.

> [!NOTE]
> Se till att det virtuella Azure-nätverk som du skapar eller väljer att använda med Azure AD Domain Services tillhör en Azure-region som stöds av Azure AD Domain Services. På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.
> 
> 

Skriv ned namnet på det virtuella nätverket så att du väljer rätt virtuellt nätverk när du aktiverar Azure AD Domain Services i ett efterföljande konfigurationssteg.

Utför följande konfigurationssteg för att skapa ett virtuellt Azure-nätverk som du vill aktivera Azure AD Domain Services i.

1. Gå till **den klassiska Azure-portalen** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Välj noden **Nätverk** i det vänstra fönstret.
   
    ![Nätverksnod](./media/active-directory-domain-services-getting-started/networks-node.png)
3. Klicka på **Nytt** i åtgärdsfönstret längst ned på sidan.
   
    ![Nod för virtuella nätverk](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Välj **Virtuellt nätverk** i noden **Nätverkstjänster**.
5. Klicka på **Snabbregistrering** för att skapa ett virtuellt nätverk.
   
    ![Virtuellt nätverk – snabbregistrering](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
6. Ange ett **namn** för det virtuella nätverket. Du kan också välja att konfigurera **adressutrymmet** eller **maximalt antal virtuella datorer** för det här nätverket. Du kan lämna inställningen **DNS-server** på ”Ingen” för tillfället. Du kan uppdatera DNS-serverninställningen när du har aktiverat Azure AD Domain Services.
7. Var noga med att välja en Azure-region som stöds i listrutan **Plats**. På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.
8. Skapa det virtuella nätverket genom att klicka på **Skapa ett virtuellt nätverk**.
   
    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. När det virtuella nätverket har skapats väljer du det virtuella nätverket och klickar på fliken **KONFIGURERA**.
   
    ![Skapa ett undernät](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Navigera till avdelningen **adressutrymme för virtuellt nätverk**. Klicka på **lägg till undernät** och ange ett undernät med namnet **AaddsSubnet**. Klicka på **Spara** för att skapa undernätet.
    
    ![Skapa ett undernät för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

<br>

## Uppgift 3: Aktivera Azure AD Domain Services
Nästa konfigurationsåtgärd är att [aktivera Azure AD Domain Services](active-directory-ds-getting-started-enableaadds.md).

<!--HONumber=Oct16_HO1-->


