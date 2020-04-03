---
title: Konfigurera Azure multifaktorautentisering för Windows Virtual Desktop - Azure
description: Konfigurera Azure multifaktorautentisering för ökad säkerhet i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b3c47e1bbe5efdc5ee303305e52a785a49d0c00
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586878"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Konfigurera Azure Multi Factor-autentisering

Windows-klienten för Windows Virtual Desktop är ett utmärkt alternativ för att integrera Windows Virtual Desktop med din lokala dator. Men när du konfigurerar ditt Windows Virtual Desktop-konto till Windows-klienten finns det vissa åtgärder som du måste vidta för att skydda dig själv och dina användare.

När du loggar in första gången frågar klienten efter ditt användarnamn, lösenord och Azure MFA. Därefter kommer klienten att komma ihåg din token från ditt Azure Active Directory -företagsprogram nästa gång du loggar in. När du väljer **Kom ihåg mig**kan användarna logga in efter att ha startat om klienten utan att behöva ange sina autentiseringsuppgifter igen.

Det är praktiskt att komma ihåg autentiseringsuppgifter, men det kan också göra distributioner på Enterprise-scenarier eller personliga enheter mindre säkra. För att skydda användarna måste du se till att klienten fortsätter att be om MFA-autentiseringsuppgifter (Azure Multi-Factor Authentication). I den här artikeln visas hur du konfigurerar principen villkorlig åtkomst för Windows Virtual Desktop för att aktivera den här inställningen.

## <a name="prerequisites"></a>Krav

Det här behöver du för att komma igång:

- Tilldela alla användare en av följande licenser:
  - Microsoft 365 E3 eller E5
  - Azure Active Directory Premium P1 eller P2
  - Enterprise Mobility + Säkerhet E3 eller E5
- En Azure Active Directory-grupp med användarna som tilldelats som gruppmedlemmar.
- Aktivera Azure MFA för alla dina användare. Mer information om hur du gör det finns i Så här kräver du [tvåstegsverifiering för en användare](/active-directory/authentication/howto-mfa-userstates).

>[!NOTE]
>Följande inställning gäller även för [webbklienten för Windows Virtual Desktop](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Anmäl dig till principen villkorlig åtkomst

1. Öppna **Azure Active Directory**.

2. Gå till fliken **Alla program.** I den nedrullningsbara menyn Programtyp väljer du **Enterprise Applications**och söker sedan efter **Windows Virtual Desktop Client**.

    ![En skärmbild av fliken Alla program. Användaren skrev in "windows virtual desktop client" i sökfältet och appen har visats i sökresultaten.](media/all-applications-search.png)

3. Välj **Villkorlig åtkomst**.

    ![En skärmbild som visar användaren som håller muspekaren över fliken Villkorlig åtkomst.](media/conditional-access-location.png)

4. Välj **+ Ny princip**.

   ![En skärmbild av sidan Villkorlig åtkomst. Användaren håller muspekaren över den nya principknappen.](media/new-policy-button.png)

5. Ange ett **namn** på **regeln**och **välj** sedan *namnet på den **grupp** som du skapade i förutsättningarna.

6. Välj **Välj**och välj sedan **Klar**.

7. Öppna sedan **Molnappar eller -åtgärder**.

8. Välj windows **Virtual Desktop** Enterprise-appen på fliken **Välj.**

    ![En skärmbild av sidan Molnappar eller åtgärder. Användaren har valt windows virtual desktop-appen genom att markera markeringen bredvid den. Den valda appen är markerad i rött.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Du bör också se windows virtual desktop client-appen som valts till vänster på skärmen, vilket visas i följande bild. Du behöver både Windows Virtual Desktop- och Windows Virtual Desktop Client Enterprise-apparna för att principen ska fungera.
    >
    > ![En skärmbild av sidan Molnappar eller åtgärder. Apparna Windows Virtual Desktop och Windows Virtual Desktop Client är markerade i rött.](media/cloud-apps-enterprise-selected.png)

9. Välj **Välj**

10. Öppna sedan **Grant** 

11. Välj **Kräv multifaktorautentisering**och välj sedan **Kräv en av de markerade kontrollerna**.
   
    ![En skärmbild av sidan Bevilja. "Kräv multifaktorautentisering" har valts.](media/grant-page.png)

    >[!NOTE]
    >Om du har MDM-registrerade enheter i organisationen och inte vill att de ska visa MFA-prompten kan du också välja **Kräv att enheten ska markeras som kompatibel**.

12. Välj **Session**.

13. Ställ in **inloggningsfrekvensen** till **Aktiv**och ändra sedan dess värde till **1 timmar**.

    ![En skärmbild av sidan Session. Sessionsmenyn visar att rullgardinsmenyn för inloggningsfrekvens har ändrats till "1" och "Timmar".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Aktiva sessioner i Windows Virtual Desktop-miljö fortsätter att fungera när du ändrar principen. Men om du kopplar från eller signerar måste du ange dina autentiseringsuppgifter igen efter 60 minuter. När du ändrar inställningarna kan du förlänga timeout-perioden så mycket du vill (så länge den stämmer överens med organisationens säkerhetsprincip).
    >
    >Standardinställningen är ett rullande fönster på 90 dagar, vilket innebär att klienten ber användarna att logga in igen när de försöker komma åt en resurs efter att ha varit inaktiv på sin dator i 90 dagar eller längre.

14. Aktivera principen.

15. Välj **Skapa** för att bekräfta principen.

Nu är du klar! Känn dig fri att testa principen för att se till att din tillåt lista fungerar som avsett.
