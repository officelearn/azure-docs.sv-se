---
title: 'Konfigurera Azure AD-autentisering för användares VPN-anslutning: virtuellt WAN'
description: Lär dig hur du konfigurerar Azure Active Directory autentisering för användar-VPN.
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 2028cae4908214db28de2545f02f5f2997eeb8af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077473"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Konfigurera Azure Active Directory autentisering för användar-VPN

Den här artikeln visar hur du konfigurerar Azure AD-autentisering för användar-VPN i virtuella WAN-nätverk för att ansluta till dina resurser i Azure över en OpenVPN VPN-anslutning. Azure Active Directory autentisering är endast tillgängligt för gateways med OpenVPN-protokoll och klienter som kör Windows.

Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en hubb
> * Skapa en P2S-konfiguration
> * Ladda ned en VPN-klient profil
> * Tillämpa P2S-konfigurationen på en hubb
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och tillämpa VPN-klientkonfigurationen
> * Visa virtuellt WAN

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrol lera att inget av under näten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure Portal finns i [snabb](../virtual-network/quick-create-portal.md)starten.

* Det virtuella nätverket har inga virtuella Nätverksgatewayen. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gatewayer. Den här konfigurationen kräver att virtuella nätverk är anslutna i stället till den virtuella WAN Hub-gatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Hubben är ett virtuellt nätverk som skapas och används av virtuellt WAN-nätverk. Det adress intervall som du anger för hubben får inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervall som finns i din lokala nätverks konfiguration, koordinerar du med någon som kan ge den informationen åt dig.

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Gå till den virtuella WAN-sidan. Klicka på **+Skapa en resurs** i portalen. Skriv det **virtuella WAN-nätverket** i sökrutan och välj RETUR.
2. Välj **virtuellt WAN** från resultaten. På den virtuella WAN-sidan klickar du på **skapa** för att öppna sidan Skapa WAN.
3. På sidan **skapa WAN** , på fliken **grundläggande** , fyller du i följande fält:

   ![Virtual WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resurs grupp** – skapa ny eller Använd befintlig.
   * **Resurs grupps plats** – Välj en resurs plats i list rutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** – ange det namn som du vill anropa ditt WAN.
   * **Typ:** Standard. Om du skapar ett grundläggande WAN-nätverk kan du bara skapa en Basic-hubb. Basic-hubbar har endast stöd för VPN för plats-till-plats-anslutning.
4. När du har fyllt i fälten väljer du **Granska + skapa**.
5. När verifieringen har godkänts väljer du **skapa** för att skapa det virtuella WAN-nätverket.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Skapa en tom virtuell hubb

1. Under ditt virtuella WAN-nätverk väljer du hubbar och klickar på **+ ny hubb**.

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Fyll i följande fält på sidan Skapa virtuellt nav.

   **Region** – Välj den region som du vill distribuera den virtuella hubben i.

   **Namn** – ange det namn som du vill använda för att anropa den virtuella hubben.

   **Hubb privat adress utrymme** – hubbens adress intervall i CIDR-format.

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klicka på **Granska + skapa**.
4. Klicka på **skapa**på sidan **valideringen har slutförts** .

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Skapa en ny P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Välj **VPN-konfigurationer för användare**under ditt virtuella WAN-nätverk.

   ![ny konfiguration](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. Klicka på **+ skapa användare VPN-konfiguration**.

   ![ny konfiguration](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Ange informationen och klicka på **skapa**

   ![ny konfiguration](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Redigera hubbtilldelning

1. Gå till bladet **hubbar** under det virtuella WAN-nätverket.
2. Välj den hubb som du vill koppla VPN-serverkonfigurationen till och klicka på ellipsen (...).

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klicka på **Redigera virtuell hubb**.
4. Markera kryss rutan **Inkludera punkt-till-plats-Gateway** och välj den **enhet för gateway-skalning** som du vill använda.

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Ange adresspoolen **från vilken** VPN-klienter ska tilldelas IP-adresser.
6. Klicka på **Bekräfta**.
7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="download-vpn-profile"></a><a name="device"></a>Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. På sidan för ditt virtuella WAN-nätverk klickar du på **användare VPN-konfigurationer**.
2. Klicka på **Hämta VPN-konfiguration för användare**längst upp på sidan.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd profil filen för att konfigurera VPN-klienterna.

## <a name="configure-user-vpn-clients"></a>Konfigurera användares VPN-klienter

För att ansluta måste du ladda ned Azure VPN-klienten och importera VPN-klienttjänsten som hämtades i föregående steg på varje dator som vill ansluta till det virtuella nätverket.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN- &reg; protokoll anslutningar.
>

#### <a name="to-download-the-azure-vpn-client"></a>Ladda ned Azure VPN-klienten

Använd den här [länken](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) för att ladda ned Azure VPN-klienten.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klient profil

1. På sidan väljer du **Importera**.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Bläddra till profil-XML-filen och markera den. När filen är vald väljer du **Öppna**.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. När du har anslutit ikonen blir den grön och säg **ansluten**.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Ta bort en klient profil

1. Välj ellipsen (...) bredvid den klient profil som du vill ta bort. Välj sedan **ta bort**.

    ![ta bort](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Välj **ta bort** för att ta bort.

    ![ta bort](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostisera anslutnings problem

1. För att diagnostisera anslutnings problem kan du använda verktyget **diagnostisera** . Välj ellipsen (...) bredvid den VPN-anslutning som du vill diagnostisera för att Visa menyn. Välj sedan **diagnostisera**.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. På sidan **anslutnings egenskaper** väljer du **Kör diagnostik**.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.


## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
