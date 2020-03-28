---
title: Självstudiekurs - Konfigurera virtuella nätverk för Azure AD Domain Services | Microsoft-dokument
description: I den här självstudien får du lära dig hur du skapar och konfigurerar ett Azure-virtuellt nätverksundernät eller nätverks peering för en Azure Active Directory Domain Services-instans med Azure-portalen.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 26122278ad74fb1d383ca7a900810b6060ee78f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73172643"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-instance"></a>Självstudiekurs: Konfigurera virtuella nätverk för en Azure Active Directory Domain Services-instans

För att tillhandahålla anslutning till användare och program distribueras en Azure Active Directory Domain Services (Azure AD DS) hanterad domän till ett Azure-virtuellt nätverksundernät. Det här virtuella nätverksundernätet bör endast användas för hanterade domänresurser som tillhandahålls av Azure-plattformen. När du skapar egna virtuella datorer och program bör de inte distribueras till samma virtuella nätverksundernät. I stället bör du skapa och distribuera dina program till ett separat virtuellt nätverksundernät eller i ett separat virtuellt nätverk som är peered till Azure AD DS virtuella nätverket.

Den här självstudien visar hur du skapar och konfigurerar ett dedikerat virtuellt nätverksundernät eller hur du peer ett annat nätverk till Azure AD DS-hanterade domänens virtuella nätverk.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Förstå alternativen för virtuell nätverksanslutning för domänanslutna resurser till Azure AD DS
> * Skapa ett IP-adressintervall och ytterligare undernät i det virtuella Azure AD DS-nätverket
> * Konfigurera virtuell nätverks peering till ett nätverk som är skilt från Azure AD DS

Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* Du behöver globala administratörsbehörighet i din Azure AD-klient för att aktivera Azure AD DS. *global administrator*
* Du *Contributor* behöver deltagarbehörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien skapar och konfigurerar du Azure AD DS-instansen med Azure-portalen. Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com).

## <a name="application-workload-connectivity-options"></a>Anslutningsalternativ för programarbetsbelastning

I den föregående självstudien skapades en Azure AD DS-hanterad domän som använde vissa standardkonfigurationsalternativ för det virtuella nätverket. Dessa standardalternativ skapade ett virtuellt Azure-nätverk och virtuellt nätverksundernät. Azure AD DS-domänkontrollanterna som tillhandahåller hanterade domäntjänster är anslutna till det här virtuella nätverksundernätet.

När du skapar och kör virtuella datorer som behöver använda den Azure AD DS-hanterade domänen måste nätverksanslutningen tillhandahållas. Den här nätverksanslutningen kan tillhandahållas på något av följande sätt:

* Skapa ytterligare ett virtuellt nätverksundernät i standarddomänen Azure AD DS-hanterad domän virtuella nätverk. Det här ytterligare undernätet är där du skapar och ansluter dina virtuella datorer.
    * Eftersom de virtuella datorerna ingår i samma virtuella nätverk kan de automatiskt utföra namnmatchning och kommunicera med Azure AD DS-domänkontrollanterna.
* Konfigurera Azure-virtuell nätverks peering från Azure AD DS-hanterade domänens virtuella nätverk till ett eller flera separata virtuella nätverk. Dessa separata virtuella nätverk är där du skapar och ansluter dina virtuella datorer.
    * När du konfigurerar virtuell nätverks peering måste du också konfigurera DNS-inställningar för att använda namnmatchning tillbaka till Azure AD DS-domänkontrollanterna.

Du använder vanligtvis bara något av dessa alternativ för nätverksanslutning. Valet beror ofta på hur du vill hantera separata Azure-resurser. Om du vill hantera Azure AD DS och anslutna virtuella datorer som en grupp resurser kan du skapa ytterligare ett virtuellt nätverksundernät för virtuella datorer. Om du vill separera hanteringen av Azure AD DS och sedan alla anslutna virtuella datorer kan du använda virtuell nätverks peering. Du kan också välja att använda virtuell nätverks peering för att tillhandahålla anslutning till befintliga virtuella datorer i din Azure-miljö som är anslutna till ett befintligt virtuellt nätverk.

I den här självstudien behöver du bara konfigurera ett av dessa alternativ för anslutning till virtuella nätverk.

Mer information om hur du planerar och konfigurerar det virtuella nätverket finns i [nätverksöverväganden för Azure Active Directory Domain Services][nätverksöverväganden].

## <a name="create-a-virtual-network-subnet"></a>Skapa ett virtuellt nätverksundernät

Som standard innehåller det virtuella Azure-nätverk som skapats med azure AD DS-hanterad domän ett enda virtuellt nätverksundernät. Det här virtuella nätverksundernätet bör endast användas av Azure-plattformen för att tillhandahålla hanterade domäntjänster. Skapa ytterligare ett undernät om du vill skapa och använda egna virtuella datorer i det här virtuella Azure-nätverket.

Så här skapar du ett virtuellt nätverksundernät för virtuella datorer och programarbetsbelastningar:

1. I Azure-portalen väljer du resursgruppen för din Azure AD DS-hanterade domän, till exempel *myResourceGroup*. Välj standard virtuellt nätverk i listan över resurser, till exempel *aadds-vnet*.
1. Välj **Adressutrymme**i menyn till vänster i det virtuella nätverksfönstret . Det virtuella nätverket skapas med ett enda adressutrymme *på 10.0.1.0/24*, som används av standardundernätet.

    Lägg till ytterligare ett IP-adressintervall i det virtuella nätverket. Storleken på det här adressintervallet och det faktiska IP-adressintervall som ska användas beror på andra nätverksresurser som redan har distribuerats. IP-adressintervallet bör inte överlappa med befintliga adressintervall i din Azure- eller lokala miljö. Se till att du lägger upp IP-adressintervallet tillräckligt stort för det antal virtuella datorer som du förväntar dig att distribuera till undernätet.

    I följande exempel läggs ytterligare ett IP-adressintervall *på 10.0.2.0/24* till. När du är klar väljer du **Spara**.

    ![Lägga till ytterligare ett IP-adressintervall för virtuellt nätverk i Azure-portalen](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Välj sedan **Undernät**i menyn till vänster i det virtuella nätverksfönstret och välj sedan **+ Undernät** för att lägga till ett undernät.
1. Ange ett namn för undernätet, till exempel *arbetsbelastningar*. Om det behövs uppdaterar du **adressintervallet** om du vill använda en delmängd av IP-adressintervallet som konfigurerats för det virtuella nätverket i föregående steg. För tillfället, lämna standardvärden för alternativ som nätverkssäkerhet grupp, rutttabell, serviceslutpunkter.

    I följande exempel skapas ett undernät med namnet *arbetsbelastningar* som använder IP-adressintervallet *10.0.2.0/24:*

    ![Lägga till ytterligare ett virtuellt nätverksundernät i Azure-portalen](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. När du är klar väljer du **OK**. Det tar en stund att skapa det virtuella nätverksundernätet.

När du skapar en virtuell dator som måste använda azure AD DS-hanterad domän, se till att du väljer det här virtuella nätverksundernätet. Skapa inte virtuella datorer i standardnätet *aadds-undernät*. Om du väljer ett annat virtuellt nätverk finns det ingen nätverksanslutning och DNS-lösning för att nå den Azure AD DS-hanterade domänen om du inte konfigurerar virtuell nätverks peering.

## <a name="configure-virtual-network-peering"></a>Konfigurera virtuell nätverks peering

Du kan ha ett befintligt virtuellt Azure-nätverk för virtuella datorer eller vill hålla ditt virtuella Azure AD DS-hanterade domännätverk separat. För att kunna använda den hanterade domänen behöver virtuella datorer i andra virtuella nätverk ett sätt att kommunicera med Azure AD DS-domänkontrollanterna. Den här anslutningen kan tillhandahållas med azure-peering för virtuella nätverk.

Med Azure virtual network peering är två virtuella nätverk sammankopplade, utan behov av en VPN-enhet (Virtual Private Network). Med nätverks peering kan du snabbt ansluta virtuella nätverk och definiera trafikflöden i din Azure-miljö. Mer information om peering finns i [Översikt över virtuell azure-nätverks peering][peering-overview].

Så här peer ett virtuellt nätverk till azure AD DS-hanterade domänens virtuella nätverk gör du följande:

1. Välj det virtuella standardnätverk som skapats för din Azure AD DS-instans med namnet *aadds-vnet*.
1. Välj **Peerings**i menyn till vänster i det virtuella nätverksfönstret .
1. Om du vill skapa en peering väljer du **+ Lägg till**. I följande exempel är *standardaads-vnet* peered till ett virtuellt nätverk med namnet *myVnet*. Konfigurera följande inställningar med dina egna värden:

    * **Namn på peering från aadds-vnet till fjärrvirviska nätverk:** En beskrivande identifierare för de två nätverken, till exempel *aadds-vnet-to-myvnet*
    * **Distributionstyp för virtuellt nätverk**: *Resurshanteraren*
    * **Prenumeration**: Prenumerationen för det virtuella nätverk som du vill peer-till, till exempel *Azure*
    * **Virtuellt nätverk:** Det virtuella nätverk som du vill peer to, till exempel *myVnet*
    * **Namnet på peering från myVnet till aadds-vnet**: En beskrivande identifierare för de två nätverken, till exempel *myvnet-to-aadds-vnet*

    ![Konfigurera virtuell nätverks peering i Azure-portalen](./media/tutorial-configure-networking/create-peering.png)

    Lämna alla andra standardvärden för virtuell nätverksåtkomst eller vidarebefordrad trafik om du inte har specifika krav för din miljö och välj sedan **OK**.

1. Det tar en stund att skapa peering på både Azure AD DS virtuella nätverk och det virtuella nätverket du valt. När du är klar rapporterar **peering-status** *ansluten*, som visas i följande exempel:

    ![Anslutna peer-nätverk i Azure-portalen](./media/tutorial-configure-networking/connected-peering.png)

Innan virtuella datorer i det peer-hanterade virtuella nätverket kan använda den hanterade Azure AD DS-domänen konfigurerar du DNS-servrarna så att de tillåter korrekt namnmatchning.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Konfigurera DNS-servrar i det peer-inservade virtuella nätverket

För att virtuella datorer och program i det peer-anpassade virtuella nätverket ska kunna prata med den Azure AD DS-hanterade domänen måste DNS-inställningarna uppdateras. IP-adresserna för Azure AD DS-domänkontrollanterna måste konfigureras som DNS-servrar i det peer-in-virtuella nätverket. Det finns två sätt att konfigurera domänkontrollanterna som DNS-servrar för det peer-inkopplade virtuella nätverket:

* Konfigurera DNS-servrarna för virtuellt nätverk i Azure så att de använder Azure AD DS-domänkontrollanterna.
* Konfigurera den befintliga DNS-servern som används i det peer-kopplade virtuella nätverket så att villkorlig DNS-vidarebefordran används för att dirigera frågor till den Hanterade Azure AD DS-domänen. Dessa steg varierar beroende på vilken DNS-server som används.

I den här självstudien ska vi konfigurera AZURE-DNS-servrar för virtuella nätverk så att alla frågor dirigeras till Azure AD DS-domänkontrollanterna.

1. I Azure-portalen väljer du resursgruppen för det peer-inkopplade virtuella nätverket, till exempel *myResourceGroup*. Välj det peer-inkopplade virtuella nätverket, till exempel *myVnet.*
1. Välj **DNS-servrar**i menyn till vänster i det virtuella nätverksfönstret .
1. Som standard använder ett virtuellt nätverk de inbyggda DNS-servrarna som tillhandahålls av Azure. Välj att använda **anpassade** DNS-servrar. Ange IP-adresserna för Azure AD DS-domänkontrollanterna, som vanligtvis är *10.0.1.4* och *10.0.1.5*. Bekräfta dessa IP-adresser i **översiktsfönstret för** din Azure AD DS-hanterade domän i portalen.

    ![Konfigurera DNS-servrar för virtuellt nätverk så att Azure AD DS-domänkontrollanter används](./media/tutorial-configure-networking/custom-dns.png)

1. När du är klar väljer du **Spara**. Det tar en stund att uppdatera DNS-servrarna för det virtuella nätverket.
1. Om du vill tillämpa de uppdaterade DNS-inställningarna på de virtuella datorerna startar du om virtuella datorer som är anslutna till det peer-anslutna virtuella nätverket.

När du skapar en virtuell dator som måste använda azure AD DS-hanterad domän, se till att du väljer det här peer-ed virtuella nätverket. Om du väljer ett annat virtuellt nätverk finns det ingen nätverksanslutning och DNS-lösning för att nå den Azure AD DS-hanterade domänen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Förstå alternativen för virtuell nätverksanslutning för domänanslutna resurser till Azure AD DS
> * Skapa ett IP-adressintervall och ytterligare undernät i det virtuella Azure AD DS-nätverket
> * Konfigurera virtuell nätverks peering till ett nätverk som är skilt från Azure AD DS

Skapa och ansluta en virtuell dator till domänen om du vill se den här hanterade domänen i praktiken.

> [!div class="nextstepaction"]
> [Ansluta till en virtuell Windows Server-dator med din hanterade domän](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
