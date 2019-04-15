---
title: Konfigurera enkel inloggning – Azure Active Directory | Microsoft Docs
description: I den här självstudiekursen används Azure Portal till att konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d96799e69e2fdef3a4ffd1a436727e6a58da79
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565996"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Självstudie: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory

I den här självstudiekursen används [Azure Portal](https://portal.azure.com) till att konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory (Azure AD). Använd den här självstudien när det inte finns någon [programspecifik självstudie](../saas-apps/tutorial-list.md) tillgänglig. 

I den här självstudiekursen används Azure Portal till att:

> [!div class="checklist"]
> * välja läge för SAML-baserad enkel inloggning
> * konfigurera programspecifika domäner och URL:er
> * konfigurera användarattribut
> * skapa ett SAML-signeringscertifikat
> * tilldela användare till programmet
> * konfigurera programmet för SAML-baserad enkel inloggning
> * testa SAML-inställningarna

## <a name="before-you-begin"></a>Innan du börjar

1. Om programmet inte har lagts till din Azure AD-klientorganisation går du till [Snabbstart: Lägga till ett program till din Azure AD-klientorganisation](add-application-portal.md).

2. Be leverantören av programmet för informationen som beskrivs i [konfigurera SAML-grundalternativ](#configure-basic-saml-options).

3. Använda en icke-produktionsmiljö för att testa stegen i den här självstudien. Om du inte har en icke-produktionsmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

4. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

## <a name="select-a-single-sign-on-mode"></a>Välja ett läge för enkel inloggning

När du har lagt till ett program till Azure AD-klienten, är du redo att konfigurera enkel inloggning för programmet.

Öppna inställningarna för enkel inloggning:

1. I den [Azure-portalen](https://portal.azure.com), på den vänstra navigeringspanelen väljer **Azure Active Directory**. 

2. Under **hantera** i den **Azure Active Directory** navigeringsfönster som visas, Välj **företagsprogram**. Ett slumpmässigt urval av program i Azure AD-klienten visas. 

3. I den **programtyp** menyn och välj **alla program**, och välj sedan **tillämpa**.

4. Ange namnet på det program som du vill konfigurera enkel inloggning för. Du kan till exempel ange **GitHub-test** att konfigurera programmet som du lade till i den [Lägg till program](add-application-portal.md) Snabbstart.  

     ![Skärmbild som visar sökfältet program.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Välj programmet som du vill konfigurera enkel inloggning.

6. Under den **hantera** väljer **enkel inloggning**. 

7. Välj **SAML** att konfigurera enkel inloggning. Den **ange in enkel inloggning med SAML - förhandsversion** visas.

## <a name="configure-basic-saml-options"></a>Konfigurera grundläggande SAML-alternativ

Konfigurera domänen och URL:erna:

1. Kontakta programvaruleverantören för att få rätt information för följande inställningar:

    | Konfigurationsuppsättning | SP-initierad | idP-initierad | Beskrivning |
    |:--|:--|:--|:--|
    | Identifierare (entitets-ID) | Krävs för vissa appar | Krävs för vissa appar | Identifierar unikt programmet som enkel inloggning har konfigurerats för. Azure AD skickar identifieraren till programmet som målgruppsparametern för SAML-token. Programmet förväntas verifiera den. Detta värde visas även som entitets-ID i alla SAML-metadata som anges av programmet.|
    | Svars-URL | Valfri | Krävs | Anger var programmet förväntas ta emot SAML-token. Svars-URL:en kallas även för URL för konsumenttjänst för försäkran (ACS-URL). |
    | Inloggnings-URL | Krävs | Ange inte | När en användare öppnar den här URL:en omdirigerar tjänstleverantören till Azure AD för att autentisera och logga in användaren. Azure AD använder URL:en för att starta programmet från Office 365 eller Azure AD-åtkomstpanelen. När det är tomt används Azure AD som förlitar sig på identitetsprovider att starta enkel inloggning när en användare startar programmet.|
    | Vidarebefordransstatus | Valfri | Valfri | Anger för programmet var användaren ska omdirigeras när autentiseringen har slutförts. Värdet är vanligtvis en giltig URL för programmet. Men använder vissa program det här fältet på olika sätt. Kontakta programleverantören om du vill ha mer information.
    | Utloggnings-URL | Valfri | Valfri | Används för att skicka SAML-utloggning svar tillbaka till programmet.


2. Om du vill redigera grundalternativ för SAML-konfiguration, Välj den **redigera** ikon (en penna) i det övre högra hörnet av den **SAML grundkonfiguration** avsnittet.

     ![Konfigurera certifikat](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Ange informationen som tillhandahålls av tillverkaren av programmet i steg 1 i lämpliga fält på sidan.

4. Längst ned på sidan Välj **spara**.

## <a name="configure-user-attributes-and-claims"></a>Konfigurera användarattribut och anspråk 

Du kan styra vilken information som Azure AD skickar till programmet i SAML-token när en användare loggar in. Du kan styra den här informationen genom att konfigurera användarattribut. Du kan till exempel konfigurera Azure AD för att skicka användarens namn, e-post och anställnings-ID till programmet när en användare loggar in. 

Dessa attribut kan vara obligatoriska eller valfria för att göra så att enkel inloggning fungerar som det ska. Mer information finns i den [programspecifika självstudiekursen](../saas-apps/tutorial-list.md), eller fråga programleverantören.

1. Om du vill redigera användarattribut och anspråk, Välj den **redigera** ikon (en penna) i det övre högra hörnet av den **användarattribut och anspråk** avsnittet.

   Den **namn identifierarvärde** anges med standardvärdet *user.principalname*. Användaridentifieraren identifierar unikt varje användare i programmet. Exempel: om e-postadressen är både användarnamnet och den unika identifieraren anger du värdet *user.mail*.

2. Att ändra den **namn identifierarvärde**väljer den **redigera** ikon (en penna) för den **namnet ID-värde** fält. Gör ändringarna till ID-format och källa, efter behov. Spara ändringarna när du är klar. Mer information om hur du anpassar anspråk finns i den [anpassa anspråk som utfärdats i SAML-token för företagsprogram](../develop/active-directory-saml-claims-customization.md) artikel.

3. Om du vill lägga till ett anspråk, Välj **Lägg till nytt anspråk** överst på sidan. Ange den **namn** och välj lämplig källa. Om du väljer den **attributet** källa, måste du välja den **källattribut** du vill använda. Om du väljer den **Translation** källa, måste du välja den **omvandling** och **Parameter 1** du vill använda.

4. Välj **Spara**. Nytt anspråk visas i tabellen.
 
## <a name="generate-a-saml-signing-certificate"></a>Generera ett SAML-signeringscertifikat

Azure AD använder ett certifikat för att signera de SAML-token som det skickar till programmet. 

1. Om du vill generera ett nytt certifikat, Välj den **redigera** ikon (en penna) i det övre högra hörnet av den **SAML-signeringscertifikat** avsnittet.

2. I den **SAML-signeringscertifikat** väljer **nytt certifikat**.

3. I den nya certifikatraden som visas, ange den **förfallodatum**. Mer information om tillgängliga konfigurationsalternativ finns i den [avancerade alternativ för certifikatsignering](certificate-signing-options.md) artikeln.

4. Välj **spara** överst i den **SAML-signeringscertifikat** avsnittet. 

## <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Det är en bra idé att testa den enkel inloggning med flera användare eller grupper innan du distribuerar programmet till din organisation.

> [!NOTE]
>
> Dessa steg leder dig till den **användare och grupper** konfigurationsavsnittet i portalen. När du är klar måste du gå tillbaka till den **enkel inloggning** avsnitt för att slutföra den här självstudien.

Tilldela en användare eller grupp till programmet:

1. Öppna programmet i portalen om det inte redan är öppet.
2. I den vänstra navigeringspanelen för programmet, väljer **användare och grupper**.
3. Välj **Lägg till användare**.
4. I den **Lägg till tilldelning** väljer **användare och grupper**.
5. Om du vill hitta en viss användare, skriver du användarnamnet i den **Välj en medlem eller Bjud in en extern användare** box. Sedan väljer användarens profilfoto eller logotyp och välj sedan **Välj**. 
6. I den **Lägg till tilldelning** väljer **tilldela**. När du är klar de valda användarna visas i den **användare och grupper** lista.

## <a name="set-up-the-application-to-use-azure-ad"></a>Konfigurera programmet till att använda Azure AD

Nästan klart.  Du måste konfigurera programmet till att använda Azure AD som en SAML-identitetsprovider som ett sista steg. 

1. Rulla ned till den **konfigurera <applicationName>**  avsnittet. Den här självstudien det här avsnittet kallas **konfigurera GitHub-test**. 
2. Kopiera värdet från varje rad i det här avsnittet. Klistra sedan in varje värde i rätt rad i den **SAML grundkonfiguration** avsnittet. Till exempel kopiera den **inloggnings-URL** värdet från den **konfigurera GitHub-test** avsnittet och klistra in den i den **inloggning på URL: en** i den **grundläggande SAML-konfiguration**  avsnittet och så vidare.
3. När du har klistrat in alla värden i lämpliga fält, Välj **spara**.

## <a name="test-single-sign-on"></a>Testa enkel inloggning

Är du redo att testa dina inställningar.  

1. Öppna inställningarna för enkel inloggning för programmet. 
2. Bläddra till den **Validera enkel inloggning med <applicationName>**  avsnittet. Den här självstudien det här avsnittet kallas **konfigurera GitHub-test**.
3. Välj **Test**. Testningsalternativen visas.
4. Välj **logga in som aktuell användare**. Med det här testet kan du först se om enkel inloggning fungerar för dig, administratören.

Om det finns något fel visas ett felmeddelande. Utför följande steg:

1. Kopiera och klistra in informationen i rutan **Hur ser felet ut?**.

    ![Få råd om lösning](media/configure-single-sign-on-portal/error-guidance.png)

2. Välj **få vägledning för lösning**. Rotorsaken och råd om lösning visas.  I det här exemplet hade användaren inte tilldelats till programmet.

3. Läs vägledningen upplösning och sedan, om det är möjligt att åtgärda problemet.

4. Kör testet igen tills det slutförts helt.

## <a name="next-steps"></a>Nästa steg
I den här självstudien konfigurerade du inställningarna för enkel inloggning för ett program. När du slutförde konfigurationen tilldelade du en användare till programmet och konfigurerade programmet så att det använder SAML-baserad enkel inloggning. När allt detta hade slutförts verifierade du att SAML-inloggningen fungerade som skulle.

Du gjorde detta:
> [!div class="checklist"]
> * Valde SAML för enkel inloggning-läget
> * Kontaktade programleverantören för att konfigurera domän och URL:er
> * Konfigurerade användarattribut
> * Skapade ett SAML-signeringscertifikat
> * Tilldelade manuellt användare eller grupper till programmet
> * Konfigurerade programmet så att det använder Azure AD som SAML-identitetsprovider
> * Testade SAML-baserad enkel inloggning

Om du vill distribuera programmet till fler användare i din organisation, använder du automatisk användaretablering.

> [!div class="nextstepaction"]
> [Lär dig hur du tilldelar användare med automatisk etablering](configure-automatic-user-provisioning-portal.md)


