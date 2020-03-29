---
title: 'Azure AD Connect: Aktivera tillbakaskrivning av enheter | Microsoft-dokument'
description: I det här dokumentet beskrivs hur du aktiverar tillbakaskrivning av enheter med Azure AD Connect
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
ms.topic: conceptual
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 632f6f80184c6ba3409bd30ae070cbaefc77f036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109503"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Aktivera tillbakaskrivning av enheter
> [!NOTE]
> En prenumeration på Azure AD Premium krävs för tillbakaskrivning av enheter.
> 
> 

Följande dokumentation innehåller information om hur du aktiverar funktionen för tillbakaskrivning av enheter i Azure AD Connect. Enhetsåterskrivning används i följande scenarier:

* Aktivera [Windows Hello för företag med hjälp av distribution av hybridcertifikatförtroende](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Aktivera villkorlig åtkomst baserat på enheter till ADFS (2012 R2 eller högre) skyddade program (förlitande part förtroenden).

Detta ger ytterligare säkerhet och säkerhet om att åtkomst till program endast beviljas till betrodda enheter. Mer information om villkorlig åtkomst finns i [Hantera risker med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) och konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device [Registration](../../active-directory/active-directory-device-registration-on-premises-setup.md).

> [!IMPORTANT]
> <li>Enheter måste finnas i samma skog som användarna. Eftersom enheter måste skrivas tillbaka till en enda skog stöder den här funktionen för närvarande inte en distribution med flera användarskogar.</li>
> <li>Endast ett konfigurationsobjekt för enhetsregistrering kan läggas till i den lokala Active Directory-skogen. Den här funktionen är inte kompatibel med en topologi där den lokala Active Directory synkroniseras med flera Azure AD-kataloger.</li>

## <a name="part-1-install-azure-ad-connect"></a>Del 1: Installera Azure AD Connect
Installera Azure AD Connect med anpassade inställningar eller expressinställningar. Microsoft rekommenderar att du börjar med alla användare och grupper som har synkroniserats innan du aktiverar tillbakaskrivning av enheter.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Del 2: Aktivera tillbakaskrivning av enheter i Azure AD Connect
1. Kör installationsguiden igen. Välj **Konfigurera enhetsalternativ** på sidan Ytterligare uppgifter och klicka på **Nästa**. 

    ![Konfigurera enhetsalternativ](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > De nya konfigurera enhetsalternativen är endast tillgängliga i version 1.1.819.0 och nyare.

2. På sidan enhetsalternativ väljer du **Konfigurera tillbakaskrivning av enheten**. Alternativet att **inaktivera tillbakaskrivning av enheten** är inte tillgängligt förrän tillbakaskrivningen av enheten är aktiverad. Klicka på **Nästa** för att gå till nästa sida i guiden.
    ![Välj enhetsdrift](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. På tillbakaskrivningssidan ser du den angivna domänen som standardskogen för enhetsrestitution.
   ![Anpassad återställning av enhetens tillbakaskrivningsmålskog](./media/how-to-connect-device-writeback/writebackforest.png)

4. **Enhetsbehållare** sidan ger möjlighet att förbereda active directory med hjälp av något av de två tillgängliga alternativen:

    a. **Ange autentiseringsuppgifter för företagsadministratören**: Om företagsadministratörsautentiseringsuppgifterna tillhandahålls för skogen där enheter måste skrivas tillbaka, förbereder Azure AD Connect skogen automatiskt under konfigurationen av enhetens tillbakaskrivning.

    b. **Ladda ned PowerShell-skript:** Azure AD Connect genererar automatiskt ett PowerShell-skript som kan förbereda active directory för tillbakaskrivning av enheter. Om företagsadministratörsautentiseringsuppgifterna inte kan tillhandahållas i Azure AD Connect föreslås att PowerShell-skriptet hämtas. Ange det nedladdade PowerShell-skriptet **CreateDeviceContainer.psq** till företagsadministratören i skogen där enheter skrivs tillbaka till.
    ![Förbereda active directory-skog](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Följande åtgärder utförs för att förbereda active directory-skogen:
    * Om de inte redan finns skapar och konfigurerar och konfigurerar nya behållare och objekt under CN=Enhetsregistreringskonfiguration,CN=Services,CN=Configuration,[skog-dn].
    * Om de inte redan finns skapar och konfigurerar nya behållare och objekt under CN=RegisteredDevices,[domän-dn]. Enhetsobjekt skapas i den här behållaren.
    * Anger nödvändiga behörigheter för Azure AD Connector-kontot för att hantera enheter i Active Directory.
    * Behöver bara köras på en skog, även om Azure AD Connect installeras på flera skogar.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verifiera att enheter synkroniseras med Active Directory
Enhetens tillbakaskrivning ska nu fungera som den ska. Tänk på att det kan ta upp till 3 timmar innan enhetsobjekt skrivs tillbaka till AD.  Kontrollera att dina enheter synkroniseras korrekt genom att göra följande när synkroniseringsreglerna har slutförts:

1. Starta Active Directory Administrationscenter.
2. Expandera RegisteredDevices, inom domänen som matas.

   ![Registrerade enheter i Active Directory Admin Center](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Aktuella registrerade enheter kommer att listas där.

   ![Lista över registrerade enheter i Active Directory Admin Center](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Aktivera villkorlig åtkomst
Detaljerade instruktioner för att aktivera det här scenariot är tillgängliga i [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../../active-directory/active-directory-device-registration-on-premises-setup.md).

## <a name="troubleshooting"></a>Felsökning
### <a name="the-writeback-checkbox-is-still-disabled"></a>Kryssrutan för tillbakaskrivning är fortfarande inaktiverad
Om kryssrutan för enhetsreskrivning inte är aktiverad även om du har följt stegen ovan, kommer följande steg att guida dig genom vad installationsguiden verifierar innan rutan är aktiverad.

Första saker först:

* Skogen där enheterna finns måste ha skogsschemat uppgraderat till Windows 2012 R2-nivå så att enhetsobjektet och associerade attribut finns .
* Om installationsguiden redan körs identifieras inga ändringar. I så fall slutför du installationsguiden och kör den igen.
* Kontrollera att kontot som du anger i initieringsskriptet faktiskt är rätt användare som används av Active Directory Connector. Så här verifierar du detta:
  * Öppna **Synkroniseringstjänsten**på Start-menyn .
  * Öppna fliken **Kontakter.**
  * Leta reda på kopplingen med typen Active Directory Domain Services och välj den.
  * Välj **Egenskaper**under **Åtgärder**.
  * Gå till **Anslut till Active Directory Forest**. Kontrollera att domänen och användarnamnet som anges på den här skärmen matchar kontot som anges i skriptet.
    ![Anslutningskonto i Synkroniseringstjänsthanteraren](./media/how-to-connect-device-writeback/connectoraccount.png)

Verifiera konfigurationen i Active Directory:

* Kontrollera att enhetsregistreringstjänsten finns på platsen nedan (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) under konfigurationsnamnningskontexten.

![Felsöka, DeviceRegistrationService i konfigurationsnamnområdet](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Kontrollera att det bara finns ett konfigurationsobjekt genom att söka i konfigurationsnamnområdet. Om det finns fler än en tar du bort dubbletten.

![Felsöka, sök efter dubblettobjekt](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Kontrollera att attributet msDS-DeviceLocation finns och har ett värde på objektet Enhetsregistreringstjänst. Slå upp den här platsen och se till att den finns med objectType msDS-DeviceContainer.

![Felsöka, msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Felsöka, Objektklassen RegisteredDevices](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Kontrollera att kontot som används av Active Directory Connector har behörigheter för registrerade enheter som hittades i föregående steg. Det här är de förväntade behörigheterna för den här behållaren:

![Felsöka, verifiera behörigheter för behållare](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Kontrollera att Active Directory-kontot har behörigheter för CN=Enhetsregistreringskonfiguration,CN=Services,CN=Configuration-objektet.

![Felsöka, verifiera behörigheter för konfiguration av enhetsregistrering](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Ytterligare information
* [Hantera risker med villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)
* [Konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory-enhetsregistrering](../../active-directory/active-directory-device-registration-on-premises-setup.md)

## <a name="next-steps"></a>Nästa steg
Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).

