<properties
    pageTitle="Azure AD DS: Uppdatera DNS-inställningarna för det virtuella Azure-nätverket | Microsoft Azure"
    description="Komma igång med Azure Active Directory Domain Services (förhandsversion)"
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
    ms.date="04/25/2016"
    ms.author="maheshu"/>

# Azure AD Domain Services *(förhandsversion)* – Uppdatera DNS-inställningarna för det virtuella Azure-nätverket

## Uppgift 4 – Uppdatera DNS-inställningarna för det virtuella Azure-nätverket
Nu när du har aktiverat Azure AD Domain Services för din katalog är nästa uppgift att säkerställa att datorerna i det virtuella nätverket kan ansluta och använda dessa tjänster. För att kunna göra detta måste du uppdatera DNS-serverinställningarna för det virtuella nätverket så att de pekar på IP-adresserna där Azure AD Domain Services är tillgängligt i det virtuella nätverket.

> [AZURE.NOTE] Skriv ner IP-adresserna för Azure AD Domain Services som visas på fliken **Konfigurera** för din katalog när du har aktiverat Azure AD Domain Services för katalogen.

Utför följande konfigurationssteg för att uppdatera DNS-serverinställningen för det virtuella nätverket som Azure AD Domain Services är aktiverat i.

1. Gå till **den klassiska Azure-portalen** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Välj noden **Nätverk** i det vänstra fönstret.

    ![Nod för virtuella nätverk](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. På fliken **Virtuella nätverk** väljer du det virtuella nätverket som du har aktiverat Azure AD Domain Services i för att visa dess egenskaper.

4. Klicka på fliken **Konfigurera**.

    ![Nod för virtuella nätverk](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Ange IP-adresserna för Azure AD Domain Services i avsnittet **DNS-servrar**.

6. Se till att du anger båda IP-adresserna som visades i avsnittet **Domäntjänster** på fliken **Konfigurera** för katalogen.

7. Klicka på **Spara** i åtgärdsfönstret längst ned på sidan för att spara DNS-serverinställningarna för det här virtuella nätverket.

   ![Uppdatera DNS-serverinställningarna för det virtuella nätverket.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] När du har uppdaterat DNS-serverinställningarna för det virtuella nätverket kan det ta en stund innan de virtuella datorerna i nätverket får den uppdaterade DNS-konfigurationen. Om en virtuell dator inte kan ansluta till domänen kan du tömma DNS-cacheminnet (t.ex. ”ipconfig/flushdns”) på den virtuella datorn för att framtvinga en uppdatering av DNS-inställningarna på den virtuella datorn.


## Uppgift 5 – Aktivera lösenordssynkronisering till Azure AD Domain Services
Nästa konfigurationsåtgärd är att [aktivera lösenordssynkronisering till Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).



<!--HONumber=Jun16_HO2-->


