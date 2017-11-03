---
title: "Azure Active Directory-enhetshantering vanliga frågor och svar | Microsoft Docs"
description: "Azure Active Directory enhetshantering vanliga frågor och svar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3b37033e0c96e01c73bef15af1b85116f404c354
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory-enhetshantering vanliga frågor och svar



**F: hur registrera en macOS enhet?**

**S:** registrerar macOS enhet:

1.  [Skapa en efterlevnadsprincip](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definiera en princip för villkorlig åtkomst för macOS enheter](active-directory-conditional-access-azure-portal.md) 

**Anmärkning:**

- Användare som ingår i din princip för villkorlig åtkomst måste en [version av Office som stöds för macOS](active-directory-conditional-access-technical-reference.md#client-apps-condition) att komma åt resurser. 

- Under det första anslutningsförsöket uppmanas användarna att registrera enheten med hjälp av Företagsportalen.

---

**F: jag registrerade nyligen enheten. Varför ser inte enheten under Mina användarinformation i Azure portal?**

**S:** Windows 10-enheter som är ansluten till domänen med automatisk enhetsregistrering visas inte under användarinformation.
Du måste använda PowerShell för att se alla enheter. 

Följande enheter visas under informationen som användaren:

- Alla personliga enheter som inte är ansluten enterprise 
- Alla icke - Windows 10 / Windows Server 2016 
- Alla Windows-enheter 

---

**F: Varför kan jag inte se alla enheter som har registrerats i Azure Active Directory i Azure portal?** 

**S:** för närvarande, det går inte att visa alla registrerade enheter i Azure-portalen. Du kan använda Azure PowerShell för att hitta alla enheter. Mer information finns i [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet.

--- 

**F: hur vet jag Enhetsstatus för registrering av klienten är?**

**S:** registreringstillstånd enhet beror på:

- Vad enheten är
- Hur den har registrerats 
- Alla information som rör den. 
 

---

**F: Varför är en enhet som jag har tagits bort i Azure-portalen eller med Windows PowerShell fortfarande listad som har registrerats?**

**S:** detta är avsiktligt. Enheten har inte åtkomst till resurser i molnet. Om du vill ta bort enheten och registrera igen måste vara en manuell åtgärd som ska vidtas på enheten. 

För Windows 10 och Windows Server 2016 som är lokala AD-domänansluten:

1.  Öppna Kommandotolken som administratör.

2.  Typ`dsregcmd.exe /debug /leave`

3.  Logga ut och logga in att utlösa den schemalagda aktiviteten som registrerar enheten igen. 

För andra Windows-plattformar som är lokala AD-domänansluten:

1.  Öppna Kommandotolken som administratör.
2.  Skriv `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Skriv `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**F: Varför visas dubblettenhet poster i Azure-portalen?**

**S:**

-   För Windows 10 och Windows Server 2016, om de är upprepade försök att koppla från och ansluta igen samma enhet, kan det finnas dubbla poster. 

-   Om du har använt Lägg till arbets- eller Skolkonto, skapas en ny enhetspost med samma enhetsnamn varje windowsanvändare som använder Lägg till arbets- eller Skolkonto.

-   Andra Windows-plattformar som är lokala AD som ingår i domänen med hjälp av automatisk registrering skapar en ny enhetspost med samma enhetsnamn för varje domänanvändare som loggar in på enheten. 

-   En AADJ-dator som har rensats, installeras igen och ansluten igen med samma namn kommer att visas som en annan post med samma enhetsnamn.

---

**F: Varför kan en användare fortfarande komma åt resurser från en enhet som jag har inaktiverats i Azure portal?**

**S:** det kan ta upp till en timme innan en återkalla som ska användas.

>[!Note] 
>För förlorade enheter rekommenderar vi att rensa enheten så att användare inte tillgång till enheten. Mer information finns i [registrera enheter för hantering i Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**F: Varför Mina användare ser inte ”du kan hämta det här”?**

**S:** om du har konfigurerat vissa regler för villkorlig åtkomst för att kräva tillstånd för en specifik enhet och enheten inte uppfyller kriterierna som användare blockeras och detta meddelande. Utvärdera reglerna och se till att enheten kan uppfyller kriterierna för att undvika det här meddelandet.

---


**F: jag finns enheten under användarinformation i Azure-portalen och kan se status som har registrerats på klienten. Kan jag in korrekt för att använda villkorlig åtkomst?**

**S:** enhetspost (deviceID) och tillståndet på Azure-portalen måste matcha klienten och utvärdering kriterier för villkorlig åtkomst. Mer information finns i [Kom igång med Azure Active Directory Device Registration](active-directory-device-registration.md).

---

**F: Varför visas meddelandet ”användarnamnet eller lösenordet är felaktigt” för en enhet som jag bara har anslutit till Azure AD?**

**S:** vanliga orsaker till det här scenariot är:

- Användarens autentiseringsuppgifter är inte längre giltig.

- Datorn kan inte kommunicera med Azure Active Directory. Sök efter eventuella problem med nätverksanslutningen.

- Azure AD Join kraven har inte uppfyllts. Se till att du har följt stegen för att [utöka molnfunktioner till Windows 10-enheter via Azure Active Directory Join](active-directory-azureadjoin-overview.md).  

- Federerad inloggningar kräver federationsservern för att stödja en aktiv WS-Trust-slutpunkt. 

---

**F: Varför visas den ”Oops... Det uppstod ett fel”! dialogrutan när jag försöker Anslut min dator?**

**S:** detta beror på hur du konfigurerar Azure Active Directory-registrering med Intune. Mer information finns i [konfigurera enhetshantering för Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**F: varför min försök att ansluta en dator inte även om jag inte får någon information om fel?**

**S:** en trolig orsak är att användaren är inloggad på enheten med det inbyggda administratörskontot. Skapa ett annat lokalt konto innan du använder Azure Active Directory Join för att slutföra installationen. 

---

**F: var kan jag hitta instruktioner för installationen av automatisk enhetsregistrering?**

**S:** detaljerade instruktioner finns [hur du konfigurerar automatisk registrering av Windows-domänanslutna enheter med Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**F: var kan jag hitta felsökning information om automatisk enhetsregistrering?**

**S:** information om felsökning, se:

- [Felsökning av automatisk registrering av domän domänanslutna datorer till Azure AD – Windows 10 och Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Felsökning av automatisk registrering av domän domänanslutna datorer till Azure AD för äldre Windows-klienter](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

