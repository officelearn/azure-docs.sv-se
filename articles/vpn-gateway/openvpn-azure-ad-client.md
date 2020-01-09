---
title: 'VPN Gateway: VPN-klienten för OpenVPN-protokoll P2S anslutningar: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt VNet med Azure AD-autentisering
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: alzam
ms.openlocfilehash: 59af4189b52c2ad7a1109ffb03accedbc69dc6c6
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647925"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication-preview"></a>Konfigurera en VPN-klient för P2S OpenVPN-protokoll anslutningar: Azure AD-autentisering (för hands version)

Den här artikeln hjälper dig att konfigurera en VPN-klient för att ansluta till ett virtuellt nätverk med hjälp av punkt-till-plats-VPN och Azure Active Directory autentisering. Innan du kan ansluta och autentisera med Azure AD måste du först konfigurera Azure AD-klienten. Mer information finns i [Konfigurera en Azure AD-klient](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN-® protokoll anslutningar.
>

## <a name="profile"></a>Arbeta med klient profiler

För att ansluta måste du ladda ned Azure VPN-klienten (för hands version) och konfigurera en VPN-klient profil på varje dator som vill ansluta till det virtuella nätverket. Du kan skapa en klient profil på en dator, exportera den och sedan importera den till ytterligare datorer.

### <a name="to-download-the-azure-vpn-client"></a>Ladda ned Azure VPN-klienten

Använd den här [länken](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) för att ladda ned Azure VPN-klienten (för hands version).

### <a name="cert"></a>Så här skapar du en certifikatbaserad klient profil

När du arbetar med en certifikatbaserad profil bör du kontrol lera att rätt certifikat är installerade på klient datorn. Mer information om certifikat finns i [Installera klient certifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![certifikatet](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>Så här skapar du en RADIUS-klient profil

  ![RADIUS](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Server hemligheten kan exporteras i P2S VPN-klient profil.  Anvisningar om hur du exporterar en klient profil finns [här](about-vpn-profile-download.md).
>

### <a name="export"></a>Så här exporterar och distribuerar du en klient profil

När du har en fungerande profil och behöver distribuera den till andra användare kan du exportera den med hjälp av följande steg:

1. Markera den VPN-klientkonfiguration som du vill exportera, Välj **...** och välj sedan **Exportera**.

    ![exportera](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Välj den plats där du vill spara profilen, lämna fil namnet som det är och välj sedan **Spara** för att spara XML-filen.

    ![exportera](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Så här importerar du en klient profil

1. På sidan väljer du **Importera**.

    ![importera](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Bläddra till profil-XML-filen och markera den. När filen är vald väljer du **Öppna**.

    ![importera](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![importera](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![importera](./media/openvpn-azure-ad-client/import/import4.jpg)

5. När du har anslutit ikonen blir den grön och säg **ansluten**.

    ![importera](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Ta bort en klient profil

1. Välj ellipserna bredvid den klient profil som du vill ta bort. Välj sedan **ta bort**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Välj **ta bort** för att ta bort.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Skapa en anslutning

1. På sidan väljer du **+** och sedan **+ Lägg till**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Fyll i anslutnings informationen. Kontakta administratören om du är osäker på värdena. När du har fyllt i värdena väljer du **Spara**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Välj **Anslut** för att ansluta till VPN.

    ![anslutning](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Välj rätt autentiseringsuppgifter och välj sedan **Fortsätt**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create4.jpg)

5. När anslutningen är klar blir ikonen grön och säg **ansluten**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Så här ansluter du automatiskt

Dessa steg hjälper dig att konfigurera anslutningen till att ansluta automatiskt med Always On.

1. På Start sidan för VPN-klienten väljer du **VPN-inställningar**.

    ![auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Välj **Ja** i dialog rutan Växla appar.

    ![auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Kontrol lera att den anslutning som du vill ange inte redan är ansluten, markera sedan profilen och markera kryss rutan **Anslut automatiskt** .

    ![auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Välj **Anslut** för att initiera VPN-anslutningen.

    ![auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Diagnostisera anslutnings problem

1. För att diagnostisera anslutnings problem kan du använda verktyget **diagnostisera** . Välj **...** bredvid den VPN-anslutning som du vill diagnostisera för att Visa menyn. Välj sedan **diagnostisera**.

    ![diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. På sidan **anslutnings egenskaper** väljer du **Kör diagnostik**.

    ![diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>FAQ

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hur gör jag för att lägga till DNS-suffix till VPN-klienten?

Du kan ändra den hämtade profil-XML-filen och lägga till **\<dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes >** Taggar

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [skapa en Azure Active Directory-klient för P2s öppna VPN-anslutningar som använder Azure AD-autentisering](openvpn-azure-ad-tenant.md).
