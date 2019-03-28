---
title: Så här hanterar du enheter med hjälp av Azure-portalen | Microsoft Docs
description: Lär dig hur du använder Azure-portalen för att hantera enheter.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 516a2d3b0f81bcef1ed636be9d1da9a270cc7069
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520028"
---
# <a name="how-to-manage-devices-using-the-azure-portal"></a>Så här hanterar du enheter med hjälp av Azure-portalen


Med enhetshanteringen i Azure Active Directory (Azure AD) kan du se till att användarna har tillgång till dina resurser från enheter som uppfyller dina krav för säkerhet och kompatibilitet. 

Den här artikeln:

- Förutsätter att du är bekant med den [introduktionen till enhetshantering i Azure Active Directory](overview.md)

- Innehåller information om hur du hanterar dina enheter med hjälp av Azure portal

## <a name="manage-devices"></a>Hantera enheter 

Azure-portalen ger en central plats för att hantera dina enheter. Du kommer till denna plats genom att antingen använda en [Direktlänk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) eller genom att följa dessa instruktioner:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som administratör.

2. I det vänstra navigeringsfältet, klickar du på **Active Directory**.

    ![Konfigurera enhetsinställningar](./media/device-management-azure-portal/01.png)

3. I den **hantera** klickar du på **enheter**.

    ![Konfigurera enhetsinställningar](./media/device-management-azure-portal/74.png)
 
Den **enheter** sidan kan du:

- Konfigurera inställningarna för enheten

- Hitta enheter

- Utföra hanteringsaktiviteter för enhet

- Granska relaterade för enhetshantering granskningsloggar  
  

## <a name="configure-device-settings"></a>Konfigurera enhetsinställningar

För att hantera dina enheter med hjälp av Azure portal, dina enheter måste vara antingen [registrerats för eller anslutits](overview.md#getting-devices-under-the-control-of-azure-ad) till Azure AD. Du kan finjustera processen med att registrera och ansluta enheter genom att konfigurera inställningar för enheter som en administratör. 

![Konfigurera enhetsinställningar](./media/device-management-azure-portal/22.png)

Inställningssidan för enheten kan du konfigurera:

![Hantera en Intune-enhet](./media/device-management-azure-portal/21.png)


- **Användare kan ansluta enheter till Azure AD** – den här inställningen kan du välja vilka användare som kan [ansluta enheter](overview.md#azure-ad-joined-devices) till Azure AD. Standardvärdet är **alla**. Den här inställningen gäller bara för Azure AD Join i Windows 10.

- **Ytterligare lokala administratörer på Azure AD-anslutna enheter** – du kan välja de användare som har beviljats behörighet som lokal administratör på en enhet. Användare som läggs till här läggs till i *Enhetsadministratörer* roll i Azure AD. Globala administratörer i Azure AD och enhetsägare beviljas lokal administratörsbehörighet som standard. Det här alternativet är en premium edition funktioner som är tillgängliga via produkter, till exempel Azure AD Premium eller Enterprise Mobility Suite (EMS). 

- **Användare kan registrera sina enheter med Azure AD** -måste du konfigurera den här inställningen för att tillåta enheter att bli [registrerad](overview.md#azure-ad-registered-devices) med Azure AD. Om du väljer **ingen**, enheter kan inte registrera när de inte är ansluten till Azure AD eller hybrid Azure AD-anslutna. Registrering med Microsoft Intune eller Mobile Device Management (MDM) för Office 365 kräver registrering. Om du har konfigurerat någon av dessa tjänster **alla** har valts och **NONE** är inte tillgänglig.

- **Kräver Multi-Factor Authentication att ansluta enheter** – du kan välja om användare måste ange en andra autentiseringsfaktor till [join](overview.md#azure-ad-joined-devices) sin enhet till Azure AD. Standardvärdet är **nr**. Vi rekommenderar att kräva multifaktorautentisering när du registrerar en enhet. Innan du aktiverar multifaktorautentisering för den här tjänsten måste du se till att multifaktorautentisering har konfigurerats för de användare som registrerar sina enheter. Mer information om olika Azure Multi-Factor authentication-tjänster finns i [komma igång med Azure Multi-Factor authentication](../authentication/concept-mfa-whichversion.md). Den här inställningen påverkar inte hybrid-anslutning för Windows 10 eller Windows 7. Detta gäller endast för Azure AD Join på Windows 10- och BYOD-enhetsregistrering för Windows 10, iOS och Android. 

- **Högsta antalet enheter** – den här inställningen kan du välja det maximala antalet enheter som en användare kan ha i Azure AD. Om en användare når den här kvoten kan de att inte lägga till ytterligare enheter förrän en eller flera av de befintliga enheterna har tagits bort. Enheten kvoten räknas för alla enheter som är ansluten till Azure AD eller Azure AD-registrerad idag. Standardvärdet är **20**.

- **Användarna kan synkronisera inställningar och AppData på enheter** -som standard är inställningen **NONE**. Välja specifika användare eller grupper eller alla kan användarens inställningar och AppData att synkronisera via sina Windows 10-enheter. Läs mer om hur synkroniseringen fungerar i Windows 10.
Det här alternativet är en premium-funktion som är tillgängliga via produkter som Azure AD Premium eller Enterprise Mobility Suite (EMS).





## <a name="locate-devices"></a>Hitta enheter

Du har två alternativ för att hitta registrerade och anslutna enheter:

- **Alla enheter** i den **hantera** delen av den **enheter** sidan  

    ![Alla enheter](./media/device-management-azure-portal/41.png)


- **Enheter** i den **hantera** delen av en **användaren** sidan
 
    ![Alla enheter](./media/device-management-azure-portal/43.png)



Med båda alternativen kommer du till en vy som:


- Kan du söka efter enheter med hjälp av visningsnamnet som filter.

- Ger dig detaljerad översikt över registrerade och anslutna enheter

- Gör att du kan utföra vanliga hanteringsuppgifter för enhet
   

![Alla enheter](./media/device-management-azure-portal/51.png)

Enhetsnamn som innehåller apostrofer kan potentiellt använda olika tecken som ser ut som apostrofer för vissa iOS-enheter. Söker efter sådana enheter är lite svårt – om du inte ser resultat från korrekt, kontrollera att strängen innehåller matchande apostroftecknet.

## <a name="device-management-tasks"></a>Enhetens hanteringsuppgifter

Som global administratör eller molnenhetsadministratör kan hantera du registrerade eller domänanslutna enheter. Intune Service-administratörer kan:
 
- Uppdatera enheter – exempel är dagliga åtgärder som till exempel aktivering/inaktivering av enheter

- Ta bort enheter – när en enhet dras tillbaka och bör tas bort i Azure AD

Det här avsnittet finns information om vanliga hanteringsuppgifter för enheten.



### <a name="manage-an-intune-device"></a>Hantera en Intune-enhet

Om du är Intune-administratör kan du hantera enheter som har markerats som **Microsoft Intune**. 

![Hantera en Intune-enhet](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Aktivera / inaktivera en Azure AD-enhet

Om du vill aktivera / inaktivera en enhet, har du två alternativ:

- Menyn uppgifter (”...”) på den **alla enheter** sidan

    ![Hantera en Intune-enhet](./media/device-management-azure-portal/71.png)

- I verktygsfältet på den **enheter** sidan

    ![Hantera en Intune-enhet](./media/device-management-azure-portal/32.png)


**Anmärkning:**

- Du måste vara en global administratör eller cloud enhetsadministratör i Azure AD för att aktivera / inaktivera en enhet. 
- Inaktiverar en enhet förhindrar att en enhet autentisering med Azure AD, vilket förhindrar enheten från åtkomst till din Azure AD-resurser som är skyddad av enheten CA: N eller med dina autentiseringsuppgifter för WH4B. 



### <a name="delete-an-azure-ad-device"></a>Ta bort en Azure AD-enhet

Om du vill ta bort en enhet, har du två alternativ:

- Menyn uppgifter (”...”) på den **alla enheter** sidan

    ![Hantera en Intune-enhet](./media/device-management-azure-portal/72.png)

- I verktygsfältet på den **enheter** sidan

    ![Ta bort en enhet](./media/device-management-azure-portal/34.png)


**Anmärkning:**

- Du måste vara en global administratör eller Intune-administratör i Azure AD för att ta bort en enhet.

- Tar bort en enhet:
 
    - Förhindrar att en enhet från att komma åt din Azure AD-resurser. 

    - Tar bort all information som är kopplade till enheten, till exempel, BitLocker-nycklar för Windows-enheter.  

    - Representerar en återställningsbar aktivitet och rekommenderas inte om det inte krävs.

Om en enhet hanteras av en annan utfärdare för hantering av (till exempel Microsoft Intune), kontrollera att enheten har rensats / tas ur drift innan du tar bort enheten i Azure AD.

 


### <a name="view-or-copy-device-id"></a>Visa eller kopiera enhets-ID

Du kan använda ett enhets-ID för att verifiera ID enhetsinformation på enheten eller med hjälp av PowerShell under felsökningen. För att komma åt alternativet Kopiera, klickar du på enheten.

![Visa ett enhets-ID](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Visa eller kopiera BitLocker-nycklar

Du kan visa och kopiera BitLocker-nycklar för att hjälpa användare att återställa sina krypterade enheten. De här nycklarna är bara tillgängliga för Windows-enheter som är krypterade och har sina nycklar som lagras i Azure AD. Du kan kopiera nycklarna vid åtkomst till information om enheten.
 
![Visa BitLocker-nycklar](./media/device-management-azure-portal/36.png)

Om du vill visa eller kopiera BitLocker-nycklar, måste du vara ägaren till enheten eller en användare som har minst en av följande roller:

- Globala administratörer
- Supportadministratörer
- Säkerhetsadministratörer
- Säkerhetsläsare
- Intune-tjänstadministratörer

> [!NOTE]
> Hybrid Azure AD-anslutna Windows 10-enheter har inte någon ägare. Så om du letar efter en enhet av ägare och gick inte att hitta den, Sök efter enhets-ID.


## <a name="audit-logs"></a>Granskningsloggar


Enheten aktiviteter är tillgängliga via aktivitetsloggar. Detta omfattar aktiviteter som utlöses av registreringstjänsten för enheter och användare:

- Skapa en enhet och lägga till ägare / användare på enheten

- Ändringar av inställningar för enheter

- Åtgärder, till exempel ta bort eller uppdatera en enhet
 
Din startpunkt för granskningsdata som är **granskningsloggar** i den **aktivitet** delen av den **enheter** sidan.

![Granskningsloggar](./media/device-management-azure-portal/61.png)


En granskningslogg har en standardlistvy som visar:

- Datum och tid för händelsen

- Den är riktad mot

- Initieraren / aktören (vem) för en aktivitet

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
- Initierad av (aktör)

Förutom filter, kan du söka efter specifika poster.

![Granskningsloggar](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till enhetshantering i Azure Active Directory](overview.md)



