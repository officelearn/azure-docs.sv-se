---
title: Konfigurera Azure Multi-Factor Authentication för Windows Virtual Desktop – Azure
description: Konfigurera Azure Multi-Factor Authentication för ökad säkerhet i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998477"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Konfigurera multifaktorautentisering i Azure

Windows-klienten för virtuellt Windows-skrivbord är ett utmärkt alternativ för att integrera virtuella Windows-datorer med din lokala dator. Men när du konfigurerar ditt Windows-konto för virtuella skriv bord till Windows-klienten, finns det vissa mått som du måste vidta för att hålla dig trygg och dina användare.

När du först loggar in frågar klienten efter ditt användar namn, lösen ord och Azure MFA. Efter det kommer klienten att komma ihåg din token från din Azure Active Directory (AD) företags program nästa gången du loggar in. När du väljer **kom ihåg mig**kan användarna logga in efter omstart av klienten utan att behöva ange sina autentiseringsuppgifter på annat sätt.

Även om det är praktiskt att komma ihåg autentiseringsuppgifterna, kan det också göra distributioner i företags scenarier eller personliga enheter mindre säkra. För att skydda dina användare måste du kontrol lera att klienten fortfarande ber om autentiseringsuppgifter för Azure Multi-Factor Authentication (MFA). I den här artikeln visas hur du konfigurerar principen för villkorlig åtkomst för Windows Virtual Desktop för att aktivera den här inställningen.

## <a name="prerequisites"></a>Krav

Det här behöver du för att komma igång:

- Tilldela alla dina användare en av följande licenser:
  - Microsoft 365 E3 eller E5
  - Azure Active Directory Premium P1 eller P2
  - Enterprise Mobility + Security E3 eller E5
- En Azure Active Directory grupp med dina användare tilldelade som grupp medlemmar.
- Aktivera Azure MFA för alla dina användare. Mer information om hur du gör finns i [så här kräver du tvåstegsverifiering för en användare](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>Följande inställning gäller även för [webb klienten för virtuella Windows-datorer](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Anmäl dig till principen för villkorlig åtkomst

1. Öppna **Azure Active Directory**.

2. Gå till fliken **alla program** . I list rutan "program typ" väljer du **företags program**och söker sedan efter **Windows Virtual Desktop-klienten**.

    ![En skärm bild av fliken alla program. Användaren angav "Windows Virtual Desktop Client" i Sök fältet och appen visas i Sök resultaten.](media/all-applications-search.png)

3. Välj **villkorlig åtkomst**.

    ![En skärm bild som visar användaren som hovrar mus markören över fliken villkorlig åtkomst.](media/conditional-access-location.png)

4. Välj **+ ny princip**.

   ![En skärm bild av sidan för villkorlig åtkomst. Användaren håller mus markören över knappen Ny princip.](media/new-policy-button.png)

5. Ange ett **namn** för **regeln**och **Välj** sedan * namnet på den **grupp** som du skapade i kraven.

6. Välj **Välj**och välj sedan **färdig**.

7. Öppna sedan **molnappar eller åtgärder**.

8. På **Välj** -panelen väljer du **Windows Virtual Desktop** Enterprise-appen.

    ![En skärm bild av sidan molnappar eller åtgärder. Användaren har valt appen för virtuella Windows-datorer genom att markera kryss rutan bredvid den. Den valda appen är markerad i rött.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Du bör också se den Windows-app för virtuella skriv bord som valdes på vänster sida av skärmen, som du ser i följande bild. Du behöver både Windows Virtual Desktop och Windows Virtual Desktop Client Enterprise-appar för att principen ska fungera.
    >
    > ![En skärm bild av sidan molnappar eller åtgärder. Klient program för virtuella Windows-datorer och Windows-appar för virtuella skriv bord är markerade i rött.](media/cloud-apps-enterprise-selected.png)

9. Välj **Välj**

10. Sedan öppnar du **Grant** 

11. Välj **Kräv Multi-Factor Authentication**och välj sedan **Kräv en av de valda kontrollerna**.
   
    ![En skärm bild av sidan för beviljande. "Kräv Multi-Factor Authentication" är valt.](media/grant-page.png)

    >[!NOTE]
    >Om du har MDM-registrerade enheter i din organisation och inte vill att de ska Visa MFA-prompten, kan du också välja **Kräv att enheten ska markeras som kompatibel**.

12. Välj **session**.

13. Ange **inloggnings frekvensen** till **aktiv**och ändra värdet till **1 timme**.

    ![En skärm bild av sidan session. Session-menyn visar de nedrullningsbara menyerna för inloggnings frekvensen som har ändrats till "1" och "timmar".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Aktiva sessioner i din Windows Virtual Desktop-miljö fortsätter att fungera när du ändrar principen. Men om du kopplar från eller signerar måste du ange dina autentiseringsuppgifter igen efter 60 minuter. När du ändrar inställningarna kan du utöka tids gränsen så mycket som du vill (så länge den överensstämmer med din organisations säkerhets princip).
    >
    >Standardinställningen är ett rullande fönster på 90 dagar, vilket innebär att klienten uppmanas att logga in igen när de försöker få åtkomst till en resurs efter att ha varit inaktiv på datorn i 90 dagar eller längre.

14. Aktivera principen.

15. Bekräfta principen genom att välja **skapa** .

Nu är du klar! Du kan testa principen för att kontrol lera att listan över tillåtna program fungerar som den ska.
