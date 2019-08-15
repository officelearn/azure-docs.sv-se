---
title: Hantera enheter med Azure Portal | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att hantera enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03c9f0908d8b5290dc4585a330a7ea78a6577ab9
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68942957"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Hantera enhets identiteter med hjälp av Azure Portal

Med hantering av enhets identitet i Azure Active Directory (Azure AD) kan du se till att användarna får åtkomst till dina resurser från enheter som uppfyller dina standarder för säkerhet och efterlevnad.

Den här artikeln:

- Förutsätter att du är bekant med [introduktionen till enhets identitets hantering i Azure Active Directory](overview.md)
- Ger information om hur du hanterar enhets identiteter med Azure AD Portal

## <a name="manage-device-identities"></a>Hantera enhetsidentiteter

Azure AD-portalen ger dig en central plats för att hantera dina enhets identiteter. Du kan komma åt den här platsen genom att antingen använda en [direkt länk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) eller genom att följa dessa manuella steg:

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Klicka på **Active Directory**i det vänstra navigerings fältet.

   ![Konfigurera enhetsinställningar](./media/device-management-azure-portal/01.png)

3. I avsnittet **Hantera** klickar du på **enheter**.

   ![Konfigurera enhetsinställningar](./media/device-management-azure-portal/74.png)

På sidan **enheter** kan du:

- Konfigurera enhets inställningar
- Hitta enheter
- Utföra hanterings uppgifter för enhets identitet
- Granska enhets relaterade gransknings loggar  
  
## <a name="configure-device-settings"></a>Konfigurera enhetsinställningar

Om du vill hantera enhets identiteter med Azure AD-portalen måste enheterna antingen vara [registrerade eller anslutna](overview.md) till Azure AD. Som administratör kan du finjustera processen för att registrera och ansluta enheter genom att konfigurera enhets inställningarna.

![Konfigurera enhetsinställningar](./media/device-management-azure-portal/22.png)

På sidan enhets inställningar kan du konfigurera:

![Hantera en Intune-enhet](./media/device-management-azure-portal/21.png)

- **Användare kan ansluta enheter till Azure AD** – med den här inställningen kan du välja vilka användare som kan registrera sina enheter som Azure AD-anslutna enheter. Standardvärdet är **alla**.

> [!NOTE]
> **Användare kan ansluta enheter till Azure AD** -inställningen gäller bara för Azure AD Join i Windows 10.

- **Ytterligare lokala administratörer på Azure AD-anslutna enheter** – du kan välja de användare som har behörighet till lokal administratör på en enhet. Användare som läggs till här läggs till i rollen *enhets administratörer* i Azure AD. Globala administratörer i Azure AD och enhets ägare beviljas lokal administratörs behörighet som standard. Det här alternativet är en Premium Edition-funktion som är tillgänglig via produkter som Azure AD Premium eller Enterprise Mobility Suite (EMS).
- **Användare kan registrera sina enheter med Azure AD** – du måste konfigurera den här inställningen så att Windows 10 personal-, iOS-, Android-och macOs-enheter kan registreras med Azure AD. Om du väljer **ingen**, tillåts inte enheter att registrera med Azure AD. Registrering med Microsoft Intune eller hantering av mobila enheter (MDM) för Office 365 kräver registrering. Om du har konfigurerat någon av dessa tjänster är **alla** markerad och **ingen** är tillgänglig.
- **Kräv Multi-factor auth för att ansluta enheter** – du kan välja om användarna måste ange ytterligare en autentiseringsnivå för att ansluta sina enheter till Azure AD. Standardvärdet är **Nej**. Vi rekommenderar att du kräver Multi-Factor Authentication när du registrerar en enhet. Innan du aktiverar Multi-Factor Authentication för den här tjänsten måste du se till att Multi-Factor Authentication har kon figurer ATS för de användare som registrerar sina enheter. Mer information om olika Azure Multi-Factor Authentication-tjänster finns i [komma igång med Azure Multi-](../authentication/concept-mfa-whichversion.md)Factor Authentication. 

> [!NOTE]
> Inställningen **Kräv Multi-factor auth för att ansluta enheter** gäller inte för Hybrid Azure AD-anslutna enheter.

- **Maximalt antal enheter** – med den här inställningen kan du välja det maximala antalet enheter som en användare kan ha i Azure AD. Om en användare når den här kvoten kan de inte lägga till fler enheter förrän en eller flera av de befintliga enheterna tas bort. Enhets kvoten räknas för alla enheter som antingen är Azure AD-anslutna eller Azure AD registrerad idag. Standardvärdet är **20**.

> [!NOTE]
> Inställningen för **maximalt antal enheter** gäller inte för Hybrid Azure AD-anslutna enheter.

- **Användare kan synkronisera inställningar och AppData över enheter** – som standard är den här inställningen inställd på **ingen**. Genom att välja vissa användare eller grupper eller alla kan användarens inställningar och AppData synkroniseras över sina Windows 10-enheter. Läs mer om hur synkronisering fungerar i Windows 10.
Det här alternativet är en Premium funktion som är tillgänglig via produkter som Azure AD Premium eller Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Hitta enheter

Det finns två alternativ för att hitta registrerade och anslutna enheter:

- **Alla enheter** i avsnittet **Hantera** på sidan **enheter**  

   ![Alla enheter](./media/device-management-azure-portal/41.png)

- **Enheter** i avsnittet **Hantera** på en **användar** sida

   ![Alla enheter](./media/device-management-azure-portal/43.png)

Med båda alternativen kan du komma till en vy som:

- Gör att du kan söka efter enheter med hjälp av visnings namnet eller enhets-ID: t som filter.
- Ger detaljerad översikt över registrerade och anslutna enheter
- Gör att du kan utföra vanliga enhets hanterings uppgifter

![Alla enheter](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Om du ser en enhet som är "hybrid Azure AD-ansluten" med ett tillstånd "väntar" i kolumnen registrerad, anger den att enheten har synkroniserats från Azure AD Connect och väntar på att slutföra registreringen från klienten. Läs mer om hur du [planerar din hybrid Azure AD Join-implementering](hybrid-azuread-join-plan.md). Ytterligare information finns i artikeln, [vanliga frågor och svar om enheter](faq.md).
>
>   ![Väntande enheter](./media/device-management-azure-portal/75.png)
>
>* För vissa iOS-enheter kan enhets namn som innehåller apostrofer eventuellt använda olika tecken som ser ut som apostrofer. Därför är det lite knepigt att söka efter sådana enheter – om du inte ser Sök resultaten korrekt kontrollerar du att Sök strängen innehåller matchande apostrof-tecken.

## <a name="device-identity-management-tasks"></a>Hanterings uppgifter för enhets identitet

Som global administratör eller moln enhets administratör kan du hantera de registrerade eller anslutna enheterna. Intune-tjänst administratörer kan:

- Uppdatera enheter – exempel är dagliga åtgärder som att aktivera/inaktivera enheter
- Ta bort enheter – när en enhet dras tillbaka och ska tas bort i Azure AD

Det här avsnittet innehåller information om vanliga hanterings uppgifter för enhets identitet.

### <a name="manage-an-intune-device"></a>Hantera en Intune-enhet

Om du är Intune-administratör kan du hantera enheter som marker ATS som **Microsoft Intune**. Om enheten inte har registrerats med Microsoft Intune alternativet "hantera" bli grå.

![Hantera en Intune-enhet](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Aktivera/inaktivera en Azure AD-enhet

Om du vill aktivera/inaktivera en enhet har du två alternativ:

- Menyn uppgifter ("...") på sidan **alla enheter**

   ![Hantera en Intune-enhet](./media/device-management-azure-portal/71.png)

- Verktygsfältet på sidan **enheter**

   ![Hantera en Intune-enhet](./media/device-management-azure-portal/32.png)

**!**

- Du måste vara global administratör eller en moln enhets administratör i Azure AD för att aktivera/inaktivera en enhet. 
- Inaktive ring av en enhet förhindrar att en enhet autentiseras med Azure AD, vilket hindrar enheten från att komma åt dina Azure AD-resurser som skyddas av enhets certifikat utfärdare eller med dina WH4B-autentiseringsuppgifter.

### <a name="delete-an-azure-ad-device"></a>Ta bort en Azure AD-enhet

Om du vill ta bort en enhet har du två alternativ:

- Menyn uppgifter ("...") på sidan **alla enheter**

   ![Hantera en Intune-enhet](./media/device-management-azure-portal/72.png)

- Verktygsfältet på sidan **enheter**

   ![Ta bort en enhet](./media/device-management-azure-portal/34.png)

**!**

- Du måste vara global administratör eller en Intune-administratör i Azure AD för att kunna ta bort en enhet.
- Ta bort en enhet:
   - Förhindrar att en enhet får åtkomst till dina Azure AD-resurser.
   - Tar bort alla uppgifter som är kopplade till enheten, till exempel BitLocker-nycklar för Windows-enheter.  
   - Representerar en icke-återställnings bar aktivitet och rekommenderas inte om det inte krävs.

Om en enhet hanteras av en annan hanterings auktoritet (till exempel Microsoft Intune) ser du till att enheten har rensats/dragits tillbaka innan enheten tas bort i Azure AD. Granska hur du [hanterar inaktuella enheter](device-management-azure-portal.md) innan du tar bort några enheter.

### <a name="view-or-copy-device-id"></a>Visa eller kopiera enhets-ID

Du kan använda ett enhets-ID för att kontrol lera enhetens ID-information på enheten eller använda PowerShell under fel sökning. Klicka på enheten för att komma åt kopierings alternativet.

![Visa ett enhets-ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Visa eller kopiera BitLocker-nycklar

Du kan visa och kopiera BitLocker-nycklar för att hjälpa användarna att återställa sina krypterade enheter. Dessa nycklar är bara tillgängliga för Windows-enheter som är krypterade och har sina nycklar lagrade i Azure AD. Du kan kopiera nycklarna när du använder information om enheten.

![Visa BitLocker-nycklar](./media/device-management-azure-portal/36.png)

Om du vill visa eller kopiera BitLocker-nycklarna måste du antingen vara ägare till enheten eller en användare som har minst en av följande roller tilldelade:

- Molnenhetsadministratör
- Global administratör
- Supportavdelningsadministratör
- Administratör för Intune-tjänsten
- Säkerhetsadministratör
- Säkerhetsläsare

> [!NOTE]
> Hybrid Azure AD-anslutna Windows 10-enheter har inte någon ägare. Så om du söker efter en enhet efter ägare och inte har hittat den, kan du söka efter enhets-ID.

## <a name="audit-logs"></a>Granskningsloggar

Enhets aktiviteter är tillgängliga via aktivitets loggarna. Dessa loggar innehåller aktiviteter som utlösts av registrerings tjänsten för enheter och användare:

- Skapa och lägga till ägare/användare på enheten
- Ändringar av enhets inställningar
- Enhets åtgärder som att ta bort eller uppdatera en enhet

Din start punkt till gransknings data är **gransknings loggar** i avsnittet **aktivitet** på sidan **enheter** .

![Granskningsloggar](./media/device-management-azure-portal/61.png)

En granskningslogg har en standardlistvy som visar:

- Datum och tid för förekomsten
- Målen
- Initieraren/aktören (som) för en aktivitet
- Aktiviteten (vad)

![Granskningsloggar](./media/device-management-azure-portal/63.png)

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Granskningsloggar](./media/device-management-azure-portal/64.png)

Om du vill begränsa de data som rapporteras till en nivå som passar dig kan du filtrera granskningsdata med hjälp av följande fält:

- Category
- Resurstyp för aktivitet
- Aktivitet
- Datumintervall
- Mål
- Initierad av (aktör)

Förutom filtren kan du söka efter vissa poster.

![Granskningsloggar](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Nästa steg

[Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
