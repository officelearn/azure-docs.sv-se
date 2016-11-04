---
title: 'Azure AD Domain Services: Aktivera Azure AD Domain Services | Microsoft Docs'
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
ms.date: 09/21/2016
ms.author: maheshu

---
# Aktivera Azure AD Domain Services
## Uppgift 3: Aktivera Azure AD Domain Services
I det här steget aktiverar du Azure AD Domain Services för din katalog. Aktivera Azure AD Domain Services för din katalog genom att utföra följande konfigurationssteg.

1. Gå till **den klassiska Azure-portalen** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Välj noden **Active Directory** i det vänstra fönstret.
3. Välj Azure AD-klienten (katalogen) som du vill aktivera Azure AD Domain Services för.
   
    ![Välja Azure AD-katalog](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Klicka på fliken **Konfigurera**.
   
    ![Fliken Konfigurera för katalogen](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. Rulla ned till avsnittet **Domäntjänster**.
   
    ![Konfigurationsavsnittet Domäntjänster](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)
6. Ändra alternativet **Aktivera domäntjänster för den här katalogen** till **Ja**. Några fler konfigurationsalternativ för Azure AD Domain Services visas på sidan.
   
    ![Aktivera Domain Services](./media/active-directory-domain-services-getting-started/enable-domain-services.png)
   
   > [!NOTE]
   > När du aktiverar Azure AD Domain Services för din klient genererar och lagrar Azure AD de Kerberos- och NTLM-hashvärden som krävs för att autentisera användare.
   > 
   > 
7. Ange **DNS-domännamnet för domäntjänster**.
   
   * Standarddomännamnet för katalogen (som slutar med domänsuffixet **.onmicrosoft.com**) väljs som standard.
   * Listan innehåller alla domäner som har konfigurerats för din Azure AD-katalog – inklusive verifierade och overifierade domäner som du konfigurerar på fliken Domäner.
   * Du kan också lägga till ett anpassat domännamn i den här listan genom att skriva det. I det här exemplet har vi angett ett anpassat domännamn: ”contoso100.com”
     
     > [!WARNING]
     > Kontrollera att domänprefixet för det domännamn som du anger (t.ex. ”contoso100” i domännamnet ”contoso100.com”) är högst 15 tecken långt. Du kan inte skapa en Azure AD Domain Services-domän med ett domänprefix som är längre än 15 tecken.
     > 
     > 
8. Nästa steg är att välja ett virtuellt nätverk där du vill att Azure AD Domain Services ska vara tillgängligt. Välj det virtuella nätverk som du skapade i listrutan **Anslut domäntjänster till det här virtuella nätverket**.
   
   * Se till att det virtuella nätverket som du har angett hör till en Azure-region som stöds av Azure AD Domain Services.
   * På sidan [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services/) kan du se i vilka Azure-regioner som Azure AD Domain Services är tillgängligt.
   * Virtuella nätverk som hör till en region där Azure AD Domain Services inte stöds visas inte i listrutan.
   * Virtuella nätverk som har skapats med Azure Resource Manager visas inte heller i listrutan. Resource Manager-baserade virtuella nätverk stöds för närvarande inte av Azure AD Domain Services.
9. Kontrollera att DNS-domännamnet som du har valt för den hanterade domänen inte redan finns i det virtuella nätverket. Kontrollera särskilt om:
   
   * du redan har en domän med samma DNS-domännamn i det virtuella nätverket
   * det virtuella nätverket som du har valt har en VPN-anslutning med det lokala nätverket och du har en domän med samma DNS-domännamn i det lokala nätverket
   * du har en befintlig molntjänst med det namnet i det virtuella nätverket.
10. Klicka på **Spara** i åtgärdsfönstret längst ned på sidan för att aktivera Azure AD Domain Services.
11. Sidan visar statusen ”Väntar...” medan Azure AD Domain Services aktiveras för katalogen.
    
    ![Aktivera Domain Services – statusen Väntar](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)
    
    > [!NOTE]
    > Azure AD Domain Services ger hög tillgänglighet för din hanterade domän. När du aktiverar Azure AD Domain Services visas de IP-adresser där Domain Services är tillgängligt i det virtuella nätverket en efter en. Den andra IP-adressen visas snart, så fort tjänsten aktiverar hög tillgänglighet för din domän. När hög tillgänglighet har konfigurerats och aktiverats för din domän bör du se två IP-adresser i avsnittet **Domäntjänster** på fliken **Konfigurera**.
    > 
    > 
12. Efter 20–30 minuter visas den första IP-adressen som Domain Services är tillgängligt för i det virtuella nätverket i fältet **IP-adress** på sidan **Konfigurera**.
    
    ![Domain Services aktiverat – den första IP-adressen har etablerats](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
13. När hög tillgänglighet är tillgängligt i din domän visas två IP-adresser på sidan. Det här är de IP-adresser som Azure AD Domain Services är tillgängligt på i ditt valda virtuella nätverk. Anteckna IP-adresserna så att du kan uppdatera DNS-inställningarna för ditt virtuella nätverk. Det här steget gör att virtuella datorer i det virtuella nätverket kan ansluta till domänen för olika åtgärder, till exempel för en domänanslutning.
    
    ![Domain Services aktiverat – båda IP-adresserna har etablerats](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> Beroende på storleken på din Azure AD-klient (antal användare, grupper m.m.) kan det ta en stund innan innehållet i klienten är tillgängligt i Azure AD Domain Services. Den här synkroniseringsprocessen sker i bakgrunden. För stora klienter med tiotusentals objekt kan det ta en eller ett par dagar innan alla användare, gruppmedlemskap och autentiseringsuppgifter finns tillgängliga i Azure AD Domain Services.
> 
> 

<br>

## Uppgift 4 – Uppdatera DNS-inställningar för det virtuella Azure-nätverket
Nästa konfigurationsåtgärd är att [uppdatera DNS-inställningarna för det virtuella Azure-nätverket](active-directory-ds-getting-started-dns.md).

<!--HONumber=Sep16_HO4-->


