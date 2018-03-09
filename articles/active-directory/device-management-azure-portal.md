---
title: "Hantera enheter med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du använder Azure-portalen för att hantera enheter."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 85447809cbc803c13ad731b5328e94a1b03abba8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="managing-devices-using-the-azure-portal"></a>Hantera enheter med hjälp av Azure portal


Med hantering av enheter i Azure Active Directory (Azure AD), kan du se till att dina användare har åtkomst till dina resurser från enheter som uppfyller dina krav för säkerhet och efterlevnad. 

Det här avsnittet:

- Förutsätter att du är bekant med den [introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)

- Innehåller information om hur du hanterar dina enheter med hjälp av Azure portal

## <a name="manage-devices"></a>Hantera enheter 

Azure-portalen ger dig en central plats för att hantera dina enheter. Du kommer till denna plats genom att antingen använda en [Direktlänk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) eller genom att följa de manuella stegen:

1. Rör sig till den [Azure-portalen](https://portal.azure.com) som administratör.

2. Klicka på den vänstra navigeringsfält för, **Active Directory**.

    ![Konfigurera inställningar för enheter](./media/device-management-azure-portal/01.png)

3. I den **hantera** klickar du på **enheter**.

    ![Konfigurera inställningar för enheter](./media/device-management-azure-portal/11.png)
 
Den **enheter** sidan kan du:

- Konfigurera inställningarna för enheten

- Hitta enheter

- Utföra hanteringsaktiviteter för enhet

- Granska enhetshantering relaterade granskningsloggar  
  

## <a name="configure-device-settings"></a>Konfigurera inställningar för enheter

Om du vill hantera enheter med Azure-portalen kan dina enheter måste vara antingen [registrerad eller anslutna](device-management-introduction.md#getting-devices-under-the-control-of-azure-ad) till Azure AD. Du kan finjustera registrera och ansluta enheter genom att konfigurera inställningar för enheter som en administratör. 

![Konfigurera inställningar för enheter](./media/device-management-azure-portal/22.png)

Inställningssidan kan du konfigurera:

![Hantera en enhet i Intune](./media/device-management-azure-portal/21.png)


- **Användare kan ansluta enheter till Azure AD** -den här inställningen kan du välja vilka användare som kan [ansluta enheter](device-management-introduction.md#azure-ad-joined-devices) till Azure AD. Standardvärdet är **alla**.

- **Ytterligare lokala administratörer på Azure AD-anslutna enheter** -du kan välja de användare som beviljas lokal administratörsbehörighet på en enhet. Användare som läggs till här läggs till i *Enhetsadministratörer* roll i Azure AD. Globala administratörer i Azure AD och enhetsägare beviljas lokal administratörsbehörighet som standard. Det här alternativet är en premium edition funktioner som är tillgängliga via produkter, till exempel Azure AD Premium eller Enterprise Mobility Suite (EMS). 

- **Användarna kan registrera sina enheter med Azure AD** -måste du konfigurera den här inställningen om du vill tillåta att enheterna ska [registrerade](device-management-introduction.md#azure-ad-registered-devices) med Azure AD. Om du väljer **ingen**, enheter tillåts inte att registrera om de inte Azure AD ansluten eller hybrid anslutna Azure AD. Registrering med Microsoft Intune eller hantering av mobilenheter (MDM) för Office 365 kräver registrering. Om du har konfigurerat någon av dessa tjänster **alla** är markerad och **NONE** är inte tillgänglig...

- **Kräva Multifaktorautentisering ansluta enheter** -du kan välja om användare måste ange en andra autentiseringsfaktor till [koppling](device-management-introduction.md#azure-ad-joined-devices) sin enhet till Azure AD. Standardvärdet är **nr**. Vi rekommenderar att kräva multifaktorautentisering vid registrering av en enhet. Innan du aktiverar multifaktorautentisering för den här tjänsten måste du kontrollera att multifaktorautentisering har konfigurerats för de användare som registrerar sina enheter. Mer information om olika Azure Multi-Factor authentication-tjänster finns [komma igång med Azure Multi-Factor authentication](../multi-factor-authentication/multi-factor-authentication-get-started.md). 

- **Högsta antal enheter** -den här inställningen kan du välja det högsta antalet enheter som en användare kan ha i Azure AD. Om användarna når den här kvoten, de är inte lägga till fler enheter förrän ett eller flera av de befintliga enheterna har tagits bort. Enheten citattecken räknas för alla enheter som är Azure AD ansluten eller Azure AD som registrerade idag. Standardvärdet är **20**.

- **Användarna kan synkronisera AppData och inställningar mellan enheter** -som standard är inställningen **NONE**. Välja specifika användare eller grupper eller alla kan användarens inställningar och AppData att synkronisera via sina Windows 10-enheter. Mer information om hur synkroniseringen fungerar i Windows 10.
Det här alternativet är en premium-funktioner som är tillgängliga via produkter, till exempel Azure AD Premium eller Enterprise Mobility Suite (EMS).
 




## <a name="locate-devices"></a>Hitta enheter

Du har två alternativ för att hitta registrerade och domänanslutna enheter:

- **Alla enheter** i den **hantera** avsnitt i den **enheter** sidan  

    ![Alla enheter](./media/device-management-azure-portal/41.png)


- **Enheter** i den **hantera** avsnitt i en **användaren** sidan
 
    ![Alla enheter](./media/device-management-azure-portal/43.png)



Med båda alternativen du en vy som:


- Kan du söka efter enheter med visningsnamnet som filter.

- Ger detaljerad översikt över registrerade och domänanslutna enheter

- Du kan utföra vanliga hanteringsuppgifter för enhet
   

![Alla enheter](./media/device-management-azure-portal/51.png)

Enhetsnamn som innehåller apostrofer kan potentiellt använda olika tecken som ser ut som apostrofer för vissa iOS-enheter. Så söker efter sådana enheter är lite komplicerade - om du inte ser sökresultat korrekt, kontrollera att strängen innehåller matchande apostroftecknet.

## <a name="device-management-tasks"></a>Enhetens hanteringsuppgifter

Som administratör kan hantera du registrerade eller anslutna enheter. Det här avsnittet ger information om vanliga hanteringsuppgifter för enheten.


### <a name="manage-an-intune-device"></a>Hantera en enhet i Intune

Om du är administratör Intune kan du hantera enheter som har markerats som **Microsoft Intune**. En administratör kan se ytterligare enheter 

![Hantera en enhet i Intune](./media/device-management-azure-portal/31.png)


### <a name="enable--disable-an-azure-ad-device"></a>Aktivera / inaktivera en enhet i Azure AD

Om du vill aktivera / inaktivera en enhet, har du två alternativ:

- Menyn uppgifter (”...”) på den **alla enheter** sidan

    ![Hantera en enhet i Intune](./media/device-management-azure-portal/71.png)

- I verktygsfältet på den **enheter** sidan

    ![Hantera en enhet i Intune](./media/device-management-azure-portal/32.png)


**Anmärkning:**

- Du måste vara en global administratör i Azure AD för att aktivera / inaktivera en enhet. 
- Inaktivera en enhet förhindrar att en enhet från att komma åt resurserna i Azure AD. 



### <a name="delete-an-azure-ad-device"></a>Ta bort en Azure AD-enhet

Om du vill ta bort en enhet har du två alternativ:

- Menyn uppgifter (”...”) på den **alla enheter** sidan

    ![Hantera en enhet i Intune](./media/device-management-azure-portal/72.png)

- I verktygsfältet på den **enheter** sidan

    ![Ta bort en enhet](./media/device-management-azure-portal/34.png)


**Anmärkning:**

- Du måste vara en global administratör i Azure AD för att ta bort en enhet.  

- Tar bort en enhet:
 
    - Förhindrar att en enhet från att komma åt resurserna i Azure AD. 

    - Tar bort all information som är kopplade till enheten, till exempel, BitLocker-nycklar för Windows-enheter.  

    - Representerar en oåterkalleligt aktivitet och rekommenderas inte om det inte krävs.

Om en enhet hanteras av en annan hanterare (t.ex, Microsoft Intune), kontrollera att enheten har rensats / dragits tillbaka innan du tar bort enheten i Azure AD.

 


### <a name="view-or-copy-device-id"></a>Visa eller kopiera enhets-ID

Du kan använda enhets-ID för att verifiera ID enhetsinformation på enheten eller med hjälp av PowerShell vid felsökning. Alternativet Kopiera, klicka på enheten.

![Visa en enhets-ID](./media/device-management-azure-portal/35.png)
  

### <a name="view-or-copy-bitlocker-keys"></a>Visa eller kopiera BitLocker-nycklar

Om du är administratör kan du visa och kopiera BitLocker-nycklar för att hjälpa användare att återställa den krypterade enheten. Nycklarna är bara tillgängliga för Windows-enheter som är krypterade och har sina nycklar lagras i Azure AD. Du kan kopiera dessa nycklar vid åtkomst till information om enheten.
 
![Visa BitLocker-nycklar](./media/device-management-azure-portal/36.png)



## <a name="audit-logs"></a>Granskningsloggar


Enheten aktiviteter är tillgängliga via aktivitetsloggarna. Detta omfattar aktiviteter som utlöses av registreringstjänsten för enheter och användare:

- Skapa en enhet och lägga till ägare / användare på enheten

- Ändringar av inställningar för enheter

- Enheten åtgärder, till exempel ta bort eller uppdatera en enhet
 
Är din inkörsport till att granska data **granskningsloggar** i den **aktiviteten** avsnitt i den **enheter** sidan.

![Granskningsloggar](./media/device-management-azure-portal/61.png)


En granskningslogg har en standardlistvy som visar:

- datum och tid för

- mål

- initieraren / aktören (som) för en aktivitet

- aktiviteten (vad)

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

Utöver filter kan du söka efter specifika poster.

![Granskningsloggar](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till hantering av enheter i Azure Active Directory](device-management-introduction.md)



