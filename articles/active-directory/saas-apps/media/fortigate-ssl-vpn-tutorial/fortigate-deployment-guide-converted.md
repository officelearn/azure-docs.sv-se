---
title: Distributions guide för FortiGate | Microsoft Docs
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2020
ms.locfileid: "88658482"
---
# <a name="fortigate-deployment-guide"></a>Distributions guide för FortiGate

## <a name="contents"></a>Innehåll

- Lösa in FortiGate-licensen
- Ladda ned inbyggd program vara
- Distribuera den virtuella FortiGate-datorn
   - Ange en offentlig IP-Statuc och tilldela ett fullständigt kvalificerat domän namn
   - Skapa en ny regel för inkommande nätverks säkerhets grupp för TCP-port
- Skapa en anpassad Azure App för FortiGate
- Förbered för grupp matchning
   - Skapa grupper för användare
- Konfigurera den virtuella FortiGate-datorn
   - Installera licensen
   - Uppdatera den inbyggda programvaran
   - Ändra hanterings porten till TCP
   - Ladda upp Azure Active Directory SAML-signeringscertifikat
   - Ladda upp och konfigurera ett anpassat SSL-certifikat
   - Utför kommando rads konfiguration
   - Skapa VPN-portaler och brand Väggs princip
- Testa inloggning med Azure

## <a name="redeeming-the-fortigate-license"></a>Lösa in FortiGate-licensen

Fortinet FortiGate nästa generations brand Väggs produkt är tillgänglig som en virtuell dator i Azure IaaS. Det finns två licensierings lägen för den här virtuella datorn –

- Betala per användning (PAYG)
- Bring your own license (BYOL)

När du samarbetar med Fortinet för att tillhandahålla säker hybrid åtkomst (SHA) kan Fortinet ge medlemmar i Azure AD till gång till ett SHA-team med licenser. I de fall där ingen licens har angetts kommer PAYG-distributionen också att fungera.

I de fall där en licens har utfärdats tillhandahåller Fortinet en registrerings kod som måste lösas in online igen

![Fortigate SSL VPN](registration-code.png)

1. Registrera dig på https://support.fortinet.com/
2. Efter registreringen loggar du in på https://support.fortinet.com/
3. Navigera till **till gång** – > **Registrera/aktivera**
4. Ange registrerings koden som tillhandahålls av Fortinet
5. Ange registrerings koden, Välj **produkten kommer att användas av en icke-myndighets användare** och klicka på **Nästa**
6. Ange en produktbeskrivning (t. ex. FortiGate), ange Fortinet partner som **annan** – > **Microsoft** och klicka på **Nästa**
7. Godkänn **Fortinet produkt registrerings avtal** och klicka på **Nästa**
8. Godkänn **villkoren** och klicka på **Bekräfta**
9. Klicka på **licens filen Ladda ned** och spara licensen för senare


## <a name="download-firmware"></a>Ladda ned inbyggd program vara

Vid tidpunkten för skrivning levereras inte Fortinet FortiGate Azure VM med den inbyggda program vara som krävs för SAML-autentisering. Den senaste versionen måste hämtas från Fortinet.

1. Logga in på https://support.fortinet.com/
2. Navigera för att **Ladda ned** > **avbildningar av inbyggd program vara**
3. Klicka på **Hämta** till höger om **versions information**
4. Klicka på **v6.**
5. Klicka på **6.**
6. Klicka på **6,4.**
7. Ladda ned **FGT_VM64_AZURE-V6-build1637-Fortinet. out** genom att klicka på **https** -länken på samma rad
8. Spara filen för senare


## <a name="deploy-the-fortigate-vm"></a>Distribuera den virtuella FortiGate-datorn

1. Navigera till https://portal.azure.com och logga in på den prenumeration som du vill distribuera den virtuella Fortigate-datorn till
2. Skapa en ny resurs grupp eller öppna resurs gruppen som du vill distribuera den virtuella FortiGate-datorn till
3. Klicka på **Lägg till**
4. Ange "Forti" i dialog rutan **Sök i Marketplace** och välj **Fortinet Fortigate nästa** **generations brand vägg**
5. Välj program varu planen (BYOL om du har en licens eller PAYG om inte) och klicka på **skapa**
6. Fyll i VM-konfigurationen

    ![Fortigate SSL VPN](virtual-machine.png)

7. Ange autentiseringstypen till **lösen ord** och ange administratörsautentiseringsuppgifter för den virtuella datorn
8. Klicka på **Granska + skapa**
9. Klicka på **Skapa**
10. Vänta tills VM-distributionen har slutförts


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Ange en offentlig IP-Statuc och tilldela ett fullständigt kvalificerat domän namn

För en konsekvent användar upplevelse är det önskvärt att ange den offentliga IP-adress som tilldelats den virtuella FortiGate-datorn som ska tilldelas statiskt. Dessutom är det också bra att mappa den till ett fullständigt kvalificerat domän namn av samma skäl.

_Ange en statisk offentlig IP-adress_

1. Navigera till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate
2. På **översikts** skärmen klickar du på den offentliga IP-adressen

    ![Fortigate SSL VPN](public-ip-address.png)

3. Klicka på **statisk** och klicka sedan på **Spara**

_Tilldela ett fullständigt kvalificerat domän namn_

Om du äger ett offentligt dirigerbart domän namn för miljön där den virtuella FortiGate-datorn distribueras, skapar du en värd (A)-post för den virtuella datorn som mappar till den offentliga IP-adressen som är statiskt tilldelad ovan.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Skapa en ny regel för inkommande nätverks säkerhets grupp för TCP-port

1. Navigera till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate
2. Klicka på **nätverk** i den vänstra menyn. Nätverks gränssnittet visas och reglerna för inkommande port visas
3. Klicka på **Lägg till regel för inkommande port**
4. Skapa en ny regel för inkommande portar för TCP 8443

    ![Fortigate SSL VPN](port-rule.png)

5. Klicka på **Lägg till**


## <a name="create-a-custom-azure-app-for-fortigate"></a>Skapa en anpassad Azure App för FortiGate

1. Navigera till https://portal.azure.com och öppna bladet Azure Active Directory för den klient som ska ge identitet för Fortigate-inloggningar
2. Klicka på **företags program** på den vänstra menyn
3. Klicka på **nytt program**
4. Klicka på **program som inte är Galleri**
5. Ange ett namn (t. ex. FortiGate) och klicka på **Lägg till**
6. Klicka på **användare och grupper** på den vänstra menyn
7. Lägg till användare som kan logga in och klicka på **tilldela**
8. Klicka på **enkel inloggning** på den vänstra menyn
9. Klicka på **SAML**
10. Under **grundläggande SAML-konfiguration** klickar du på Penn verktyget för att redigera konfigurationen
11. Konfigurera
    - Identifierare (entitets-ID) som ska `https://<address>/remote/saml/metadata`
    - Svars-URL (intygets konsument tjänst-URL) som ska  `https://<address>/remote/saml/login`
    - Utloggnings-URL till `https://<address>/remote/saml/logout`

    Där `address` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn

    Registrera var och en av dessa URL: er för senare användning –

    - Enhets-ID
    - Svars-URL
    - Utloggnings-URL
12. Klicka på **Spara**
13. Stäng den grundläggande SAML-konfigurationen
14. Under **3 – SAML-signeringscertifikat** , laddar du ned **certifikatet (base64)** och sparar det för senare
15. Under **4 – Konfigurera (App Name)** kopierar du Azures inloggnings-URL, Azure AD-identifierare och Azures utloggnings-URL och sparar dem för senare
    - Azure inloggnings-URL
    - Azure AD-identifierare
    - URL för Azure-utloggning
16. Under **2 – användarattribut och anspråk** klickar du på Penn verktyget för att redigera konfigurationen
17. Klicka på **Lägg till nytt anspråk**
18. Ange namnet **användar** namn
19. Ange källattributet till **User. UserPrincipalName**
20. Klicka på **Spara**
21. Klicka på **Lägg till ett grupp anspråk**
22. Välj **alla grupper**
23. Markera **anpassa namnet på grupp anspråket**
24. Ange namnet **grupp**
25. Klicka på **Spara**


## <a name="prepare-for-group-matching"></a>Förbered för grupp matchning

FortiGate tillåter olika användar Portal upplevelser efter inloggning baserat på grupp medlemskap. Det kan till exempel finnas en upplevelse för marknadsförings gruppen och en annan för gruppen ekonomi.

Konfigurera detta på följande sätt –

### <a name="create-groups-for-users"></a>Skapa grupper för användare

1. Navigera till https://portal.azure.com och öppna bladet Azure Active Directory för den klient som ska ge identitet för Fortigate-inloggningar
2. Klicka på **grupper**
3. Klicka på **ny grupp**
4. Skapa en grupp med
    - Grupptyp = säkerhet
    - Grupp namn = `a meaningful name`
    - Grupp Beskrivning = `a meaningful description for the group`
    - Medlemskaps typ = tilldelad
    - Medlemmar = `users for the user experience that will map to this group`
5. Upprepa steg 3 och 4 för ytterligare användar upplevelser
6. När grupperna har skapats väljer du varje grupp och registrerar objekt-ID: t för var och en
7. Spara dessa objekt-ID: n och grupp namn för senare


## <a name="configure-the-fortigate-vm"></a>Konfigurera den virtuella FortiGate-datorn

### <a name="install-the-license"></a>Installera licensen

1. Navigera till `https://<address>`

    här `address` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn

2. Fortsätt förbi eventuella certifikat fel
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer
4. Om distributionen använder BYOL-modellen visas en uppvarning för att ladda upp en licens. Välj licens filen som skapades tidigare och ladda upp den, klicka på **OK** och starta om den virtuella datorn Fortigate –

    ![Fortigate SSL VPN](license.png)

5. Efter omstarten loggar du in igen med administratörsautentiseringsuppgifter för att validera licensen

### <a name="update-firmware"></a>Uppdatera den inbyggda programvaran

1. Navigera till `https://<address>`

    här `address` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn

2. Fortsätt förbi eventuella certifikat fel
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer
4. I den vänstra menyn klickar du på **system**
5. I den vänstra menyn under system klickar du på **inbyggd program vara**
6. På sidan hantering av inbyggd program vara klickar du på **Bläddra** och väljer den inbyggda program varan som hämtades tidigare
7. Ignorera varningen och klicka på **säkerhetskopiera konfiguration och uppgradering** –

    ![Fortigate SSL VPN](backup-configure-upgrade.png)

8. Klicka på **Fortsätt**
9. När du uppmanas att spara FortiGate-konfigurationen (som en. conf-fil) klickar du på **Spara**
10. Vänta tills den inbyggda program varan har laddats upp, så att den kan användas och för att den virtuella datorn FortiGate ska starta om
11. När FortiGate VM har startats om loggar du in igen med administratörsautentiseringsuppgifter
12. När du uppmanas att utföra installationen av en instrument panel klickar du **senare**
13. När själv studie kursen börjar klickar du på **OK**

### <a name="change-the-management-port-to-tcp"></a>Ändra hanterings porten till TCP

1. Navigera till `https://<address>`

    här `address` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn

2. Fortsätt förbi eventuella certifikat fel
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer
4. I den vänstra menyn klickar du på **system**
5. Ändra HTTPS-porten till **8443** under administrations inställningar
6. Klicka på **Använd**
7. När ändringen har gjorts försöker webbläsaren att läsa in administrations sidan igen, men den kommer att Miss lyckas. Från och med nu är administrations sidans adress `https://<address>`

    ![Fortigate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Ladda upp Azure Active Directory SAML-signeringscertifikat

1. Navigera till `https://<address>`

    här `address` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn

2. Fortsätt förbi eventuella certifikat fel
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer
4. I den vänstra menyn klickar du på **system**
5. Under system klickar du på **certifikat**
6. Klicka på **Importera** – > **fjärrcertifikat**
7. Bläddra till det certifikat som har laddats ned från FortiGate-distributionen för anpassad app i Azure-klienten, markera den och klicka på **OK**

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Ladda upp och konfigurera ett anpassat SSL-certifikat

Du kanske vill konfigurera den virtuella datorn FortiGate med ditt eget SSL-certifikat som har stöd för det FQDN som du använder. Om du har åtkomst till ett SSL-certifikat som paketeras med den privata nyckeln i. PFX-format, det kan användas för detta ändamål

1. Navigera till `https://<address>`

    här `address` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn

2. Fortsätt förbi eventuella certifikat fel
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer
4. I den vänstra menyn klickar du på **system**
5. Under system klickar du på **certifikat**
6. Klicka på **Importera** – > **lokalt certifikat**
7. Klicka på **PKCS #12 certifikat**
8. Bläddra till. PFX-fil som innehåller SSL-certifikatet och den privata nyckeln
9. Ange. PFX-lösenord
10. Ange ett beskrivande namn för certifikatet
11. Klicka på **OK**
12. I den vänstra menyn klickar du på **system**
13. Under system klickar du på **Inställningar**
14. Under administrations inställningar expanderar du List rutan bredvid HTTPS-servercertifikat och väljer det SSL-certifikat som importerades ovan
15. Klicka på **Använd**
16. Stäng webbläsarfönstret och gå sedan tillbaka till `https://<address>`
17. Logga in med FortiGate-administratörens autentiseringsuppgifter och Observera att rätt SSL-certifikat används


### <a name="perform-command-line-configuration"></a>Utför kommando rads konfiguration

_Utföra kommando rads konfiguration för SAML-autentisering_

1. Navigera till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate
2. I den vänstra menyn klickar du på **serie konsol**
3. Logga in i serie konsolen med autentiseringsuppgifter för FortiGate VM-administratör

    I nästa steg krävs de webb adresser som registrerats tidigare. Bygger

    - Enhets-ID
    - Svars-URL
    - Utloggnings-URL
    - Azure inloggnings-URL
    - Azure AD-identifierare
    - URL för Azure-utloggning
4. Kör följande kommandon i serie konsolen –

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > URL: en för Azure-utloggning innehåller en? jokerteck. Detta kräver en specialorder för att den ska kunna anges korrekt för FortiGate-serie konsolen. URL: en är normalt

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    Om du vill ange detta i serie konsolen går du vidare genom att skriva

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    Skriv sedan CTRL + V,

    Klistra sedan in resten av webb adressen i för att slutföra raden

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. Så här bekräftar du konfigurationen, kör –

    ```
    show user saml
    ```

_Utför kommando rads konfiguration för grupp matchning_

1. Navigera till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate
2. I den vänstra menyn klickar du på **serie konsol**
3. Logga in i serie konsolen med autentiseringsuppgifter för FortiGate VM-administratör
4. Kör följande kommandon i serie konsolen –

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    Upprepa detta kommando från redigera `group 1 name` men för varje ytterligare grupp som har en annan portal upplevelse i Fortigate

_Utför kommando rads konfigurationen för tids gräns för autentisering_

1. Navigera till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate
2. I den vänstra menyn klickar du på **serie konsol**
3. Logga in i serie konsolen med autentiseringsuppgifter för FortiGate VM-administratör
4. Kör följande kommandon i serie konsolen –

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Skapa VPN-portaler och brand Väggs princip

1. Navigera till `https://<address>`

    här `address` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn

2. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer
3. I den vänstra menyn klickar du på **VPN**
4. Under VPN klickar du på **SSL-VPN portaler**
5. Klicka på **Skapa ny**
6. Ange ett namn (vanligt vis matcha det med Azure-gruppen som används för att tillhandahålla den anpassade Portal upplevelsen)
7. Klicka på plus tecknet ( **+** ) bredvid käll-IP-pooler, Välj standard-poolen och klicka på **Stäng**
8. Anpassa upplevelsen för den här gruppen. För testning kan du anpassa Portal meddelandet och temat. Du kan också skapa anpassade bok märken som dirigerar användare till interna resurser
9. Klicka på **OK**
10. Upprepa steg 5 till 9 för varje Azure-grupp som kommer att ha en anpassad Portal upplevelse
11. Under VPN klickar du på **SSL-VPN-inställningar**
12. Klicka på plus tecknet ( **+** ) bredvid Lyssna på gränssnitt
13. Välj **PORT1** och klicka på **Stäng**


14. Om ett anpassat SSL-certifikat redan har installerats kan du ändra Server certifikatet till att använda det anpassade SSL-certifikatet på den nedrullningsbara menyn
15. Under autentisering/Portal-mappning klickar du på **Skapa ny**
16. Välj den första Azure-gruppen och matcha den med portalen med samma namn
17. Klicka på **OK**
18. Upprepa steg 15 till 17 för varje Azure-grupp/Portal-par
19. Redigera **alla andra användare/grupper** under autentisering/Portal mappning
20. Ställ in portalen på **fullständig åtkomst**
21. Klicka på **OK**
22. Klicka på **Använd**
23. Bläddra längst upp på sidan SSL-VPN-inställning och klicka på varningen det finns **inga SSL-VPN-principer** 
     **. Klicka här om du vill skapa en ny SSL-VPN-princip med de här inställningarna**
24. Ange ett namn, till exempel **VPN-GRP**
25. Ange utgående gränssnitt till **port**
26. Klicka på **källa**
27. Under adress väljer du **alla**
28. Under användare väljer du den första Azure-gruppen
29. Klicka på **Stäng**
30. Klicka på **mål**
31. Under adress brukar detta vara det interna nätverket. Välj login.microsoft.com för testning
32. Klicka på **Stäng**
33. Klicka på **tjänst**
34. Klicka på **alla**
35. Klicka på **Stäng**
36. Klicka på **OK**
37. I den vänstra menyn klickar du på **princip & objekt**
38. Under princip & objekt klickar du på **brand Väggs princip**
39. Expandera **SSL-VPN tunnel Interface (SSL. root)-> port**
40. Högerklicka på VPN-principen som skapades tidigare ( **VPN-GRP 1** ) och välj **Kopiera**
41. Högerklicka på VPN-principen och välj **Klistra in** > **nedan**
42. Redigera den nya principen och ge den ett annat namn (t. ex. **VPN-Grp2** ) och ändring av gruppen gäller för (en annan Azure-grupp)
43. Högerklicka på den nya principen och ange statusen till **aktive rad**


## <a name="test-sign-in-using-azure"></a>Testa inloggning med Azure

1. Använd en privat webbläsarsession och navigera till `https://<address>` 
2. Inloggningen ska omdirigeras till Azure Active Directory för inloggning
3. När du har angett autentiseringsuppgifter för en användare som har tilldelats FortiGate-appen i Azure-klienten bör rätt användar Portal visas

    ![Fortigate SSL VPN](test-sign-in.png)
