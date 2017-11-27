---
title: "Azure AD SSPR från Windows 10-inloggningsskärmen | Microsoft Docs"
description: "Konfigurera inloggningsskärmen i Windows 10, Azure AD-lösenordsåterställning och Jag har glömt min PIN-kod"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 0a3fca0c7d36122a09c825a3ed8edf11cc362b8b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>Azure AD-lösenordsåterställning från inloggningsskärmen

Du redan har distribuerat Azure AD-lösenordsåterställning via självbetjäning (SSPR) men dina användare kan fortfarande ringa supportavdelningen när de glömmer lösenordet. De ringer supportavdelningen eftersom de inte kan komma till en webbläsare för att komma åt SSPR.

Med nya Windows 10 Fall Creators Update kan användare med Azure AD-kopplade enheter se länken ”Återställ lösenord” på sin inloggningsskärm. När de klickar på den här länken kommer de till samma lösenordsåterställning via självbetjäning (SSPR) som de är vana vid.

För att användare ska kunna återställa sitt Azure AD-lösenord från Windows 10-inloggningsskärmen måste följande krav vara uppfyllda:

* Windows 10, version 1709, eller senare klient som är [kopplad till Azure AD](device-management-azure-portal.md).
* Återställning av lösenord för självbetjäning i Azure AD måste vara aktiverat.
* Konfigurera och distribuera inställningen för att aktivera länken för återställning av lösenord via någon av följande metoder:
   * [Konfigurationsprofil för Intune-enhet](active-directory-passwords-login.md#configure-reset-password-link-using-intune)
   * [Registernyckel](active-directory-passwords-login.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Konfigurera länken Återställ lösenord med Intune

### <a name="create-a-device-configuration-policy-in-intune"></a>Skapa en princip för enhetskonfiguration i Intune

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Intune**.
2. Skapa en ny profil för enhetskonfiguration genom att gå till **Enhetskonfiguration** > **Profiler** > **Skapa profil**
   * Ge profilen ett beskrivande namn
   * Du kan också ange en beskrivning av profilen
   * Plattform **Windows 10 och senare**
   * Profiltyp **Anpassad**

   ![CreateProfile][CreateProfile]

3. Konfigurera **inställningar**
   * **Lägg till** följande OMA-URI-inställning för att aktivera länken Återställ lösenord
      * Ange ett beskrivande namn som förklarar vad inställningen gör
      * Du kan också ange en beskrivning av inställningen
      * **OMA-URI** inställt på `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **Datatyp** inställt på **Integer**
      * **Värde** inställt på **1**
      * Klicka på **OK**
   * Klicka på **OK**
4. Klicka på **Skapa**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Tilldela en princip för enhetskonfiguration i Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Skapa en grupp om du vill använda enhetskonfigurationsprincip för

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Azure Active Directory**.
2. Gå till **Användare och grupper** > **Alla grupper** > **Ny grupp**
3. Ange ett namn på gruppen. Under **Medlemstyp** väljer du **Tilldelad** 
   * Under **Medlemmar** väljer du de Azure AD-kopplade Windows 10-enheter du vill tillämpa principen på.
   * Klicka på **Välj**
4. Klicka på **Skapa**

Mer information om att skapa grupper finns i artikeln [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Tilldela enhetskonfigurationsprincip till enhetsgrupp

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Intune**.
2. Leta reda på den enhetskonfigurationsprofil du skapade tidigare genom att gå till **Enhetskonfiguration** > **Profiler** > Klicka på profilen du skapade tidigare
3. Tilldela profilen till en grupp med enheter 
   * Klicka på **Tilldelningar** > under **Ta med** > **Välj grupper att ta med**
   * Välj gruppen du skapade tidigare och klicka på **Välj**
   * Klicka på **Spara**

   ![Överlåtelse][Assignment]

Du har nu skapat och tilldelat en enhetskonfigurationsprincip för att aktivera länken Återställ lösenord på inloggningsskärmen med Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Konfigurera länken Återställ lösenord med registret

Vi rekommenderar att du använder den här metoden enbart för att testa ändringen av inställningen.

1. Logga in på enheten som är kopplad till Azure AD med autentiseringsuppgifterna för administratören
2. Kör **regedit** som administratör
3. Ange följande registernyckel
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Vad ser användarna

Vad förändras för användaren när principen är konfigurerad och tilldelad? Hur vet de att de kan återställa sitt lösenord på inloggningsskärmen?

![LoginScreen][LoginScreen]

När användarna försöker logga in ser de nu en länk för återställning av lösenord som öppnar självbetjäningen för återställning av lösenord på inloggningsskärmen. Via den här funktionen kan användarna återställa sina lösenord utan att de behöver använda en annan enhet för att få åtkomst till webbläsaren.

Dina användare får hjälp med att använda funktionen i [Reset your work or school password](active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in) (Återställa ditt arbets- eller skollösenord)

## <a name="common-issues"></a>Vanliga problem

När du testar funktionen med Hyper-V visas inte länken ”Återställ lösenord”.

* Gå till den virtuella dator du använder för att testklicka på **Visa** och avmarkera **Avancerad session**.

När du testar funktionen med Fjärrskrivbord visas inte länken ”Återställ lösenord”

* Återställning av lösenord stöds inte från ett Fjärrskrivbord.

## <a name="next-steps"></a>Nästa steg
Följande länkar ger ytterligare information om lösenordsåterställning med Azure AD

* [Hur gör jag för att distribuera SSPR?](active-directory-passwords-best-practices.md)
* [Hur gör jag för att aktivera PIN-kodåterställning från inloggningsskärmen?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [Mer information om principer för MDM-autentisering](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/active-directory-passwords-login/create-profile.png "Skapa en Intune-enhetskonfigurationsprofil för att aktivera länken Återställ lösenord på Windows 10-inloggningsskärmen"
[Assignment]: ./media/active-directory-passwords-login/profile-assignment.png "Tilldela principer för Intune-enhetskonfiguration till en grupp med Windows 10-enheter"
[LoginScreen]: ./media/active-directory-passwords-login/logon-reset-password.png "Länken Återställ lösenord på Windows 10-inloggningsskärmen"
