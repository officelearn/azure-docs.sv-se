---
title: 'VPN Gateway: VPN-klient för OpenVPN-protokoll P2S-anslutningar: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt virtuella nätverk med Azure AD-autentisering
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: alzam
ms.openlocfilehash: 9250464e3d28bdac20840aa9f69cfac707f73b30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371496"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>Konfigurera en VPN-klient för P2S OpenVPN-protokollanslutningar: Azure AD-autentisering

Den här artikeln hjälper dig att konfigurera en VPN-klient för att ansluta till ett virtuellt nätverk med Point-to-Site VPN och Azure Active Directory-autentisering. Innan du kan ansluta och autentisera med Azure AD måste du först konfigurera din Azure AD-klientorganisation. Mer information finns i [Konfigurera en Azure AD-klientorganisation](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Azure AD-autentisering stöds endast för OpenVPN® protokollanslutningar.
>

## <a name="working-with-client-profiles"></a><a name="profile"></a>Arbeta med klientprofiler

För att ansluta måste du hämta Azure VPN-klienten och konfigurera en VPN-klientprofil på varje dator som vill ansluta till det virtuella nätverket. Du kan skapa en klientprofil på en dator, exportera den och sedan importera den till ytterligare datorer.

### <a name="to-download-the-azure-vpn-client"></a>Så här hämtar du Azure VPN-klienten

Använd den här [länken](https://go.microsoft.com/fwlink/?linkid=2117554) för att hämta Azure VPN-klienten. Kontrollera att Azure VPN-klienten har behörighet att köras i bakgrunden. Så här kontrollerar/aktiverar du behörigheten följer du stegen nedan:

1. Gå till Start och välj sedan Inställningar > Sekretess > Bakgrundsappar.
2. Under Bakgrundsappar kontrollerar du att **Låt appar köras i bakgrunden** är aktiverat.
3. Under Välj vilka appar som kan köras i bakgrunden vrider du inställningarna för Azure VPN-klient till **På**.

  ![Tillstånd](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>Så här skapar du en certifikatbaserad klientprofil

När du arbetar med en certifikatbaserad profil kontrollerar du att lämpliga certifikat är installerade på klientdatorn. Mer information om certifikat finns i [Installera klientcertifikat](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![Cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>Så här skapar du en RADIUS-klientprofil

  ![Radie](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> Serverhemligheten kan exporteras i P2S VPN-klientprofilen.  Instruktioner om hur du exporterar en kundprofil finns [här](about-vpn-profile-download.md).
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>Så här exporterar och distribuerar du en klientprofil

När du har en fungerande profil och behöver distribuera den till andra användare kan du exportera den med följande steg:

1. Markera den VPN-klientprofil som du vill exportera, välj **...** och välj sedan **Exportera**.

    ![Exportera](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Välj den plats som du vill spara profilen på, lämna filnamnet som det är och välj sedan **Spara** för att spara xml-filen.

    ![Exportera](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klientprofil

1. Välj **Importera**på sidan .

    ![importera](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Bläddra till profil xml-filen och markera den. När filen är markerad väljer du **Öppna**.

    ![importera](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![importera](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![importera](./media/openvpn-azure-ad-client/import/import4.jpg)

5. När den är ansluten blir ikonen grön och säger **Ansluten**.

    ![importera](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Så här tar du bort en klientprofil

1. Markera ellipserna bredvid den klientprofil som du vill ta bort. Välj sedan **Ta bort**.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Välj **Ta bort** om du vill ta bort.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>Skapa en anslutning

1. På sidan väljer **+** du och sedan **+ Lägg till**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Fyll i anslutningsinformationen. Om du är osäker på värdena kontaktar du administratören. När du har fyllt i värdena väljer du **Spara**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Välj **Anslut** för att ansluta till VPN.

    ![anslutning](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Välj rätt autentiseringsuppgifter och välj sedan **Fortsätt**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create4.jpg)

5. När den har anslutits blir ikonen grön och säger **Ansluten**.

    ![anslutning](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>Så här ansluter du automatiskt

De här stegen hjälper dig att konfigurera anslutningen så att den ansluts automatiskt med Always-on.

1. Välj **VPN-inställningar**på startsidan för din VPN-klient .

    ![Auto](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Välj **Ja** i dialogrutan Växla appar.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Kontrollera att anslutningen som du vill ange inte redan är ansluten, markera sedan profilen och markera kryssrutan **Anslut automatiskt.**

    ![Auto](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. Välj **Anslut** för att initiera VPN-anslutningen.

    ![Auto](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostisera anslutningsproblem

1. Om du vill diagnostisera anslutningsproblem kan du använda **diagnosverktyget.** Välj **den ...** bredvid VPN-anslutningen som du vill diagnostisera för att visa menyn. Välj sedan **Diagnostisera**.

    ![Diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. På sidan **Anslutningsegenskaper** väljer du **Kör diagnos**.

    ![Diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![Diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. Visa diagnosresultaten.

    ![Diagnostisera](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hur lägger jag till DNS-suffix i VPN-klienten?

Du kan ändra den nedladdade profil-XML-filen och lägga till ** \<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes>** taggar

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

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>Hur lägger jag till anpassade DNS-servrar i VPN-klienten?

Du kan ändra den nedladdade profil-XML-filen och lägga till ** \<dnsservers \<>dnsserver> \</dnsserver>\</dnsservers>** taggar

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> OpenVPN Azure AD-klienten använder NRPT-poster (DNS Name Resolution Policy Table), vilket innebär `ipconfig /all`att DNS-servrar inte visas under utdata från . Information om hur du bekräftar DNS-inställningarna i användning finns i [Get-DnsClientNrptPolicy](https://docs.microsoft.com/powershell/module/dnsclient/get-dnsclientnrptpolicy?view=win10-ps) i PowerShell.
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>Hur lägger jag till anpassade vägar till VPN-klienten?

Du kan ändra den nedladdade profil-XML-filen och lägga till ** \< \<>-väg \<>>mål>\<mask \<> \</>/>\</>\</includeroutes>** taggar

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>Hur blockerar jag (utesluter) vägar från VPN-klienten?

Du kan ändra den nedladdade profil-XML-filen och lägga till ** \< \<exkluderingsflödet>\<väg>mål>\<mask> \</>\< \</>\</route>/excluderoutes>** taggar

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Skapa en Azure Active Directory-klient för P2S Open VPN-anslutningar som använder Azure AD-autentisering](openvpn-azure-ad-tenant.md).
