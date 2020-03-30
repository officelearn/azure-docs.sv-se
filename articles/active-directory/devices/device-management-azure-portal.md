---
title: Hantera enheter med Azure-portalen | Microsoft-dokument
description: Lär dig hur du använder Azure-portalen för att hantera enheter.
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
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262248"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Hantera enhetsidentiteter med Azure-portalen

Med enhetsidentitetshantering i Azure Active Directory (Azure AD) kan du se till att användarna får åtkomst till dina resurser från enheter som uppfyller dina standarder för säkerhet och efterlevnad.

Den här artikeln:

- Förutsätter att du är bekant med [introduktionen till enhetsidentitetshantering i Azure Active Directory](overview.md)
- Ger dig information om hur du hanterar dina enhetsidentiteter med Hjälp av Azure AD-portalen

## <a name="manage-device-identities"></a>Hantera enhetsidentiteter

Azure AD-portalen ger dig en central plats för att hantera dina enhetsidentiteter. Du kan komma till denna plats genom att antingen använda en [direkt länk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) eller:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory-enheter** > **Devices**.

På sidan **Enheter** kan du:

- Konfigurera enhetsinställningarna
- Hitta enheter
- Utföra enhetsidentitetshanteringsuppgifter
- Granska enhetsrelaterade granskningsloggar  
  
## <a name="configure-device-settings"></a>Konfigurera enhetsinställningar

För att hantera dina enhetsidentiteter med Azure AD-portalen måste dina enheter antingen [registreras eller anslutas](overview.md) till Azure AD. Som administratör kan du finjustera processen med att registrera och ansluta till enheter genom att konfigurera enhetsinställningarna.

På sidan enhetsinställningar kan du konfigurera inställningar som är relaterade till enhetsidentiteter:

![Hantera en Intune-enhet](./media/device-management-azure-portal/21.png)

- **Användare kan ansluta enheter till Azure AD** - Med den här inställningen kan du välja de användare som kan registrera sina enheter som Azure AD-anslutna enheter. Standard är **Alla**.

> [!NOTE]
> **Användare kan ansluta till enheter till Azure AD-inställningen** är endast tillämplig på Azure AD-anslutning på Windows 10.

- **Ytterligare lokala administratörer på Azure AD-anslutna enheter** – Du kan välja vilka användare som beviljas lokala administratörsrättigheter på en enhet. Användare som läggs till här läggs till i rollen *Enhetsadministratörer* i Azure AD. Globala administratörer i Azure AD och enhetsägare beviljas som standard lokala administratörsrättigheter. Det här alternativet är en premiumutgåva som är tillgänglig via produkter som Azure AD Premium eller Enterprise Mobility Suite (EMS).
- **Användare kan registrera sina enheter med Azure AD** - Du måste konfigurera den här inställningen så att Windows 10-enheter för personliga, iOS, Android och MacOs kan registreras med Azure AD. Om du väljer **Ingen**får enheter inte registrera sig med Azure AD. Registrering med Microsoft Intune eller Mobile Device Management (MDM) för Office 365 kräver registrering. Om du har konfigurerat någon av dessa tjänster är **ALL** markerat och **INGEN** är inte tillgänglig.
- **Kräv MultifaktorAuth för att ansluta till enheter** – Du kan välja om användare måste ange ytterligare en autentiseringsfaktor för att ansluta till sin enhet till Azure AD. Standard är **Nej**. Vi rekommenderar att du kräver multifaktorautentisering när du registrerar en enhet. Innan du aktiverar multifaktorautentisering för den här tjänsten måste du se till att multifaktorautentisering är konfigurerad för de användare som registrerar sina enheter. Mer information om olika Azure-multifaktorautentiseringstjänster finns [i Komma igång med Azure multifaktorautentisering](../authentication/concept-mfa-whichversion.md). 

> [!NOTE]
> **Kräv multifaktorautentisering för att ansluta till** enhetsinställningen gäller för enheter som antingen är Azure AD-anslutna eller Azure AD-registrerade. Den här inställningen gäller inte hybrid Azure AD-anslutna enheter.

- **Maximalt antal enheter** - Med den här inställningen kan du välja det maximala antalet Azure AD-anslutna eller Azure AD-registrerade enheter som en användare kan ha i Azure AD. Om en användare når den här kvoten kan han eller hon inte lägga till ytterligare enheter förrän en eller flera av de befintliga enheterna har tagits bort. Standardvärdet är **20**.

> [!NOTE]
> **Maximalt antal enheter** gäller för enheter som antingen är Azure AD-anslutna eller Azure AD-registrerade. Den här inställningen gäller inte hybrid Azure AD-anslutna enheter.

- **Användare kan synkronisera inställningar och appdata mellan enheter** – Som standard är den här inställningen inställd **på NONE**. Om du väljer specifika användare eller grupper eller ALLA kan användarens inställningar och appdata synkroniseras mellan sina Windows 10-enheter. Läs mer om hur synkronisering fungerar i Windows 10.
Det här alternativet är en premiumfunktion som är tillgänglig via produkter som Azure AD Premium eller Enterprise Mobility Suite (EMS).

## <a name="locate-devices"></a>Hitta enheter

Du har två alternativ för att hitta registrerade och anslutna enheter:

- **Alla enheter** i avsnittet **Hantera** på sidan **Enheter**  
- **Enheter** i avsnittet **Hantera** på en **användarsida**

Med båda alternativen kan du komma till en vy som:

- Gör att du kan söka efter enheter med visningsnamnet eller enhets-ID som filter.
- Ger dig detaljerad översikt över registrerade och anslutna enheter
- Gör att du kan utföra vanliga enhetshanteringsuppgifter

![Alla enheter](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* Om du ser en enhet som är "Hybrid Azure AD-ansluten" med tillståndet "Väntande" under kolumnen REGISTRERAD, anger den att enheten har synkroniserats från Azure AD connect och väntar på att slutföra registreringen från klienten. Läs mer om hur du [planerar implementeringen av Hybrid Azure AD-koppling](hybrid-azuread-join-plan.md). Ytterligare information finns i artikeln, [Enheter vanliga frågor](faq.md).
>
>   ![Väntande enheter](./media/device-management-azure-portal/75.png)
>
>* För vissa iOS-enheter kan enhetsnamnen som innehåller apostrofer potentiellt använda olika tecken som ser ut som apostrofer. Så att söka efter sådana enheter är lite knepigt - om du inte ser sökresultaten korrekt, se till att söksträngen innehåller matchande apostrof karaktär.

## <a name="device-identity-management-tasks"></a>Uppgifter om hantering av enhetsidentitet

Som global administratör eller molnenhetsadministratör kan du hantera registrerade eller anslutna enheter. Intune-tjänstadministratörer kan:

- Uppdatera enheter - Exempel är dagliga åtgärder som att aktivera/inaktivera enheter
- Ta bort enheter – När en enhet har dragits tillbaka och ska tas bort i Azure AD

Det här avsnittet innehåller information om vanliga enhetsidentitetshanteringsuppgifter.

### <a name="manage-an-intune-device"></a>Hantera en Intune-enhet

Om du är Intune-administratör kan du hantera enheter som är markerade som **Microsoft Intune**. Om enheten inte är registrerad hos Microsoft Intune kommer alternativet "Hantera" att vara nedtonat.

![Hantera en Intune-enhet](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Aktivera/inaktivera en Azure AD-enhet

För att aktivera / inaktivera en enhet, har du två alternativ:

- Uppgiftsmenyn ("...") på sidan **Alla enheter**

   ![Hantera en Intune-enhet](./media/device-management-azure-portal/71.png)

- Verktygsfältet på sidan **Enheter**

   ![Hantera en Intune-enhet](./media/device-management-azure-portal/32.png)

**Anmärkningar:**

- Du måste vara en global administratör eller molnenhetsadministratör i Azure AD för att aktivera/inaktivera en enhet. 
- Om du inaktiverar en enhet förhindras att en enhet autentiseras med Azure AD, vilket förhindrar att enheten kommer åt dina Azure AD-resurser som skyddas av enhetsutfärdaren eller använder dina WH4B-autentiseringsuppgifter.
- Om du inaktiverar enheten återkallas både den primära uppdateringstoken (PRT) och eventuella uppdateringstoken (RT) på enheten.

### <a name="delete-an-azure-ad-device"></a>Ta bort en Azure AD-enhet

Om du vill ta bort en enhet har du två alternativ:

- Uppgiftsmenyn ("...") på sidan **Alla enheter**

   ![Hantera en Intune-enhet](./media/device-management-azure-portal/72.png)

- Verktygsfältet på sidan **Enheter**

   ![Ta bort en enhet](./media/device-management-azure-portal/34.png)

**Anmärkningar:**

- Du måste vara global administratör eller Intune-administratör i Azure AD för att kunna ta bort en enhet.
- Ta bort en enhet:
   - Förhindrar att en enhet kommer åt dina Azure AD-resurser.
   - Tar bort all information som är ansluten till enheten, till exempel BitLocker-nycklar för Windows-enheter.  
   - Representerar en aktivitet som inte kan återställas och rekommenderas inte om det inte krävs.

Om en enhet hanteras av en annan hanteringsmyndighet (till exempel Microsoft Intune) kontrollerar du att enheten har raderats/dragits tillbaka innan enheten tas bort i Azure AD. Granska hur [du hanterar inaktuella enheter](device-management-azure-portal.md) innan du tar bort några enheter.

### <a name="view-or-copy-device-id"></a>Visa eller kopiera enhets-ID

Du kan använda ett enhets-ID för att verifiera enhets-ID-informationen på enheten eller använda PowerShell under felsökning. Klicka på enheten om du vill komma åt kopieringsalternativet.

![Visa ett enhets-ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Visa eller kopiera BitLocker-nycklar

Du kan visa och kopiera BitLocker-nycklarna så att användarna kan återställa sin krypterade enhet. Dessa nycklar är endast tillgängliga för Windows-enheter som är krypterade och har sina nycklar lagrade i Azure AD. Du kan kopiera dessa nycklar när du öppnar information om enheten.

![Visa BitLocker-tangenter](./media/device-management-azure-portal/36.png)

Om du vill visa eller kopiera BitLocker-nycklarna måste du antingen vara ägare till enheten eller en användare som har minst en av följande roller tilldelad:

- Administratör för molnenheter
- Global administratör
- Administratör för helpdesk
- Administratör för Intune-tjänsten
- Säkerhetsadministratör
- Säkerhetsläsare

> [!NOTE]
> Hybrid Azure AD Gick med i Windows 10-enheter har ingen ägare. Så, om du letar efter en enhet efter ägare och inte hittade den, sök efter enhets-ID.

## <a name="audit-logs"></a>Granskningsloggar

Enhetsaktiviteter är tillgängliga via aktivitetsloggarna. Dessa loggar omfattar aktiviteter som utlöses av enhetsregistreringstjänsten och av användare:

- Skapande av enheter och lägger till ägare/användare på enheten
- Ändringar i enhetsinställningar
- Enhetsåtgärder som att ta bort eller uppdatera en enhet

Startpunkten till granskningsdata är **Granskningsloggar** i avsnittet **Aktivitet** på sidan **Enheter.**

Granskningsloggen har en standardlistvy som visar:

- Datum och tid för förekomsten
- Målen för
- Initiativtagaren/aktören (vem) för en aktivitet
- Aktiviteten (vad)

![Granskningsloggar](./media/device-management-azure-portal/63.png)

Du kan anpassa listvyn genom att klicka på **Kolumner** i verktygsfältet.

![Granskningsloggar](./media/device-management-azure-portal/64.png)

Om du vill begränsa de data som rapporteras till en nivå som passar dig kan du filtrera granskningsdata med hjälp av följande fält:

- Kategori
- Resurstyp för aktivitet
- Aktivitet
- Datumintervall
- Mål
- Initierad av (skådespelare)

Förutom filtren kan du söka efter specifika poster.

![Granskningsloggar](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Nästa steg

[Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
