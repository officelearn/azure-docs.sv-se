<properties
    pageTitle="Azure AD Domain Services: Skapa eller välja ett virtuellt nätverk | Microsoft Azure"
    description="Komma igång med Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>


# Skapa eller välja ett virtuellt nätverk för Azure AD Domain Services

## Riktlinjer för att välja ett virtuellt Azure-nätverk
> [AZURE.NOTE] **Innan du börjar**: Läs [Nätverksrelaterade aspekter att tänka på med Azure AD Domain Services](active-directory-ds-networking.md).


## Uppgift 2: Skapa ett virtuellt Azure-nätverk
Nästa konfigurationsåtgärd är att skapa ett virtuellt Azure-nätverk som du vill aktivera Azure AD Domain Services i. Du kan hoppa över det här steget om du redan har ett befintligt virtuellt nätverk som du vill använda.

> [AZURE.NOTE] Se till att det virtuella Azure-nätverk som du skapar eller väljer att använda med Azure AD Domain Services tillhör en Azure-region som stöds av Azure AD Domain Services. På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.

Skriv ned namnet på det virtuella nätverket så att du väljer rätt virtuellt nätverk när du aktiverar Azure AD Domain Services i ett efterföljande konfigurationssteg.

Utför följande konfigurationssteg för att skapa ett virtuellt Azure-nätverk som du vill aktivera Azure AD Domain Services i.

1. Gå till **den klassiska Azure-portalen** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Välj noden **Nätverk** i det vänstra fönstret.

3. Klicka på **Nytt** i åtgärdsfönstret längst ned på sidan.

    ![Nod för virtuella nätverk](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. Välj **Virtuellt nätverk** i noden **Nätverkstjänster**.

5. Klicka på **Snabbregistrering** för att skapa ett virtuellt nätverk.

    ![Virtuellt nätverk – snabbregistrering](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Ange ett **namn** för det virtuella nätverket. Du kan också välja att konfigurera **adressutrymmet** eller **maximalt antal virtuella datorer** för det här nätverket. Du kan lämna inställningen Inget för DNS-servern tills vidare. Den här inställningen uppdateras när du har aktiverat Azure AD Domain Services.

7. Var noga med att välja en Azure-region som stöds i listrutan **Plats**. På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) ser du i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.

8. Skapa det virtuella nätverket genom att klicka på **Skapa ett virtuellt nätverk**.

    ![Skapa ett virtuellt nätverk för Azure AD Domain Services.](./media/active-directory-domain-services-getting-started/create-vnet.png)

<br>

## Uppgift 3: Aktivera Azure AD Domain Services
Nästa konfigurationsåtgärd är att [aktivera Azure AD Domain Services](active-directory-ds-getting-started-enableaadds.md).



<!--HONumber=Sep16_HO3-->


