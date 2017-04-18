---
title: 'Azure Active Directory Domain Services: Aktivera Azure Active Directory Domain Services | Microsoft Docs'
description: "Komma igång med Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: e5f1fe51d8931985fa55b2d8c0a3fd25bb93f20f
ms.lasthandoff: 04/12/2017


---
# <a name="enable-azure-active-directory-domain-services"></a>Aktivera Azure Active Directory Domain Services
## <a name="task-3-enable-azure-active-directory-domain-services"></a>Uppgift 3: Aktivera Azure Active Directory Domain Services
I den här uppgiften ska du aktivera Azure Active Directory Domain Services (Azure AD DS) för din katalog genom att göra följande:

1. Gå till den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj knappen **Active Directory** i den vänstra rutan.
3. Välj den innehavare (katalog) för Azure Active Directory (Azure AD) som du vill aktivera Azure AD DS för.

    ![Välja Azure AD-katalog](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. På sidan **Preview directory** (Förhandsgranska katalog) klickar du på fliken **Konfigurera**.

    ![Fliken Konfigurera för katalogen](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Under **Domain services** (Domäntjänster) ändrar du alternativet **Enable domain services for this directory** (Aktivera domäntjänster för den här katalogen) till **Ja**.  
    Ytterligare konfigurationsalternativ för Azure Active Directory Domain Services visas på sidan.

    ![Aktivera Domain Services](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > När du aktiverar Azure Active Directory Domain Services för din klient genererar och lagrar Azure AD de Kerberos- och NTLM-hashvärden som krävs för att autentisera användare.
   >
   >
6. Ange **DNS-domännamnet för domäntjänster**.

   * Standarddomännamnet för katalogen (med suffixet **.onmicrosoft.com**) är valt som standard.

   * Listan innehåller alla domäner som har konfigurerats för din Azure AD-katalog – inklusive verifierade och overifierade domäner som du konfigurerar på fliken **Domäner**.

   * Du kan också ange ett anpassat domännamn. I det här exemplet är det anpassade domännamnet *contoso100.com*.

     > [!WARNING]
     > Prefixet för det angivna domännamnet (till exempel *contoso100* i domännamnet *contoso100.com*) kan innehålla upp till 15 tecken. Du kan inte skapa en Azure Active Directory Domain Services-domän med ett prefix som innehåller fler än 15 tecken.
     >
     >
7. Kontrollera att DNS-domännamnet som du har valt för den hanterade domänen inte redan finns i det virtuella nätverket. Mer specifikt kontrollerar du om:

   * Du redan har en domän med samma DNS-domännamn i det virtuella nätverket.

   * Det virtuella nätverket som du har valt har en VPN-anslutning med det lokala nätverket och om du har en domän med samma DNS-domännamn i det lokala nätverket.

   * Du har en befintlig molntjänst med det namnet i det virtuella nätverket.
8. Välj ett virtuellt nätverk där du vill att Azure Active Directory Domain Services ska vara tillgängligt. Välj det virtuella nätverk och det dedikerade undernät som du skapade i listrutan **Connect domain services to this virtual network** (Anslut domäntjänster till det här virtuella nätverket). Tänk också på följande:

   * Se till att det virtuella nätverk som du har angett tillhör en Azure-region som stöds av Azure Active Directory Domain Services. Gå till [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) om du vill veta i vilka Azure-regioner som Azure Active Directory Domain Services är tillgängligt.

   * Virtuella nätverk som tillhör en region där Azure Active Directory Domain Services inte stöds visas inte i listrutan.

   * Använd ett dedikerat undernät i det virtuella nätverket för Azure Active Directory Domain Services. Välj *inte* gatewayundernätet. Se [Nätverksöverväganden](active-directory-ds-networking.md).

   * Virtuella nätverk som har skapats med Azure Resource Manager visas inte heller i listrutan. Resource Manager-baserade virtuella nätverk stöds för närvarande inte av Azure Active Directory Domain Services.
9. Aktivera Azure Active Directory Domain Services genom att klicka på **Spara** i åtgärdsfönstret längst ned på sidan. 
    * Medan Azure Active Directory Domain Services aktiveras för din katalog visas statusen *Väntande* på sidan.

        ![Fönstret Enable Domain Services (Aktivera domäntjänster)](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Azure Active Directory Domain Services ger hög tillgänglighet för din hanterade domän. När du har aktiverat Azure Active Directory Domain Services ser du att IP-adresserna där domäntjänster är tillgängliga i det virtuella nätverket visas en i taget. Den andra IP-adressen visas strax efter den första, så fort tjänsten aktiverar hög tillgänglighet för din domän. När hög tillgänglighet har konfigurerats och aktiverats för din domän bör du se två IP-adresser i avsnittet **Domäntjänster** på fliken **Konfigurera**.
        >
        >
    * Efter 20–30 minuter visas den första IP-adressen som domäntjänster är tillgängliga för i det virtuella nätverket i fältet **IP-adress** på sidan **Konfigurera**.

        ![Fönstret Domain Services (Domäntjänster) med den första etablerade IP-adressen](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * När hög tillgänglighet är tillgängligt för din domän visas två IP-adresser på sidan. Den hanterade domänen är tillgänglig i ditt valda virtuella nätverk på dessa två IP-adresser. 
    
10. Notera de två IP-adresserna så att du kan uppdatera DNS-inställningarna för det virtuella nätverket. På så sätt kan virtuella datorer i det virtuella nätverket ansluta till domänen för olika åtgärder, till exempel för en domänanslutning.

    ![Fönstret Domain Services (Domäntjänster) med de två etablerade IP-adresserna](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> Beroende på Azure AD-innehavarens storlek (t.ex. antalet användare eller grupper) kan synkroniseringen till din hanterade domän ta en stund. Den här synkroniseringsprocessen sker i bakgrunden. För stora klienter med tiotusentals objekt kan det ta en eller ett par dagar innan alla användare, gruppmedlemskap och autentiseringsuppgifter har synkroniserats.
>
>

## <a name="next-steps"></a>Nästa steg
Uppgift 4: [Uppdatera DNS-inställningarna för det virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md)

