---
title: 'Azure AD Connect: Aktivera tillbakaskrivning av enheter | Microsoft Docs'
description: Det här dokumentet beskriver hur du aktiverar tillbakaskrivning av enheter med Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: fa8cdaf1a21a59a5bb695e3be90382f1e33823a2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590597"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Aktivera tillbakaskrivning av enheter
> [!NOTE]
> Det krävs en prenumeration på Azure AD Premium för tillbakaskrivning av enheter.
> 
> 

Följande dokumentation innehåller information om hur du aktiverar funktionen för tillbakaskrivning av enhet i Azure AD Connect. Tillbakaskrivning av enheter används i följande scenarier:

* Aktivera villkorlig åtkomst baserad på AD FS-enheter (2012 R2 eller högre) skyddade applikationer (förtroenden för beroende part).

Det ger ökad säkerhet och garanterar att beviljas åtkomst till program bara till betrodda enheter. Mer information om villkorlig åtkomst finns [hantera risker med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) och [konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>Enheter måste finnas i samma skog som användarna. Eftersom enheter måste skrivas tillbaka till en enda skog, stöder funktionen för närvarande inte en distribution med flera skogar för användaren.</li>
> <li>Konfigurationsobjekt för endast en enhet registreringen kan läggas till lokala Active Directory-skogen. Den här funktionen är inte kompatibel med en topologi där lokala Active Directory synkroniseras till flera Azure AD-kataloger.</li>> 

## <a name="part-1-install-azure-ad-connect"></a>Del 1: Installera Azure AD Connect
Installera Azure AD Connect med anpassade eller standardinställningar. Microsoft rekommenderar att börja med alla användare och grupper har synkroniserats innan du aktiverar tillbakaskrivning av enheter.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Del 2: Aktivera tillbakaskrivning av enhet i Azure AD Connect
1. Kör installationsguiden igen. Välj **konfigurera Enhetsalternativ** från ytterligare aktiviteter och klicka på **nästa**. 

    ![Konfigurera enhetsalternativ](./media/active-directory-aadconnect-feature-device-writeback/deviceoptions.png)

    >[!NOTE]
    > De nya konfigurera Enhetsalternativ är bara tillgängliga i version 1.1.819.0 och nyare.

2. På alternativsidan för enheten väljer **konfigurera tillbakaskrivning av enheter**. Alternativet att **inaktivera tillbakaskrivning av enheter** blir inte tillgängliga förrän tillbakaskrivning av enheter som har aktiverats. Klicka på **nästa** att flytta till nästa sida i guiden.
    ![Åtgärden för valt enhet](./media/active-directory-aadconnect-feature-device-writeback/configuredevicewriteback1.png)

3. På sidan tillbakaskrivning visas den angivna domänen som standard enheten tillbakaskrivning skog.
   ![Anpassad installation enheten tillbakaskrivning målskogen](./media/active-directory-aadconnect-feature-device-writeback/writebackforest.png)

4. **Enheten behållaren** ger dig möjligheten att förbereda active directory med hjälp av en av de två alternativen:

    a. **Ange enterprise administratörsautentiseringsuppgifter**: Om autentiseringsuppgifterna som företagsadministratör har angetts för skogen där enheter måste skrivas tillbaka Azure AD Connect ska förbereda skogen automatiskt under konfigurationen av tillbakaskrivning av enheter.

    b. **Ladda ned PowerShell-skript**: Azure AD Connect auto-genererar ett PowerShell-skript som kan förbereda active directory för tillbakaskrivning av enheter. Om autentiseringsuppgifterna som företagsadministratör inte kan anges i Azure AD Connect, rekommenderas att ladda ned PowerShell-skript. Ange det hämta PowerShell-skriptet **CreateDeviceContainer.psq** till företagsadministratör för skogen där enheter kommer att skrivas tillbaka till.
    ![Förbereda active Directory-skog](./media/active-directory-aadconnect-feature-device-writeback/devicecontainercreds.png)
    
    Följande åtgärder utförs för att förbereda active directory-skogen:
    * Om de inte finns redan, skapar och konfigurerar nya behållare och objekt under CN = konfiguration för Enhetsregistrering, CN = Services, CN = Configuration, [skog dn].
    * Om de inte finns redan, skapar och konfigurerar nya behållare och objekt under CN = Registreradeenheter, [domän-dn]. Enhetsobjekt skapas i den här behållaren.
    * Anger behörigheter som krävs för Azure AD Connector-konto att hantera enheter i din Active Directory.
    * Endast måste köras i en skog, även om Azure AD Connect installeras på flera skogar.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Kontrollera att enheter ska synkroniseras med Active Directory
Tillbakaskrivning av enheter bör nu fungerar korrekt. Tänk på att det kan ta upp till tre timmar för enhetsobjekt som ska skrivas tillbaka till AD.  Gör för att kontrollera att dina enheter som har synkroniserats korrekt följande när reglerna synkroniseringen är klar:

1. Starta Active Directory Administrationscenter.
2. Expandera Registreradeenheter inom domänen som är att federerade.

   ![Active Directory Administrationscenter registrerade enheter](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)

3. Aktuella registrerade enheter visas det.

   ![Active Directory Administrationscenter registrerade enheter lista](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Aktivera villkorlig åtkomst
Detaljerade instruktioner för att aktivera det här scenariot finns i [konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="troubleshooting"></a>Felsökning
### <a name="the-writeback-checkbox-is-still-disabled"></a>Kryssrutan tillbakaskrivning är fortfarande inaktiverad
Om kryssrutan för tillbakaskrivning av enheter inte är aktiverad trots att du har följt stegen ovan följande steg hjälper dig att vad installationsguiden verifierar innan den aktiveras.

Första sakerna första:

* Kontrollera att minst en skog som har Windows Server 2012 R2. Typ av enhet objekt måste finnas.
* Om installationsguiden körs redan, kommer alla ändringar inte identifieras. I det här fallet slutföra Installationsguiden och kör det igen.
* Kontrollera att det konto som du anger i skriptet initieringen är faktiskt rätt användare som används av Active Directory-kopplingen. Följ dessa steg om du vill kontrollera detta:
  * Start-menyn, öppna **synkroniseringstjänsten**.
  * Öppna den **kopplingar** fliken.
  * Hitta anslutningen med typen Active Directory Domain Services och markera den.
  * Under **åtgärder**väljer **egenskaper**.
  * Gå till **ansluta till Active Directory-skog**. Kontrollera att namnet på domänen och användarnamnet som angetts på den här skärmen matchar det konto som anges i skriptet.
    ![Connector-kontot i Sync Service Manager](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Kontrollera konfigurationen i Active Directory:

* Kontrollera att registreringstjänsten för enheter finns på plats nedan (CN = DeviceRegistrationService, CN = enheten registreringen Services, CN = konfiguration för Enhetsregistrering, CN = Services, CN = Configuration) enligt konfigurationsnamngivningskontexten.

![Felsöka DeviceRegistrationService i konfigurationen namnområde](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* Kontrollera att det finns endast en konfigurationsobjektet genom att söka configuration-namnområdet. Ta bort Dubbletten om det finns fler än en.

![Felsöka, Sök efter duplicerade objekt](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Kontrollera att attributet msDS-DeviceLocation finns och har ett värde på objektet Registreringstjänst för enheter. Sökning sökvägen och se till att det finns en sådan med objectType msDS-DeviceContainer.

![Felsöka msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Felsöka Registreradeenheter objektklass](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Kontrollera kontot som används av Active Directory-kopplingen har behörighet för behållaren registrerade enheter som föregående steg. Detta är förväntat behörigheterna för den här behållaren:

![Felsöka, kontrollera behörigheter för behållaren](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Verifiera Active Directory-kontot har behörighet på CN = konfiguration för Enhetsregistrering, CN = Services, CN = Configuration-objekt.

![Felsöka, kontrollera behörigheter på konfigurationen för Enhetsregistrering](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Ytterligare information
* [Hantera risker med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

