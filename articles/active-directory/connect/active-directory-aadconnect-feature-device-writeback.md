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
ms.openlocfilehash: 4ee3d424d5a2f58a87cd83ca3d6e9b131dc0383c
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056322"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Aktivera tillbakaskrivning av enheter
> [!NOTE]
> En prenumeration på Azure AD Premium krävs för tillbakaskrivning av enhet.
> 
> 

Följande dokumentation innehåller information om hur du aktiverar funktionen för tillbakaskrivning av enhet i Azure AD Connect. Tillbakaskrivning av enheter används i följande scenarier:

* Aktivera villkorlig åtkomst baserat på enheter för att AD FS (2012 R2 eller senare) skyddade applikationer (förtroenden för förlitande part).

Detta ger ytterligare säkerhet och kontroll som ges åtkomst till program bara till betrodda enheter. Mer information om villkorlig åtkomst finns i [hantera risker med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) och [konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../active-directory-conditional-access-automatic-device-registration-setup.md).

> [!IMPORTANT]
> <li>Enheter måste finnas i samma skog som användare. Eftersom enheter måste skrivas tillbaka till en enda skog, stöder den här funktionen för närvarande inte en distribution med flera skogar för användaren.</li>
> <li>Konfigurationsobjekt för endast en enhet registreringen kan läggas till en lokal Active Directory-skog. Den här funktionen är inte kompatibel med en topologi där den lokala Active Directory synkroniseras till flera Azure AD-kataloger.</li>

## <a name="part-1-install-azure-ad-connect"></a>Del 1: Installera Azure AD Connect
Installera Azure AD Connect med anpassade eller standardinställningar. Microsoft rekommenderar att börja med alla användare och grupper har synkroniserats innan du aktiverar tillbakaskrivning av enhet.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Del 2: Aktivera tillbakaskrivning av enheter i Azure AD Connect
1. Kör installationsguiden igen. Välj **konfigurera Enhetsalternativ** från ytterligare uppgifter och klickar på **nästa**. 

    ![Konfigurera enhetsalternativ](./media/active-directory-aadconnect-feature-device-writeback/deviceoptions.png)

    >[!NOTE]
    > De nya konfigurera Enhetsalternativ är endast tillgängliga i version 1.1.819.0 och nyare.

2. På alternativsidan för enheten väljer **konfigurera tillbakaskrivning av enhet**. Alternativet att **inaktivera tillbakaskrivning av enhet** blir inte tillgängliga förrän tillbakaskrivning av enhet har aktiverats. Klicka på **nästa** att flytta till nästa sida i guiden.
    ![Välja enheten igen](./media/active-directory-aadconnect-feature-device-writeback/configuredevicewriteback1.png)

3. På sidan tillbakaskrivning av visas den angivna domänen som tillbakaskrivningsskog för standard-enhet.
   ![Anpassad installation enheten tillbakaskrivning av målskogen](./media/active-directory-aadconnect-feature-device-writeback/writebackforest.png)

4. **Enhetsbehållare** sidan innehåller möjligheten att förbereda active directory med hjälp av något av två tillgängliga alternativ:

    a. **Ange enterprise administratörsautentiseringsuppgifter**: om administratörsautentiseringsuppgifterna har angetts för skogen där enheter måste skrivas tillbaka, Azure AD Connect kommer förbereda skogen automatiskt under konfigurationen av tillbakaskrivning av enhet.

    b. **Hämta PowerShell-skript**: Azure AD Connect auto-genererar ett PowerShell-skript som kan förbereda active directory för tillbakaskrivning av enhet. Om autentiseringsuppgifterna som företagsadministratör inte kan tillhandahållas i Azure AD Connect, rekommenderas att ladda ned PowerShell-skriptet. Ange det hämta PowerShell-skriptet **CreateDeviceContainer.psq** till skogen där enheter ska skrivas tillbaka till enterprise-administratören.
    ![Förbereda active Directory-skog](./media/active-directory-aadconnect-feature-device-writeback/devicecontainercreds.png)
    
    Följande åtgärder utförs för att förbereda active directory-skogen:
    * Om de inte redan finns redan, skapar och konfigurerar nya behållare och objekt under CN = enhet registrering Configuration, CN = Services, CN = Configuration, [skog-dn].
    * Om de inte redan finns redan, skapar och konfigurerar nya behållare och objekt under CN = RegisteredDevices, [domän-dn]. Enhetsobjekt skapas i den här behållaren.
    * Anger behörigheter som krävs för Azure AD Connector-konto att hantera enheter i din Active Directory.
    * Behöver bara köras på en skog, även om Azure AD Connect installeras på flera skogar.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Kontrollera enheterna synkroniseras till Active Directory
Tillbakaskrivning av enhet bör nu fungerar korrekt. Tänk på att det kan ta upp till 3 timmar innan enhetsobjekt som ska skrivas tillbaka till AD.  Kontrollera att dina enheter som har synkroniserats korrekt genom du göra följande när sync-regler har slutfört:

1. Starta Active Directory Administrationscenter.
2. Expandera RegisteredDevices i domänen som är att federerad.

   ![Active Directory Administrationscenter-registrerade enheter](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)

3. Befintliga registrerade enheter visas det.

   ![Active Directory Administrationscenter registrerad enhetslistan](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Aktivera villkorlig åtkomst
Detaljerade anvisningar för att aktivera det här scenariot är tillgängliga i [konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="troubleshooting"></a>Felsökning
### <a name="the-writeback-checkbox-is-still-disabled"></a>Tillbakaskrivning av kryssrutan är fortfarande inaktiverad
Om kryssrutan för tillbakaskrivning av enhet inte aktiveras även om du har följt stegen ovan, följande steg vägleder dig genom vad installationsguiden verifierar innan rutan är aktiverat.

Första sakerna första:

* Kontrollera att minst en skog har Windows Server 2012 R2. Typ av enhet objekt måste finnas.
* Om installationsguiden körs redan, kommer alla ändringar inte identifieras. I det här fallet slutföra Installationsguiden och kör det igen.
* Kontrollera att det konto som du anger i initieringsskriptet är faktiskt rätt användare används av Active Directory-koppling. Följ dessa steg om du vill kontrollera detta:
  * Start-menyn Öppna **synkroniseringstjänsten**.
  * Öppna den **kopplingar** fliken.
  * Hitta kopplingen med Active Directory Domain Services och markera den.
  * Under **åtgärder**väljer **egenskaper**.
  * Gå till **ansluta till Active Directory-skog**. Kontrollera att namnet på domänen och användarnamnet som angetts på den här skärmen matchar det konto som anges i skriptet.
    ![Anslutningskontot i synkronisering av Service Manager](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Kontrollera konfigurationen i Active Directory:

* Kontrollera att registreringstjänsten för enheter finns på plats nedan (CN = DeviceRegistrationService, CN = enhet registrering Services, CN = enhet registrering Configuration, CN = Services, CN = Configuration) under konfigurationsnamngivningen.

![Felsöka DeviceRegistrationService i konfigurationen namnområde](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

* Kontrollera att det finns endast en konfigurationsobjektet genom att söka i namnområdet konfiguration. Ta bort Dubbletten om det finns fler än en.

![Felsöka genom att söka efter duplicerade objekt](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

* Kontrollera att attributet msDS-DeviceLocation finns och har ett värde på Device Registration Service-objektet. Lookup den här platsen och kontrollera att den finns med objectType msDS-DeviceContainer.

![Felsöka msDS-DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Felsöka RegisteredDevices objektklass](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

* Kontrollera att det konto som används av Active Directory-anslutningen har behörigheter som krävs för behållaren för registrerade enheter som upptäckts av föregående steg. Det här är de förväntade behörigheterna på den här behållaren:

![Felsöka genom att kontrollera behörigheter för behållare](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

* Verifiera Active Directory-kontot har behörighet på CN = enhet registrering Configuration, CN = Services, CN = Configuration-objekt.

![Felsöka genom att kontrollera behörigheter på konfigurationen för Enhetsregistrering](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Ytterligare information
* [Hantera risker med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).

