---
title: Distributions guide för FortiGate | Microsoft Docs
description: Konfigurera och arbeta med Fortinet-FortiGate nästa generations brand Väggs produkt.
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
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273338"
---
# <a name="fortigate-deployment-guide"></a>Distributions guide för FortiGate

Med hjälp av den här distributions guiden får du lära dig hur du konfigurerar och arbetar med Fortinet-FortiGate nästa generations brand Väggs produkt.

## <a name="redeem-the-fortigate-license"></a>Lös in FortiGate-licensen

Fortinet FortiGate nästa generations brand Väggs produkt är tillgänglig som en virtuell dator i Azure Infrastructure as a Service (IaaS). Det finns två licensierings lägen för den här virtuella datorn: betala per användning och hämta din egen licens.

Fortinet kan ge medlemmar i Azure Active Directory (Azure AD) "till gång till produktion av Secure hybrid Access (SHA)"-teamet med licenser. I de fall där ingen licens har tillhandahållits fungerar även distributionen betala per användning.

Om en licens har utfärdats tillhandahåller Fortinet en registrerings kod som måste lösas in online igen.

![Skärm bild av FortiGate SSL VPN Registration Code.](registration-code.png)

1. Registrera dig på https://support.fortinet.com/ .
2. När du har registrerat dig loggar du in på https://support.fortinet.com/ .
3. Gå till **till gångs**  >  **register/aktivera**.
4. Ange registrerings koden som tillhandahålls av Fortinet.
5. Ange registrerings koden, Välj **produkten kommer att användas av en icke-myndighets användare**och välj **Nästa**.
6. Ange en produktbeskrivning (till exempel Fortigate), ange Fortinet-partnern som **andra**  >  **Microsoft**och välj **Nästa**.
7. Godkänn **produkt registrerings avtalet för Fortinet**och välj **Nästa**.
8. Godkänn **villkoren** och välj **Bekräfta**.
9. Välj **licens filen Ladda ned**och spara licensen för senare.


## <a name="download-firmware"></a>Ladda ned inbyggd program vara

Fortinet FortiGate Azure VM levereras för närvarande inte med den inbyggda program vara versionen som krävs för SAML-autentisering. Den senaste versionen måste hämtas från Fortinet.

1. Logga in på https://support.fortinet.com/ .
2. Gå till **Hämta**  >  **avbildningar av inbyggd program vara**.
3. Till höger om **versions anteckningar**väljer du **Hämta**.
4. Välj **v6.** > **3-6.** > **6,4.**.
5. Ladda ned **FGT_VM64_AZURE-V6-build1637-Fortinet. out** genom att välja **https** -länken på samma rad.
6. Spara filen för senare.


## <a name="deploy-the-fortigate-vm"></a>Distribuera den virtuella FortiGate-datorn

1. Gå till https://portal.azure.com och logga in på den prenumeration som du vill distribuera den virtuella Fortigate-datorn till.
2. Skapa en ny resurs grupp eller öppna den resurs grupp som du vill distribuera den virtuella FortiGate-datorn till.
3. Välj **Lägg till**.
4. Skriv *Forti*i **Sök på Marketplace**. Välj **Fortinet-Fortigate nästa generations brand vägg**.
5. Välj program varu planen (ta egen licens om du har en licens eller betala per användning om du inte vill det). Välj **Skapa**.
6. Fyll i konfigurationen för den virtuella datorn.

    ![Skärm bild av skapa en virtuell dator.](virtual-machine.png)

7. Ange **Autentiseringstyp** till **lösen ord**och ange administratörs behörighet för den virtuella datorn.
8. Välj **Granska + skapa** > **Skapa**.
9. Vänta tills den virtuella dator distributionen har slutförts.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Ange en statisk offentlig IP-adress och tilldela ett fullständigt kvalificerat domän namn

För en konsekvent användar upplevelse anger du den offentliga IP-adress som tilldelats den virtuella FortiGate-datorn som ska tilldelas statiskt. Mappa dessutom det till ett fullständigt kvalificerat domän namn (FQDN).

1. Gå till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate.
2. På sidan **Översikt** väljer du den offentliga IP-adressen.

    ![Skärm bild av Fortigate SSL VPN.](public-ip-address.png)

3. Välj **statiskt**  >  **Spara**.

Om du äger ett offentligt dirigerbart domän namn för miljön där den virtuella FortiGate-datorn distribueras, skapar du en värd (A) post för den virtuella datorn. Den här posten mappar till föregående offentliga IP-adress som har tilldelats statiskt.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>Skapa en ny regel för inkommande nätverks säkerhets grupp för TCP-port

1. Gå till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate.
2. Välj **nätverk**i menyn till vänster. Nätverks gränssnittet visas och reglerna för inkommande port visas.
3. Välj **Lägg till regel för inkommande port**.
4. Skapa en ny regel för inkommande portar för TCP 8443.

    ![Skärm bild av Lägg till inkommande säkerhets regel.](port-rule.png)

5. Välj **Lägg till**.


## <a name="create-a-custom-azure-app-for-fortigate"></a>Skapa en anpassad Azure-app för FortiGate

1. Gå till https://portal.azure.com och öppna Azure AD-fönstret för den klient som ska ge identiteten för Fortigate-inloggningar.
2. På den vänstra menyn väljer du **företags program**.
3. Välj **nytt program**program  >  **utanför galleriet**.
4. Ange ett namn (till exempel FortiGate) och välj **Lägg till**.
5. Välj **användare och grupper**på den vänstra menyn.
6. Lägg till användare som kan logga in och välj **tilldela**.
7. På den vänstra menyn väljer du **enkel inloggning**.
8. Välj **SAML**.
9. Under **grundläggande SAML-konfiguration**väljer du Penn ikonen för att redigera konfigurationen.
10. Konfigurera följande:
    - **Identifierare (entitets-ID)** som ska användas `https://<address>/remote/saml/metadata` .
    - **Svars-URL (intygets konsument tjänst-URL)** ska vara  `https://<address>/remote/saml/login` .
    - **URL för utloggning** ska vara `https://<address>/remote/saml/logout` .

    `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella FortiGate-datorn.

11. Registrera var och en av dessa URL: er för senare användning: entitets-ID, svars-URL och utloggning-URL.
12. Välj **Spara**och Stäng **grundläggande SAML-konfiguration**.
13. Under **3 – SAML-signeringscertifikat**, ladda ned **certifikat (base64)** och spara det för senare.
14. Under **4 – Konfigurera (App Name)** kopierar du Azures inloggnings-URL, Azure AD-identifierare och Azures utloggnings-URL och sparar dem för senare.
15. Under **2 – användarattribut och anspråk**väljer du Penn ikonen för att redigera konfigurationen.
16. Välj **Lägg till nytt anspråk**och ange namnet till **användar**namn.
17. Ange källattributet till **User. UserPrincipalName**.
18. Välj **Spara**  >  **Lägg till ett grupp anspråk**  >  **alla grupper**.
19. Välj **anpassa namnet på grupp anspråket**och ange namnet till **grupp**.
20. Välj **Spara**.


## <a name="prepare-for-group-matching"></a>Förbered för grupp matchning

FortiGate tillåter olika användar Portal upplevelser efter inloggning, baserat på grupp medlemskap. Det kan till exempel finnas en upplevelse för marknadsförings gruppen och en annan för gruppen ekonomi. Så här skapar du grupper för användare:

1. Gå till https://portal.azure.com och öppna Azure AD-fönstret för den klient som ska ge identiteten för Fortigate-inloggningar.
2. Välj **grupper**  >  **ny grupp**.
3. Skapa en grupp med följande information:
    - Grupptyp = säkerhet
    - Grupp namn = `a meaningful name`
    - Grupp Beskrivning = `a meaningful description for the group`
    - Medlemskaps typ = tilldelad
    - Medlemmar = `users for the user experience that will map to this group`
4. Upprepa steg 3 och 4 för eventuella ytterligare användar upplevelser.
5. När du har skapat grupper väljer du varje grupp och registrerar **objekt-ID** för var och en.
6. Spara dessa objekt-ID: n och grupp namn för senare.


## <a name="configure-the-fortigate-vm"></a>Konfigurera den virtuella FortiGate-datorn

I följande avsnitt lär du dig hur du konfigurerar den virtuella FortiGate-datorn.

### <a name="install-the-license"></a>Installera licensen

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. Om distributionen använder sig av en egen-licens modell visas en uppfråga om att ladda upp en licens. Välj licens filen som skapades tidigare och ladda upp den. Välj **OK** och starta om den virtuella Fortigate-datorn.

    ![Skärm bild av FortiGate VM-licens.](license.png)

5. Efter omstarten loggar du in igen med administratörsautentiseringsuppgifter för att validera licensen.

### <a name="update-firmware"></a>Uppdatera inbyggd program vara

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **systemets**  >  **inbyggda program vara**.
5. I **hantering av inbyggd program vara**väljer du **Bläddra**och väljer den inbyggda program varan som hämtades tidigare.
6. Ignorera varningen och välj **config och Upgrade (säkerhetskopiera**).

    ![Skärm bild av hantering av inbyggd program vara.](backup-configure-upgrade.png)

7. Välj **Fortsätt**.
8. När du uppmanas att spara FortiGate-konfigurationen (som en. conf-fil) väljer du **Spara**.
9. Vänta tills den inbyggda program varan har laddats upp och tillämpats. Vänta tills den virtuella FortiGate-datorn har startats om.
10. När FortiGate VM har startats om loggar du in igen med administratörs behörigheterna.
11. När du uppmanas att ställa in instrument panelen väljer du **senare**.
12. Välj **OK**när video kursen börjar.

### <a name="change-the-management-port-to-tcp"></a>Ändra hanterings porten till TCP

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **system**.
5. Ändra HTTPS-porten till **8443**under **administrations inställningar**och välj **Använd**.
6. När ändringen har tillämpats försöker webbläsaren att läsa in administrations sidan igen, men den Miss lyckas. Från och med nu är administrations sidans adress `https://<address>` .

    ![Skärm bild av Ladda upp fjärrcertifikat.](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Ladda upp Azure AD-certifikatet för SAML-signering

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **system**  >  **certifikat**.
5. Välj **Importera**  >  **fjärrcertifikat**.
6. Bläddra till det certifikat som har hämtats från FortiGate-distributionen för anpassad app i Azure-klienten. Markera den och välj **OK**.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Ladda upp och konfigurera ett anpassat SSL-certifikat

Du kanske vill konfigurera den virtuella datorn FortiGate med ditt eget SSL-certifikat som har stöd för det FQDN som du använder. Om du har åtkomst till ett SSL-certifikat som paketeras med den privata nyckeln i PFX-format kan det användas för detta ändamål.

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Fortsätt förbi eventuella certifikat fel.
3. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
4. På den vänstra menyn väljer du **system**  >  **certifikat**.
5. Välj **Importera**  >  **Local Certificate**  >  **PKCS #12-certifikat**för lokal certifikat.
6. Bläddra till. PFX-fil som innehåller SSL-certifikatet och den privata nyckeln.
7. Ange. PFX-lösenord och ett beskrivande namn för certifikatet. Välj sedan **OK**.
8. På den vänstra **menyn väljer du**  >  **Systeminställningar**.
9. Under **administrations inställningar**expanderar du listan bredvid **https-servercertifikat**och väljer det SSL-certifikat som importerades tidigare.
10. Välj **Använd**.
11. Stäng webbläsarfönstret och gå till `https://<address>` .
12. Logga in med autentiseringsuppgifterna för FortiGate-administratören. Nu bör du se rätt SSL-certifikat som används.


### <a name="perform-command-line-configuration"></a>Utföra kommando rads konfiguration

I följande avsnitt finns steg för olika konfigurationer med hjälp av kommando raden.

#### <a name="for-saml-authentication"></a>För SAML-autentisering

1. Gå till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate.
2. På den vänstra menyn väljer du **serie konsol**.
3. Logga in i serie konsolen med autentiseringsuppgifterna för FortiGate VM-administratörer. I nästa steg krävs de URL: er som du spelat in tidigare:
    - Enhets-ID
    - Svars-URL
    - Utloggnings-URL
    - Azure inloggnings-URL
    - Azure AD-identifierare
    - URL för Azure-utloggning
4. Kör följande kommandon i serie konsolen:

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
    > URL: en för Azure-utloggning innehåller ett- `?` blank steg. Detta kräver en specialorder för att den ska kunna anges korrekt för FortiGate-serie konsolen. URL: en är vanligt vis `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1` . Om du vill ange detta i serie konsolen skriver du:
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    Skriv sedan CTRL + V och klistra in resten av webb adressen i för att slutföra raden: 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. Bekräfta konfigurationen genom att köra följande:

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>För grupp matchning

1. Gå till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate.
2. På den vänstra menyn väljer du **serie konsol**.
3. Logga in i serie konsolen med autentiseringsuppgifterna för FortiGate VM-administratörer.
4. Kör följande kommandon i serie konsolen:

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

    Upprepa dessa kommandon (som börjar med den andra raden i koden) för varje ytterligare grupp som kommer att ha en annan portal upplevelse i FortiGate.

#### <a name="for-authentication-timeout"></a>För autentiserings-timeout

1. Gå till https://portal.azure.com och öppna inställningarna för den virtuella datorn Fortigate.
2. På den vänstra menyn väljer du **serie konsol**.
3. Logga in i serie konsolen med autentiseringsuppgifterna för FortiGate VM-administratörer.
4. Kör följande kommandon i serie konsolen:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>Skapa VPN-portaler och brand Väggs princip

1. Gå till `https://<address>`. Här `<address>` är FQDN eller den offentliga IP-adress som tilldelats den virtuella Fortigate-datorn.

2. Logga in med de administratörsautentiseringsuppgifter som angavs under distributionen av virtuella FortiGate-datorer.
3. På den vänstra menyn väljer du **VPN**  >  **SSL – VPN portaler**  >  **Skapa nytt**.
6. Ange ett namn (vanligt vis matcha det med Azure-gruppen som används för att tillhandahålla den anpassade Portal upplevelsen).
7. Välj plus tecknet ( **+** ) bredvid **käll-IP-pooler**, Välj standardpoolen och välj sedan **Stäng**.
8. Anpassa upplevelsen för den här gruppen. För testning kan du anpassa Portal meddelandet och temat. Du kan också skapa anpassade bok märken som dirigerar användare till interna resurser.
9. Välj **OK**.
10. Upprepa steg 5-9 för varje Azure-grupp som kommer att ha en anpassad Portal upplevelse.
11. Under VPN väljer du **SSL-VPN-inställningar**.
12. Välj plus tecknet ( **+** ) bredvid **Lyssna på gränssnitt**, Välj **PORT1**och välj sedan **Stäng**.
14. Om du tidigare har installerat ett anpassat SSL-certifikat ändrar du **Server certifikatet** till att använda det anpassade SSL-certifikatet på den nedrullningsbara menyn.
15. Under **autentisering/Portal mappning**väljer du **Skapa ny**. Välj den första Azure-gruppen och matcha den med portalen med samma namn. Välj sedan **OK**.
18. Upprepa steg 15-17 för varje par av en Azure-grupp och en portal.
19. Under **autentisering/Portal mappning**, redigera **alla andra användare/grupper**.
20. Ställ in portalen på **fullständig åtkomst**och välj **OK**  >  **Använd**.
23. Bläddra längst upp på sidan **SSL-VPN-inställning** och välj **inga SSL-VPN-principer finns. Klicka här om du vill skapa en ny SSL-VPN-princip med de här inställningarna.**
24. Ange ett namn, t. ex. **VPN-GRP**. Ange sedan **utgående gränssnitt** till **port**och välj **källa**.
27. Under **adress**väljer du **alla**.
28. Under **användare**väljer du den första Azure-gruppen.
29. Välj **Stäng**  >  **destination**. Under **adress**är detta vanligt vis det interna nätverket. Välj **login.Microsoft.com** för testning.
32. Välj **Stäng**  >  **tjänst**  >  **alla**. Välj sedan **Stäng**  >  **OK**.
37. På den vänstra menyn väljer du **princip & objekt**  >  **brand Väggs princip**.
39. Expandera **SSL-port (SSL-VPN tunnel Interface)**  >  **port**.
40. Högerklicka på VPN-principen som skapades tidigare (**VPN-GRP 1**) och välj **Kopiera**.
41. Högerklicka på VPN-principen och välj **Klistra in**  >  **nedan**.
42. Redigera den nya principen och ge den ett annat namn (t. ex. **VPN-Grp2**). Ändra även den grupp den gäller för (till en annan Azure-grupp).
43. Högerklicka på den nya principen och ange status till **aktive rad**.


## <a name="test-sign-in-by-using-azure"></a>Testa inloggning med Azure

1. Använd en privat webbläsarsession, gå till `https://<address>` .  
2. Inloggningen ska omdirigeras till Azure AD för inloggning.
3. När du har angett autentiseringsuppgifter för en användare som har tilldelats FortiGate-appen i Azure-klienten bör rätt användar Portal visas.

    ![Skärm bild av FortiGate SSL VPN](test-sign-in.png)
