---
title: 'Azure AD Connect: Aktivera tillbakaskrivning av enheter | Microsoft Docs'
description: "Det här dokumentet beskriver hur du aktiverar tillbakaskrivning av enheter med Azure AD Connect"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: ac5c39eac34221992803f3b27db241263f828b36
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
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
1. Installera Azure AD Connect med anpassade eller standardinställningar. Microsoft rekommenderar att börja med alla användare och grupper har synkroniserats innan du aktiverar tillbakaskrivning av enheter.

## <a name="part-2-prepare-active-directory"></a>Del 2: Förbereda Active Directory
Använd följande steg för att förbereda för att använda tillbakaskrivning av enheter.

1. Starta PowerShell från den dator där Azure AD Connect är installerat i upphöjt läge.
2. Om Active Directory PowerShell-modulen inte är installerad, installerar du den Remote Server Administration Tools som innehåller AD PowerShell-modulen och dsacls.exe som krävs för att köra skriptet.  Kör följande kommando:
  
   ``` powershell
   Add-WindowsFeature RSAT-AD-Tools
   ```

3. Om Azure Active Directory PowerShell-modulen inte är installerat kan du hämta och installera den från [Azure Active Directory-modulen för Windows PowerShell (64-bitars version)](http://go.microsoft.com/fwlink/p/?linkid=236297). Den här komponenten har ett beroende på inloggningsassistenten, som installeras med Azure AD Connect.  
4. Kör följande kommandon och avsluta sedan PowerShell med enterprise-administratörsautentiseringsuppgifter.
   
   ``` powershell
   Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'
   ```

   ``` powershell
   Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}
   ```

Enterprise-administratörsautentiseringsuppgifter krävs eftersom namnområdet konfiguration behövs. En domänadministratör har inte tillräcklig behörighet.

![PowerShell för att aktivera tillbakaskrivning av enheter](./media/active-directory-aadconnect-feature-device-writeback/powershell.png) d


Beskrivning:

* Om de inte finns redan, skapar och konfigurerar nya behållare och objekt under CN = konfiguration för Enhetsregistrering, CN = Services, CN = Configuration, [skog dn].
* Om de inte finns redan, skapar och konfigurerar nya behållare och objekt under CN = Registreradeenheter, [domän-dn]. Enhetsobjekt skapas i den här behållaren.
* Anger behörigheter som krävs för Azure AD Connector-konto att hantera enheter i din Active Directory.
* Endast måste köras i en skog, även om Azure AD Connect installeras på flera skogar.

Parametrar:

* Domännamn: Active Directory-domän där enhetsobjekt kommer att skapas. Obs: Alla enheter för en viss Active Directory-skog skapas i en domän.
* AdConnectorAccount: Active Directory-konto som ska användas av Azure AD Connect för att hantera objekt i katalogen. Detta är det konto som används av Azure AD Connect-synkronisering för att ansluta till AD. Om du har installerat med standardinställningar är det konto som föregås av MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Del 3: Aktivera tillbakaskrivning av enhet i Azure AD Connect
Använd följande procedur för att aktivera tillbakaskrivning av enheter i Azure AD Connect.

1. Kör installationsguiden igen. Välj **anpassa synkroniseringsalternativ** från ytterligare aktiviteter och klicka på **nästa**.
   ![Anpassad installation anpassa synkroniseringsalternativ](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2. På sidan valfria funktioner är tillbakaskrivning av enheter inte längre nedtonade. Du ska Observera att om Azure AD Connect prep steg inte är slutförda tillbakaskrivning vara nedtonade ut på sidan valfria funktioner. Markera kryssrutan för tillbakaskrivning av enheter och på **nästa**. Om kryssrutan är fortfarande inaktiverad, finns det [avsnittet om felsökning](#the-writeback-checkbox-is-still-disabled).
   ![Anpassad installation valfria funktioner för tillbakaskrivning av enhet.](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3. På sidan tillbakaskrivning visas den angivna domänen som standard enheten tillbakaskrivning skog.
   ![Anpassad installation enheten tillbakaskrivning målskogen](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4. Slutför installationen av guiden utan ytterligare konfigurationsändringar. Om det behövs, referera till [anpassad installation av Azure AD Connect.](active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Aktivera villkorlig åtkomst
Detaljerade instruktioner för att aktivera det här scenariot finns i [konfigurera lokal villkorlig åtkomst med hjälp av Azure Active Directory Device Registration](../active-directory-conditional-access-automatic-device-registration-setup.md).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Kontrollera att enheter ska synkroniseras med Active Directory
Tillbakaskrivning av enheter bör nu fungerar korrekt. Tänk på att det kan ta upp till tre timmar för enhetsobjekt som ska skrivas tillbaka till AD.  Gör för att kontrollera att dina enheter som har synkroniserats korrekt följande när reglerna synkroniseringen är klar:

1. Starta Active Directory Administrationscenter.
2. Expandera Registreradeenheter inom domänen som är att federerade.
   ![Active Directory Administrationscenter registrerade enheter](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3. Aktuella registrerade enheter visas det.
   ![Active Directory Administrationscenter registrerade enheter lista](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

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

